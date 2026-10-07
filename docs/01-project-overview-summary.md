# MicroCredit — Enterprise Lending Platform

Sistema empresarial de microcréditos orientado a demostrar arquitectura de software, diseño de dominio, procesamiento transaccional, comunicación asíncrona y despliegue cloud-native.

## 1. Descripción
MicroCredit es una plataforma empresarial de gestión de microcréditos que cubre el ciclo completo de un crédito: registro del cliente, simulación, solicitud, evaluación automatizada de riesgo, revisión manual, aprobación, desembolso, generación del cronograma, gestión de pagos y seguimiento de cartera.

El proyecto está diseñado como una plataforma orientada a producción y servirá como proyecto integrador para demostrar conocimientos de:

- Domain-Driven Design (DDD)
- Arquitectura modular
- Desarrollo backend con NestJS
- Desarrollo frontend con Angular
- PostgreSQL
- Transactional Outbox Pattern
- Event-Driven Architecture
- Change Data Capture (CDC)
- Debezium
- AWS SNS/SQS
- AWS Lambda
- DynamoDB Single Table Design
- IAM y principio de mínimo privilegio
- AWS Secrets Manager
- Docker
- Kubernetes
- Horizontal Pod Autoscaling
- Observabilidad
- Testing
- Seguridad
- Auditoría y trazabilidad

## 2. Objetivo del proyecto
Construir una plataforma de lending que simule un entorno empresarial real, donde las operaciones financieras críticas requieran consistencia transaccional y los procesos secundarios puedan ejecutarse de forma asíncrona y desacoplada.

El proyecto deberá demostrar que las decisiones tecnológicas responden a necesidades concretas del dominio y no únicamente al uso de determinadas herramientas.

La arquitectura deberá evolucionar progresivamente desde un núcleo transaccional modular hacia una plataforma distribuida y orientada a eventos.

## 3. Problema de negocio
Una entidad financiera necesita gestionar solicitudes de microcrédito de manera rápida, consistente, segura y trazable.

El sistema debe permitir:
- Registrar y gestionar clientes.
- Simular diferentes escenarios de crédito.
- Recibir solicitudes de préstamo.
- Evaluar automáticamente el riesgo.
- Derivar casos complejos a revisión manual.
- Aprobar o rechazar solicitudes.
- Desembolsar créditos aprobados.
- Generar cronogramas de pago.
- Registrar y aplicar pagos.
- Gestionar cuotas vencidas y mora.
- Mantener una auditoría completa de operaciones relevantes.
- Proporcionar información optimizada para dashboards y consultas.
- Procesar eventos de negocio de forma asíncrona.
- Mantener separación entre el modelo transaccional y los modelos de lectura.

## 4. Objetivos técnicos

### 4.1 Arquitectura
- Aplicar Domain-Driven Design.
- Mantener separación entre dominio, aplicación e infraestructura.
- Utilizar un monorepo administrado con Nx.
- Mantener límites claros entre módulos.
- Evitar acoplamiento innecesario entre el dominio y frameworks externos.

### 4.2 Persistencia
- Utilizar PostgreSQL como fuente principal de verdad transaccional.
- Implementar transacciones ACID.
- Utilizar locking cuando sea necesario.
- Garantizar idempotencia en operaciones críticas.
- Implementar un Transactional Outbox.

### 4.3 Procesamiento asíncrono
- Publicar eventos de dominio mediante Outbox.
- Capturar cambios mediante CDC.
- Utilizar Debezium como mecanismo de Change Data Capture.
- Distribuir eventos mediante AWS SNS/SQS.
- Implementar consumidores desacoplados.

### 4.4 Read Models
- Utilizar DynamoDB para modelos de lectura específicos.
- Diseñar una Single Table orientada a patrones de acceso.
- Actualizar las proyecciones mediante eventos.
- Aplicar conceptos de CQRS cuando resulte apropiado.

### 4.5 Serverless
- Utilizar AWS Lambda para determinados consumidores y tareas asíncronas.
- Aplicar IAM con mínimo privilegio.
- Gestionar secretos mediante AWS Secrets Manager.

### 4.6 Frontend
- Construir una aplicación Angular moderna.
- Utilizar Angular Signals.
- Utilizar arquitectura Zoneless.
- Aplicar carga diferida mediante `@defer`.
- Diseñar dashboards orientados a rendimiento.

### 4.7 Infraestructura
- Crear imágenes Docker multi-stage.
- Ejecutar los servicios mediante Kubernetes.
- Configurar requests y limits.
- Implementar liveness/readiness probes.
- Configurar Horizontal Pod Autoscaler.
- Mantener configuraciones separadas por entorno.

## 5. Actores

### 5.1 Cliente
El cliente puede:
- Registrarse y autenticarse.
- Gestionar su información y completar perfil socioeconómico, laboral y financiero.
- Simular un crédito y solicitarlo.
- Adjuntar documentación.
- Consultar el estado de su solicitud, crédito activo y cronograma.
- Registrar pagos simulados e historial.

### 5.2 Analista de riesgos
El analista puede:
- Consultar solicitudes pendientes, información financiera y resultado del motor de riesgo con sus factores.
- Aprobar solicitudes enviadas a revisión manual o rechazarlas.
- Registrar observaciones y consultar auditoría relacionada.

### 5.3 Administrador
El administrador puede:
- Gestionar usuarios y roles.
- Configurar parámetros de riesgo, tasas y parámetros operativos.
- Consultar auditoría, métricas e información de cartera.

## 6. Ciclo de vida del crédito
```text
CUSTOMER
   │
   ▼
CREDIT_APPLICATION_CREATED
   │
   ▼
UNDER_REVIEW
   │
   ▼
RISK_ASSESSMENT
   │
   ├───────────────┐
   │               │
   ▼               ▼
APPROVED       MANUAL_REVIEW
   │               │
   │          ┌────┴────┐
   │          ▼         ▼
   │       APPROVED  REJECTED
   │          │
   └──────────┘
          │
          ▼
      DISBURSED
          │
          ▼
        ACTIVE
          │
     ┌────┴────┐
     ▼         ▼
  PAID_OFF  DEFAULTED
```
Las transiciones de estado deberán estar explícitamente controladas. No se permitirá cambiar arbitrariamente el estado de una solicitud o préstamo.

## 7. Dominios iniciales

- **7.1 Identity & Access:** Usuarios, autenticación, autorización, roles y permisos.
- **7.2 Customer:** Información del cliente, perfil socioeconómico, laboral y financiero.
- **7.3 Credit Application:** Solicitudes, documentación, estado de evaluación e información solicitada.
- **7.4 Risk:** Evaluación de riesgo, reglas, score, factores de riesgo, decisión automática y derivación a revisión manual.
- **7.5 Loan:** Crédito aprobado, condiciones del préstamo, desembolso y estado del crédito.
- **7.6 Amortization:** Cronograma, cuotas, capital, intereses, saldos y penalidades.
- **7.7 Payment:** Registro y aplicación de pagos, idempotencia, actualización de cuotas y préstamo.
- **7.8 Audit:** Registro de acciones, cambios de estado, operaciones financieras y trazabilidad.
- **7.9 Notification:** Notificaciones, eventos de comunicación y procesamiento asíncrono.

## 8. Motor de riesgo
Basado en reglas determinísticas considerando ingresos, deuda, ratio de endeudamiento, situación y antigüedad laboral, historial, monto y plazo.
Resultados: `APPROVED`, `REJECTED`, `MANUAL_REVIEW`. Cada evaluación conserva los factores de decisión.

## 9. Simulador y cálculos financieros
Entradas: Monto, plazo, tasa, frecuencia de pago y fecha de inicio.
Salidas: Cuota, intereses, capital, saldo, costo total y cronograma.
Primera versión: Amortización francesa con tipos y estrategias de precisión adecuados (evitando punto flotante) y redondeos explícitos.

## 10. Pagos e integridad transaccional
Secuencia: `Payment` → `Installment` → `Loan Balance` → `Outbox Event` ejecutados en una sola transacción ACID con control de reversión.

## 11. Idempotencia
Operaciones sensibles (como pagos) toleran duplicados de identificadores (`payment_request_id`) sin procesarse doblemente, clave para eventos asíncronos.

## 12. Transactional Outbox
Evita inconsistencias entre la base de datos PostgreSQL y el broker de mensajes escribiendo eventos en una tabla outbox dentro de la misma transacción de negocio para ser procesados por Debezium.

## 13. Eventos de dominio
Contratos versionables para eventos como `CustomerRegistered`, `CreditApplicationSubmitted`, `RiskAssessmentCompleted`, `CreditApplicationApproved`, `CreditApplicationRejected`, `ManualReviewRequested`, `LoanDisbursed`, `InstallmentGenerated`, `PaymentRegistered`, `InstallmentPaid`, `InstallmentOverdue`, `LoanPaidOff`.

## 14. Change Data Capture (CDC)
Debezium captura los registros de la tabla Outbox en PostgreSQL para transportarlos al event stream desacoplando el dominio de la infraestructura de mensajería.

## 15. Arquitectura de mensajería
Flujo: Debezium → AWS SNS → SQS (Notificaciones / Proyecciones) → Lambdas → Notificaciones / DynamoDB. Contempla reintentos, idempotencia, DLQ y observabilidad.

## 16. DynamoDB y CQRS
Modelo de lectura optimizado mediante DynamoDB con Single Table Design (`PK`, `SK`) alimentado por proyecciones basadas en eventos desde PostgreSQL.

## 17. AWS Lambda y Seguridad IAM
Procesamiento asíncrono ligero sujeto estrictamente al principio de mínimo privilegio en roles IAM. Secretos gestionados con AWS Secrets Manager.

## 18. Frontend Angular
Aplicación organizada por capacidades de negocio, con experiencias diferenciadas para Cliente, Analista y Administrador. Implementa Angular Signals, arquitectura Zoneless, componentes standalone, lazy loading, `@defer` y manejo reactivo del estado.

## 19. Monorepo Nx
Monorepo organizado con Nx separando aplicaciones **ejecutables** en `apps/ (api, web, workers)` y **librerias de capacidades reutilizables** en `libs/ (backend, frontend, contracts)`. Las dependencias estarán restringidas mediante límites arquitectónicos, manteniendo el dominio independiente de frameworks e infraestructura.

## 20. Docker y Kubernetes
Empaquetado multi-stage y despliegue cloud-native en Kubernetes gestionando deployments, services, probes, configuración de recursos y Horizontal Pod Autoscaler (HPA).

## 21. Observabilidad y Auditoría
- Logs estructurados, correlation IDs, métricas, health checks y trazabilidad punta a punta.
- Registro histórico inmutable de auditoría para operaciones relevantes.

## 22. Indicadores
Métricas de negocio documentadas (solicitudes, conversión, desembolsos, cartera activa/vencida, mora, PAR).

## 23. Requisitos no funcionales
Seguridad, Consistencia, Disponibilidad, Rendimiento y Mantenibilidad.

## 24. Testing
Estrategia de pruebas abarcando Unit Tests, Integration Tests, Contract Tests y End-to-End (E2E).

## 25. Arquitectura de alto nivel y Evolución incremental
Evolución planificada en 9 fases:
- **Fase 1:** Domain Core
- **Fase 2:** Monolith Core
- **Fase 3:** Transactional Integrity
- **Fase 4:** Event-Driven Architecture
- **Fase 5:** Read Models
- **Fase 6:** Serverless
- **Fase 7:** Frontend moderno
- **Fase 8:** Cloud Native
- **Fase 9:** Observabilidad

## 26. Fuera del alcance inicial
Integraciones reales con bancos, centrales de riesgo, pasarelas de pago externas, KYC o Machine Learning para scoring (se usarán datos y stubs simulados).

## 27. Principios arquitectónicos
1. El dominio primero.
2. PostgreSQL como fuente de verdad.
3. Eventos como mecanismo de desacoplamiento.
4. Read Models especializados.
5. Idempotencia.
6. Seguridad por defecto.
7. Observabilidad desde el diseño.
8. Infraestructura reproducible.
9. Evolución incremental.

## 28. Definición de éxito
Validación integral mediante un flujo de 18 pasos desde la creación de la solicitud del cliente hasta la actualización de proyecciones y registro de pagos.

## 29. Datos del repositorio
- **Repositorio:** `microcredit-platform`
- **Descripción corta:** Enterprise lending platform built with DDD, NestJS, Angular, PostgreSQL, Transactional Outbox, Debezium CDC, AWS SNS/SQS, Lambda, DynamoDB and Kubernetes.