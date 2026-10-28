# MVP — Modelos, reglas, relaciones e invariantes

Diseño de dominio mínimo para el MVP. Sin código, sin bounded contexts extra y sin infraestructura.

El orden de implementación prioriza:

Customer → Loan → Payment

Este documento fija el modelo de negocio, las reglas, las relaciones y las invariantes. Los casos de uso técnicos, el diseño de persistencia y el código vendrán después.

## 1. Alcance

### En alcance

| Concepto | Rol |
|---|---|
| Customer | Persona que puede tener un crédito |
| Loan | Crédito desembolsado, con condiciones, saldo y cronograma |
| Installment | Cuota del préstamo. Forma parte de Loan; no es un modelo independiente |
| Payment | Dinero recibido e aplicado al préstamo |

### Fuera del MVP

No se modelan ahora:

- Solicitud de crédito
- Motor de riesgo
- Revisión manual
- Identity / IAM como dominio
- Auditoría inmutable
- Notificaciones
- Outbox / CDC
- Read models
- Mora avanzada y penalidades
- KYC real, bancos y pasarelas

### Capacidad que debe quedar verdadera

Un cliente activo recibe un préstamo con cronograma francés. Puede registrar pagos idempotentes. El saldo y las cuotas se actualizan de forma consistente. El préstamo termina en `PAID_OFF` cuando no queda nada por cobrar.

## 2. Relaciones

```text
Customer 1 ──────────── * Loan
                          │
                          │ 1
                          │
                          * Installment
                          │
Loan 1 ──────────────── * Payment
```

Cardinalidad:

- Un Customer tiene 0..N Loan.
- Un Loan pertenece a exactamente un Customer y no cambia de titular.
- Un Loan tiene 1..N Installment. N es el plazo en periodos.
- Un Payment pertenece a exactamente un Loan.
- Un Payment puede cubrir una o varias cuotas (aplicación FIFO).
- Una cuota puede recibir cero o más aplicaciones de pago (pago parcial).

```mermaid
erDiagram
    CUSTOMER ||--o{ LOAN : owns
    LOAN ||--|{ INSTALLMENT : schedules
    LOAN ||--o{ PAYMENT : receives
    PAYMENT }o--o{ INSTALLMENT : applies_to

    CUSTOMER {
        id customerId
        document unique
        status ACTIVE_INACTIVE
    }
    LOAN {
        id loanId
        customerId FK
        status CREATED_ACTIVE_PAID_OFF_CANCELLED
        principal Money
        outstandingBalance Money
    }
    INSTALLMENT {
        number period
        dueDate date
        status PENDING_PARTIAL_PAID_OVERDUE
        outstanding Money
    }
    PAYMENT {
        id paymentId
        loanId FK
        requestId unique
        amount Money
    }
```

## 3. Agregados

Tres agregados. Installment no es agregado: vive dentro de Loan porque cuota y saldo deben cambiar juntos.

| Agregado | Raíz | Contiene | Consistencia |
|---|---|---|---|
| Customer | Customer | Datos de identidad y perfil mínimo | Unicidad y estado del cliente |
| Loan | Loan | Condiciones, saldo, cuotas | Cronograma, saldo y estados de cuotas |
| Payment | Payment | Importe, origen, idempotencia | Este cobro existe una sola vez |

RegisterPayment cruza Loan y Payment. Se registran juntos en una sola transacción de negocio. El dominio no deja un pago sin aplicación ni una cuota cobrada sin pago.

## 4. Customer

### Datos mínimos

- `customerId`
- Documento (`tipo` + `número`): identidad de negocio
- Nombre
- Contacto (email y/o teléfono)
- Estado: `ACTIVE` | `INACTIVE`
- Fecha de registro

El perfil laboral y financiero no es obligatorio en este MVP. Hará falta cuando exista evaluación de riesgo.

### Estados

```text
ACTIVE ⇄ INACTIVE
```

El alta siempre ocurre en `ACTIVE`.

### Reglas

1. El documento es obligatorio y único en el sistema.
2. Nombre y al menos un medio de contacto son obligatorios.
3. Solo un cliente `ACTIVE` puede recibir un préstamo nuevo.
4. No se desactiva un cliente con préstamo `ACTIVE`.
5. El `customerId` no se reutiliza.

### Invariantes

- `I-C1` Un documento identifica a un solo Customer.
- `I-C2` `status ∈ {ACTIVE, INACTIVE}`.
- `I-C3` Si existe un Loan `ACTIVE` del cliente, el cliente está `ACTIVE`.

## 5. Loan

### Datos

- `loanId`
- `customerId` (inmutable)
- Condiciones (inmutables tras activar):
  - principal (`Money` > 0)
  - tasa de interés
  - plazo (periodos ≥ 1)
  - frecuencia de pago
  - fecha de inicio / desembolso
  - moneda
- `outstandingBalance` (≥ 0)
- `status`
- Cronograma: lista ordenada de cuotas 1..N

### Estados y transiciones

```text
CREATED ──► ACTIVE ──► PAID_OFF
    │
    └──► CANCELLED

No hay otras transiciones.
```

| De | A | Cuándo |
|---|---|---|
| — | `CREATED` | Se origina el préstamo con condiciones y cronograma, sin desembolso |
| `CREATED` | `ACTIVE` | Desembolso. El cliente debe estar `ACTIVE`. El cronograma ya existe |
| `CREATED` | `CANCELLED` | Nunca se desembolsó |
| `ACTIVE` | `PAID_OFF` | `outstandingBalance = 0` y todas las cuotas `PAID` |
| `ACTIVE` | `CANCELLED` | Prohibido |
| `PAID_OFF` / `CANCELLED` | cualquier otro | Prohibido |

`DEFAULTED` queda fuera del MVP. Las cuotas pueden estar `OVERDUE` sin cambiar el estado del préstamo.

### Cuota (Installment)

- Número de periodo (1..N, único en el préstamo)
- Fecha de vencimiento
- Capital de la cuota, interés de la cuota, total de la cuota
- `paidAmount`
- `outstanding` = total − pagado
- Estado: `PENDING` | `PARTIALLY_PAID` | `PAID` | `OVERDUE`

`OVERDUE` significa que la cuota no está `PAID` y la fecha de vencimiento ya pasó. Es un estado derivado; no se asigna a mano.

### Reglas

1. Un préstamo tiene un único titular y no se transfiere.
2. Principal, tasa, plazo, frecuencia y moneda son válidos y coherentes entre sí.
3. Amortización francesa al crear el cronograma: N cuotas, numeradas, fechas según frecuencia.
4. La suma de capital del cronograma es igual al principal, tras la política de redondeo.
5. El desembolso no crea dinero suelto. Al pasar a `ACTIVE`, el saldo inicial es el total aún no cobrado del cronograma.
6. Solo un préstamo `ACTIVE` acepta pagos.
7. Como máximo un préstamo `ACTIVE` por cliente.
8. No se editan condiciones ni cronograma en `ACTIVE` o `PAID_OFF`.
9. `PAID_OFF` solo si el saldo es 0 y todas las cuotas están `PAID`.
10. Los importes se expresan como `Money` (sin punto flotante). El redondeo es explícito y único. Ejemplo: half-up a 2 decimales; la última cuota ajusta el residuo.

### Invariantes

- `I-L1` `customerId` existe y, al activar, el cliente está `ACTIVE`.
- `I-L2` Las transiciones son solo las de la tabla.
- `I-L3` `count(installments) = plazo`.
- `I-L4` Periodos 1..N consecutivos, sin huecos.
- `I-L5` `outstandingBalance = suma(installment.outstanding)`.
- `I-L6` `status = PAID_OFF` si y solo si saldo 0 y toda cuota `PAID`.
- `I-L7` `status = ACTIVE` implica que el préstamo aún no está cerrado.
- `I-L8` Ningún importe negativo. En cada cuota, `paidAmount ≤ total`.
- `I-L9` Un cliente tiene como máximo un Loan `ACTIVE`.
- `I-L10` La moneda del préstamo es la moneda de todas las cuotas.

## 6. Payment

### Datos

- `paymentId`
- `loanId`
- `paymentRequestId` (idempotencia de negocio)
- `amount` (`Money` > 0)
- `paidAt`
- `status`: al persistir queda `REGISTERED` e inmutable
- Desglose de aplicación (qué cuota, cuánto): derivado al aplicar, no editable

### Estados

```text
(no existe) ──► REGISTERED
```

No hay anulación ni estorno en el MVP.

### Reglas

1. Solo contra un Loan `ACTIVE`.
2. Misma moneda que el préstamo.
3. `amount > 0`.
4. `amount ≤ outstandingBalance`. El exceso se rechaza. No hay saldo a favor.
5. Aplicación FIFO por fecha de vencimiento / número de cuota, solo cuotas no `PAID`.
6. Cubre cuotas en orden: primero completa `outstanding` de la más antigua; el resto sigue.
7. Una cuota puede quedar `PARTIALLY_PAID`.
8. Si tras aplicar el saldo es 0, el Loan pasa a `PAID_OFF` en la misma operación.
9. Idempotencia: el mismo `paymentRequestId` no crea un segundo cobro. La repetición se trata como el pago ya existente.
10. Un pago registrado no se modifica ni se borra.

### Invariantes

- `I-P1` `paymentRequestId` es único en el sistema.
- `I-P2` `amount > 0` y la moneda es la del Loan.
- `I-P3` La suma aplicada a cuotas es igual a `amount`.
- `I-P4` No existe Payment huérfano. Siempre hay un `loanId` válido.
- `I-P5` Tras registrar, `I-L5` e `I-L6` del préstamo siguen cumpliéndose.
- `I-P6` Un Payment `REGISTERED` es inmutable.

## 7. Invariantes cruzados

1. Titularidad: todo Loan apunta a un Customer existente; todo Payment a un Loan existente.
2. Un activo a la vez: no hay dos Loan `ACTIVE` del mismo cliente.
3. Cliente operable: no hay Loan `ACTIVE` de un cliente `INACTIVE`.
4. Conservación de dinero: dinero cobrado = reducción de saldo = reducción de `outstanding` de cuotas.
5. Cierre coherente: `PAID_OFF` solo cuando no queda nada por cobrar.
6. Idempotencia del cobro: un `paymentRequestId` produce un solo efecto económico.
7. Máquina de estados cerrada: nadie asigna estados arbitrariamente. Solo operaciones de negocio (activar, cancelar, registrar pago).

## 8. Operaciones de negocio

Aún no son casos de uso técnicos. Son las operaciones que el modelo debe permitir.

| Operación | Sobre | Efecto |
|---|---|---|
| Registrar cliente | Customer | Alta `ACTIVE` |
| Actualizar datos de contacto | Customer | No cambia documento ni id |
| Desactivar cliente | Customer | Solo sin préstamo `ACTIVE` |
| Crear préstamo | Loan | `CREATED` + cronograma francés |
| Desembolsar / activar | Loan | `CREATED` → `ACTIVE` |
| Cancelar préstamo no desembolsado | Loan | `CREATED` → `CANCELLED` |
| Registrar pago | Payment + Loan | Pago + cuotas + saldo. `PAID_OFF` si corresponde |
| Consultar préstamo y cronograma | lectura | No muta |

Simular cuota o cronograma puede ser un cálculo que reutiliza las mismas reglas francesas, sin persistir un agregado Simulation.

## 9. Value objects conceptuales

Sin tipos de código. Evitan primitivos sueltos:

- Document: tipo + número
- Money: importe + moneda
- InterestRate: tasa en la unidad acordada
- Term: número de periodos
- PaymentFrequency: mensual, quincenal, u otra frecuencia definida
- LoanConditions: principal, tasa, plazo, frecuencia, fecha de inicio, moneda
- PaymentRequestId: clave de idempotencia

## 10. Qué se pospone

| Concepto | Motivo |
|---|---|
| Credit Application / Risk | El MVP origina el Loan directamente |
| Varios préstamos activos | Simplifica cartera y cobros |
| Estornos, pagos en exceso, mora con penalidad | Cambian invariantes de dinero |
| `DEFAULTED` | Requiere política de cobranzas |
| Eventos, Outbox, auditoría | Aún no hay consumidores ni requisito de slice |
| Usuario / rol | No hace falta para que el ciclo cliente–préstamo–pago sea verdadero |

Cuando existan solicitud y riesgo, el Loan no se inventará a mano: nacerá de una aprobación. Hasta entonces, crear y activar préstamo es la operación válida del MVP.

## 11. Criterio de verdad

El modelo está completo cuando se puede afirmar, sin infraestructura:

1. No hay dos clientes con el mismo documento.
2. No hay préstamo activo de un cliente inactivo.
3. No hay dos préstamos activos del mismo cliente.
4. El cronograma es francés, completo y cuadra con el principal, con redondeo definido.
5. Un pago duplicado no cobra dos veces.
6. Cada cobro baja el saldo exactamente en el importe aplicado.
7. Cuotas y préstamo no pueden quedar en un estado ilegal.
8. Al pagar el último saldo, el préstamo queda `PAID_OFF` y no acepta más pagos.
