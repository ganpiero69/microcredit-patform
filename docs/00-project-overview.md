MicroCredit — Enterprise Lending Platform
Sistema empresarial de microcréditos orientado a demostrar arquitectura de software, diseño de dominio, procesamiento transaccional, comunicación asíncrona y despliegue cloud-native.

1. Descripción
MicroCredit es una plataforma empresarial de gestión de microcréditos que cubre el ciclo completo de un crédito: registro del cliente, simulación, solicitud, evaluación automatizada de riesgo, revisión manual, aprobación, desembolso, generación del cronograma, gestión de pagos y seguimiento de cartera.

El proyecto está diseñado como una plataforma orientada a producción y servirá como proyecto integrador para demostrar conocimientos de:

* Domain-Driven Design (DDD)

* Arquitectura modular

* Desarrollo backend con NestJS

* Desarrollo frontend con Angular

* PostgreSQL

* Transactional Outbox Pattern
 
* Event-Driven Architecture
 
* Change Data Capture (CDC)
 
* Debezium
 
* AWS SNS/SQS

* AWS Lambda
 
* DynamoDB Single Table Design
 
* IAM y principio de mínimo privilegio
 
* AWS Secrets Manager
 
* Docker
 
* Kubernetes

* Horizontal Pod Autoscaling

* Observabilidad

* Testing
 
* Seguridad
 
* Auditoría y trazabilidad

2. Objetivo del proyecto
Construir una plataforma de lending que simule un entorno empresarial real, donde las operaciones financieras críticas requieran consistencia transaccional y los procesos secundarios puedan ejecutarse de forma asíncrona y desacoplada.

El proyecto deberá demostrar que las decisiones tecnológicas responden a necesidades concretas del dominio y no únicamente al uso de determinadas herramientas.

La arquitectura deberá evolucionar progresivamente desde un núcleo transaccional modular hacia una plataforma distribuida y orientada a eventos.

3. Problema de negocio
Una entidad financiera necesita gestionar solicitudes de microcrédito de manera rápida, consistente, segura y trazable.

El sistema debe permitir:

Registrar y gestionar clientes.

Simular diferentes escenarios de crédito.

Recibir solicitudes de préstamo.

Evaluar automáticamente el riesgo.

Derivar casos complejos a revisión manual.

Aprobar o rechazar solicitudes.

Desembolsar créditos aprobados.

Generar cronogramas de pago.

Registrar y aplicar pagos.

Gestionar cuotas vencidas y mora.

Mantener una auditoría completa de operaciones relevantes.

Proporcionar información optimizada para dashboards y consultas.

Procesar eventos de negocio de forma asíncrona.

Mantener separación entre el modelo transaccional y los modelos de lectura.

4. Objetivos técnicos
El proyecto tendrá como objetivos técnicos principales:

4.1 Arquitectura
Aplicar Domain-Driven Design.

Mantener separación entre dominio, aplicación e infraestructura.

Utilizar un monorepo administrado con Nx.

Mantener límites claros entre módulos.

Evitar acoplamiento innecesario entre el dominio y frameworks externos.

4.2 Persistencia
Utilizar PostgreSQL como fuente principal de verdad transaccional.

Implementar transacciones ACID.

Utilizar locking cuando sea necesario.

Garantizar idempotencia en operaciones críticas.

Implementar un Transactional Outbox.

4.3 Procesamiento asíncrono
Publicar eventos de dominio mediante Outbox.

Capturar cambios mediante CDC.

Utilizar Debezium como mecanismo de Change Data Capture.

Distribuir eventos mediante AWS SNS/SQS.

Implementar consumidores desacoplados.

4.4 Read Models
Utilizar DynamoDB para modelos de lectura específicos.

Diseñar una Single Table orientada a patrones de acceso.

Actualizar las proyecciones mediante eventos.

Aplicar conceptos de CQRS cuando resulte apropiado.

4.5 Serverless
Utilizar AWS Lambda para determinados consumidores y tareas asíncronas.

Aplicar IAM con mínimo privilegio.

Gestionar secretos mediante AWS Secrets Manager.

4.6 Frontend
Construir una aplicación Angular moderna.

Utilizar Angular Signals.

Utilizar arquitectura Zoneless.

Aplicar carga diferida mediante @defer.

Diseñar dashboards orientados a rendimiento.

4.7 Infraestructura
Crear imágenes Docker multi-stage.

Ejecutar los servicios mediante Kubernetes.

Configurar requests y limits.

Implementar liveness/readiness probes.

Configurar Horizontal Pod Autoscaler.

Mantener configuraciones separadas por entorno.

5. Actores
El sistema tendrá inicialmente tres actores principales.

5.1 Cliente
El cliente puede:

Registrarse.

Autenticarse.

Gestionar su información.

Completar información financiera.

Simular un crédito.

Solicitar un crédito.

Adjuntar documentación.

Consultar el estado de su solicitud.

Consultar su crédito activo.

Consultar su cronograma.

Registrar pagos simulados.

Consultar su historial.

5.2 Analista de riesgos
El analista puede:

Consultar solicitudes pendientes.

Consultar información financiera.

Consultar el resultado del motor de riesgo.

Consultar los factores que afectaron la decisión.

Aprobar solicitudes enviadas a revisión manual.

Rechazar solicitudes.

Registrar observaciones.

Consultar información de auditoría relacionada con la solicitud.

5.3 Administrador
El administrador puede:

Gestionar usuarios y roles.

Configurar parámetros de riesgo.

Configurar tasas.

Consultar auditoría.

Consultar métricas.

Consultar información de cartera.

Gestionar parámetros operativos.

6. Ciclo de vida del crédito
El flujo principal será:

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
    │       APPROVED   REJECTED
    │          │
    └──────────┘
           │
           ▼
      DISBURSED
           │
           ▼
         ACTIVE
           │
     ┌─────┴─────┐
     ▼           ▼
   PAID_OFF   DEFAULTED

Las transiciones de estado deberán estar explícitamente controladas.

No se permitirá cambiar arbitrariamente el estado de una solicitud o préstamo.

7. Dominios iniciales
El sistema estará organizado alrededor de diferentes bounded contexts.

7.1 Identity & Access
Responsabilidades:

Usuarios.

Autenticación.

Autorización.

Roles.

Permisos.

7.2 Customer
Responsabilidades:

Información del cliente.

Perfil socioeconómico.

Información laboral.

Información financiera.

7.3 Credit Application
Responsabilidades:

Solicitudes.

Documentación.

Estado de evaluación.

Información solicitada.

7.4 Risk
Responsabilidades:

Evaluación de riesgo.

Reglas.

Score.

Factores de riesgo.

Decisión automática.

Derivación a revisión manual.

7.5 Loan
Responsabilidades:

Crédito aprobado.

Condiciones del préstamo.

Desembolso.

Estado del crédito.

7.6 Amortization
Responsabilidades:

Cronograma.

Cuotas.

Capital.

Intereses.

Saldos.

Penalidades.

7.7 Payment
Responsabilidades:

Registro de pagos.

Aplicación de pagos.

Idempotencia.

Actualización de cuotas.

Actualización del préstamo.

7.8 Audit
Responsabilidades:

Registro de acciones.

Cambios de estado.

Operaciones financieras.

Trazabilidad.

7.9 Notification
Responsabilidades:

Notificaciones.

Eventos de comunicación.

Procesamiento asíncrono.

8. Motor de riesgo
El sistema contará con un motor de decisión basado inicialmente en reglas determinísticas.

Las variables podrán incluir:

Ingresos mensuales.

Deuda mensual.

Ratio de endeudamiento.

Situación laboral.

Antigüedad laboral.

Historial crediticio.

Monto solicitado.

Plazo.

Información adicional del cliente.

El resultado será:

APPROVED
REJECTED
MANUAL_REVIEW

Cada evaluación deberá generar información suficiente para explicar la decisión.

Ejemplo conceptual:

Risk Assessment

Score: 742

Decision: MANUAL_REVIEW

Factors:
- Debt ratio above preferred threshold
- Employment tenure below preferred threshold
- Credit history acceptable

El sistema deberá conservar los factores utilizados para la decisión y no únicamente el resultado final.

9. Simulador y cálculos financieros
El sistema permitirá realizar simulaciones antes de crear una solicitud formal.

Entradas:

Monto.

Plazo.

Tasa.

Frecuencia de pago.

Fecha de inicio.

Salidas:

Cuota.

Intereses.

Capital.

Saldo.

Costo total.

Cronograma.

La primera versión utilizará amortización francesa.

Los cálculos financieros deberán utilizar tipos de datos y estrategias de precisión adecuadas para evitar errores derivados del uso de números de punto flotante.

Los redondeos deberán estar definidos explícitamente.

10. Pagos e integridad transaccional
El registro de un pago representa una operación crítica.

Una operación podrá implicar:

Payment
    ↓
Installment
    ↓
Loan Balance
    ↓
Outbox Event

Estas modificaciones deberán ejecutarse dentro de una misma transacción cuando formen parte de una única operación de negocio.

Conceptualmente:

BEGIN

Create Payment
Apply Payment to Installment
Update Loan Balance
Create Outbox Event

COMMIT

Si cualquiera de las operaciones falla, la transacción deberá revertirse.

11. Idempotencia
Las operaciones sensibles deberán ser idempotentes.

Un ejemplo es el procesamiento de pagos.

El sistema deberá poder recibir dos veces el mismo identificador de operación sin registrar dos pagos.

Conceptualmente:

payment_request_id = "PAY-123"

Request 1 → processed
Request 2 → already processed

Esto será especialmente importante cuando el sistema procese mensajes asíncronos.

12. Transactional Outbox
Las operaciones de negocio que produzcan eventos deberán utilizar el patrón Transactional Outbox.

Ejemplo:

┌──────────────────────────────────┐
│ PostgreSQL Transaction            │
│                                  │
│  Update Loan                     │
│  Create Payment                  │
│  Insert Outbox Event             │
│                                  │
└───────────────┬──────────────────┘
                │
              COMMIT
                │
                ▼
        Outbox Event
                │
                ▼
            Debezium

El objetivo es evitar inconsistencias entre:

Base de datos
      +
Broker de mensajes

La publicación del evento será responsabilidad de la infraestructura de mensajería/CDC y no de una segunda transacción independiente.

13. Eventos de dominio
El sistema podrá producir eventos como:

CustomerRegistered
CreditApplicationSubmitted
RiskAssessmentCompleted
CreditApplicationApproved
CreditApplicationRejected
ManualReviewRequested
LoanDisbursed
InstallmentGenerated
PaymentRegistered
InstallmentPaid
InstallmentOverdue
LoanPaidOff

Los eventos deberán tener contratos versionables.

Ejemplo conceptual:

{
  "eventId": "uuid",
  "eventType": "LoanDisbursed",
  "version": 1,
  "occurredAt": "timestamp",
  "aggregateId": "loan-id",
  "payload": {}
}

14. Change Data Capture
Debezium será utilizado para capturar los eventos registrados en el Outbox.

Flujo:

PostgreSQL
    │
    ▼
Outbox Table
    │
    ▼
Debezium
    │
    ▼
Event Stream
    │
    ▼
AWS Messaging

La implementación deberá evitar que el dominio dependa directamente de Debezium.

El dominio producirá eventos.

La infraestructura será responsable de transportarlos.

15. Arquitectura de mensajería
La arquitectura asíncrona utilizará:

Debezium
    │
    ▼
AWS SNS
    │
    ├───────────────┐
    ▼               ▼
SQS Notifications  SQS Projections
    │               │
    ▼               ▼
 Lambda            Lambda
    │               │
    ▼               ▼
Notification     DynamoDB

Esto permitirá que diferentes consumidores procesen el mismo evento de manera independiente.

Los consumidores deberán contemplar:

Reintentos.

Idempotencia.

Dead Letter Queues.

Manejo de errores.

Observabilidad.

16. DynamoDB y CQRS
PostgreSQL será el modelo transaccional principal.

DynamoDB será utilizado para determinados modelos de lectura.

Ejemplo:

PostgreSQL
    │
    │ Domain Events
    ▼
SNS/SQS
    │
    ▼
Projection Lambda
    │
    ▼
DynamoDB

La tabla utilizará Single Table Design.

Ejemplo conceptual:

PK              SK
────────────────────────────────────────
CUSTOMER#100    PROFILE
CUSTOMER#100    LOAN#500
CUSTOMER#100    LOAN#500#SUMMARY
CUSTOMER#100    LOAN#500#INSTALLMENT#01
CUSTOMER#100    LOAN#500#INSTALLMENT#02
CUSTOMER#100    LOAN#500#INSTALLMENT#03

El diseño se realizará a partir de los patrones de acceso reales del sistema.

17. AWS Lambda
Lambda será utilizada para procesamiento asíncrono y tareas que no requieran mantener un proceso de larga duración.

Casos iniciales:

Proyección de eventos hacia DynamoDB.

Procesamiento de notificaciones.

Tareas posteriores al desembolso.

Procesamiento de determinados eventos de cartera.

La lógica transaccional principal permanecerá en el core de aplicación.

18. Seguridad AWS
Las funciones Lambda utilizarán roles IAM específicos.

Ejemplo conceptual:

ProjectionLambdaRole

Allow:
  dynamodb:PutItem
  dynamodb:UpdateItem

Deny:
  administración global
  acceso a recursos no relacionados

Se aplicará el principio de mínimo privilegio.

Los secretos y credenciales sensibles no deberán almacenarse directamente en el código fuente ni en imágenes Docker.

AWS Secrets Manager será utilizado para gestionar secretos cuando corresponda.

19. Frontend Angular
La aplicación web estará construida con Angular y proporcionará experiencias diferenciadas según el rol del usuario.

La organización funcional estará orientada a capacidades de negocio y features, mientras que los roles determinarán qué funcionalidades puede visualizar y utilizar cada usuario.

Cliente
El cliente tendrá acceso a una experiencia orientada a la consulta y gestión de sus productos financieros.

Cliente
└── Dashboard
    ├── Resumen
    ├── Crédito
    ├── Cronograma
    ├── Pagos
    └── Historial

Analista
El analista tendrá una experiencia orientada al análisis y evaluación de solicitudes de crédito.

Analista
└── Risk Dashboard
    ├── Pending Reviews
    ├── Application Detail
    ├── Risk Assessment
    └── Decision

Administrador
El administrador tendrá una experiencia orientada a la configuración, administración y supervisión de la plataforma.

Administrador
└── Administration
    ├── Risk Rules
    ├── Users
    ├── Audit
    └── Metrics

Organización por funcionalidades
Aunque la interfaz se presente según roles, las librerías frontend se organizarán principalmente por capacidad de negocio, evitando duplicar funcionalidades entre diferentes experiencias.

Ejemplo:

libs/frontend/
├── customers/
├── loans/
├── payments/
├── credit-analysis/
├── risk/
├── administration/
└── shared/

De esta forma, una misma capacidad puede ser utilizada por diferentes roles sin acoplarla directamente a un único perfil de usuario.

Por ejemplo, una funcionalidad relacionada con loans puede ser utilizada tanto por el Cliente para consultar su crédito como por el Analista para evaluarlo.

Los roles se resolverán principalmente mediante:
Autenticación.

Autorización.

Route guards.

Configuración de rutas.

Composición de layouts.

Visibilidad y acceso a funcionalidades.

Arquitectura Angular

La aplicación utilizará Standalone Components como modelo principal de composición, evitando una dependencia innecesaria de NgModules tradicionales.

La navegación se organizará mediante lazy loading, cargando funcionalidades y áreas de la aplicación únicamente cuando sean requeridas.

Se utilizará Angular Signals como mecanismo principal para el manejo reactivo del estado local y derivado.

La aplicación utilizará Zoneless Angular para reducir la dependencia del mecanismo tradicional de Zone.js y adoptar un modelo de actualización basado en señales y mecanismos explícitos de reactividad.

@defer se utilizará de manera selectiva para diferir la carga y renderizado de contenido que no sea necesario inmediatamente, especialmente en funcionalidades secundarias o componentes costosos.

El manejo del estado seguirá un enfoque reactivo, diferenciando entre:

Estado local
    ↓
Estado de feature
    ↓
Estado de aplicación
    ↓
Datos provenientes del backend

No todo estado deberá convertirse en estado global. La ubicación del estado dependerá de su alcance y responsabilidad.

Principios frontend
El frontend deberá mantener una separación clara entre:

Presentación
    ↓
Estado / Data Access
    ↓
API / Contracts

Las funcionalidades Angular no deberán importar directamente lógica interna de libs/backend.

La comunicación con el backend se realizará mediante APIs y contratos definidos en libs/contracts.

La arquitectura frontend deberá priorizar:

Composición mediante features.

Reutilización de componentes.

Separación entre presentación y acceso a datos.

Carga diferida.

Reactividad explícita.

Accesibilidad.

Seguridad basada en autorización.

Experiencia diferenciada según el rol.

20. Monorepo Nx
El proyecto utilizará Nx para organizar las aplicaciones ejecutables y las librerías del sistema dentro de un único monorepo.

La separación principal será:

apps/
├── api/
├── web/
└── workers/

libs/
├── backend/
├── frontend/
└── contracts/

Esta organización diferencia entre:

apps/
    Procesos ejecutables y desplegables.

libs/
    Capacidades reutilizables del sistema.

Applications
apps/ contiene los puntos de entrada de los diferentes procesos del sistema.

apps/
├── api/
├── web/
└── workers/

API
apps/api/

Contiene la aplicación NestJS que expone la API HTTP.

Su responsabilidad principal es actuar como composition root, inicializando la aplicación y componiendo las capacidades backend necesarias.

La lógica de negocio no debe concentrarse en apps/api.

Los controllers, módulos y casos de uso pueden residir en las librerías backend y ser utilizados por la aplicación API.

Conceptualmente:

apps/api/
    ↓
libs/backend/
    ↓
Business Capabilities

Esto permite mantener apps/api como ejecutable y libs/backend como código reutilizable.

Web
apps/web/

Contiene la aplicación Angular ejecutable.

Su responsabilidad es componer la experiencia web utilizando las funcionalidades disponibles en libs/frontend y los contratos definidos en libs/contracts.

Conceptualmente:

apps/web/
    ↓
libs/frontend/
    ↓
libs/contracts/
    ↓
API

La arquitectura específica de Angular, roles, navegación, Signals, Zoneless, lazy loading y @defer se define en la sección de Frontend Angular y no se duplica aquí.

Workers
apps/workers/

Contiene procesos independientes destinados a ejecutar tareas que no necesitan formar parte del ciclo HTTP.

Los workers podrán reutilizar las mismas capacidades de negocio del backend:

apps/workers/
       ↓
libs/backend/
       ↓
application
       ↓
domain

No se deberá duplicar lógica de negocio entre api y workers.

Los workers se incorporarán cuando exista una necesidad real de procesamiento asíncrono, background jobs, procesamiento prolongado, integraciones o ejecución independiente.

Backend Libraries
Las capacidades backend estarán organizadas por bounded context o feature de negocio:

libs/backend/
├── customers/
├── loans/
├── payments/
├── credit-analysis/
└── ...

Cada capacidad podrá organizarse internamente mediante las capas que sean necesarias:

loans/
├── domain/
├── application/
├── infrastructure/
└── presentation/

La estructura representa responsabilidades arquitectónicas diferentes.

Domain
Contiene el modelo y las reglas del negocio:

Entidades.

Value Objects.

Agregados.

Invariantes.

Eventos de dominio.

Reglas de negocio.

Debe permanecer independiente de frameworks, bases de datos y proveedores externos.

Application
Contiene los casos de uso y la orquestación necesaria para ejecutar operaciones del negocio.

Ejemplo:

application/
└── use-cases/
    ├── create-loan.use-case.ts
    ├── approve-loan.use-case.ts
    └── disburse-loan.use-case.ts

Application coordina el dominio y las abstracciones necesarias para completar un caso de uso.

Infrastructure
Contiene las implementaciones técnicas y adaptadores hacia sistemas externos.

Puede incluir:

infrastructure/
├── persistence/
├── messaging/
└── external-services/

Aquí pueden residir implementaciones relacionadas con:

PostgreSQL.

ORM.

Redis.

AWS.

Servicios externos.

Mensajería.

Persistencia.

Infrastructure representa detalles técnicos que no deben contaminar el modelo de dominio.

Presentation
Contiene los adaptadores mediante los cuales las capacidades del backend reciben solicitudes externas.

En el caso de NestJS puede contener:

presentation/
└── http/
    ├── loans.controller.ts
    └── dto/

Los controllers pertenecen funcionalmente al backend aunque estén dentro de una librería.

Por tanto:

apps/api
    ↓
libs/backend/loans/presentation

no representa una separación entre "API" y "backend", sino entre:

API = aplicación ejecutable

Backend = capacidades de negocio reutilizables

Esto permite que la API y los workers consuman las mismas capacidades sin duplicación.

Frontend Libraries
Las funcionalidades frontend se organizarán por capacidad de negocio:

libs/frontend/
├── customers/
├── loans/
├── payments/
├── credit-analysis/
├── risk/
├── administration/
└── shared/

Las librerías frontend contienen las funcionalidades reutilizables de Angular.

No deben depender de implementaciones internas de libs/backend.

La comunicación con el backend se realizará mediante APIs y contratos.

La estructura específica del frontend se define en la sección 19. Frontend Angular.

Contracts
Los contratos compartidos estarán organizados por capacidad:

libs/contracts/
├── customers/
├── loans/
├── payments/
├── credit-analysis/
└── ...

Estos contratos representan acuerdos de comunicación entre diferentes partes del sistema.

Pueden incluir:

Request contracts.

Response contracts.

Eventos de integración.

Tipos compartidos necesarios para comunicación.

Los contratos deben permanecer independientes de frameworks y detalles de infraestructura.

Reglas de dependencia
Nx se utilizará para establecer y hacer cumplir límites entre proyectos.

Las aplicaciones podrán consumir las librerías correspondientes:

apps/api
    → libs/backend
    → libs/contracts

apps/web
    → libs/frontend
    → libs/contracts

apps/workers
    → libs/backend
    → libs/contracts

Las dependencias internas del backend deberán respetar la dirección arquitectónica:

presentation
      ↓
application
      ↓
domain

La infraestructura implementará los detalles técnicos necesarios para interactuar con sistemas externos.

El dominio no podrá depender directamente de infraestructura.

Por lo tanto, no se permitirá:

domain → infrastructure
domain → PostgreSQL
domain → Prisma
domain → AWS
domain → NestJS
domain → Angular

Tampoco se permitirá acoplamiento directo entre las capacidades frontend y backend:

libs/frontend → libs/backend
libs/backend  → libs/frontend

La comunicación entre ambos lados se realizará mediante APIs y contratos.

Aislamiento del dominio
El objetivo principal de estas restricciones es proteger el modelo de negocio frente a decisiones tecnológicas.

El dominio deberá permanecer independiente de:

NestJS.

Angular.

PostgreSQL.

Prisma.

AWS.

DynamoDB.

Redis.

Otros frameworks, proveedores o mecanismos de infraestructura.

La tecnología podrá cambiar sin obligar a modificar las reglas fundamentales del negocio.

Evolución del monorepo
La estructura inicial no debe considerarse definitiva.

Los bounded contexts y features podrán evolucionar conforme aumente el conocimiento del negocio.

Una capacidad inicialmente organizada como:

libs/backend/loans/

podrá posteriormente dividirse, fusionarse o reorganizarse si las reglas y responsabilidades del dominio lo justifican.

Nx debe facilitar esta evolución manteniendo:

Límites claros.

Dependencias explícitas.

Reutilización controlada.

Separación de responsabilidades.

Aislamiento del dominio.

21. Docker
Las aplicaciones se empaquetarán utilizando imágenes multi-stage.

Objetivos:

Reducir tamaño de imagen.

Separar build y runtime.

Evitar herramientas innecesarias en producción.

Ejecutar procesos con usuarios no privilegiados cuando sea posible.

Mantener imágenes reproducibles.

22. Kubernetes
Los principales componentes desplegables podrán ejecutarse sobre Kubernetes.

Se deberán configurar:

Deployments.

Services.

ConfigMaps.

Secrets o integración con secret management.

Ingress.

Resource requests.

Resource limits.

Liveness probes.

Readiness probes.

Horizontal Pod Autoscaler.

Ejemplo conceptual:

                    Ingress
                       │
                       ▼
                  API Service
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
           API-1     API-2     API-3
             │         │         │
             └─────────┼─────────┘
                       │
                    PostgreSQL

23. Observabilidad
El sistema deberá contemplar:

Logs estructurados.

Correlation IDs.

Métricas.

Health checks.

Trazabilidad de operaciones.

Identificación de eventos.

Registro de errores.

Una solicitud deberá poder rastrearse conceptualmente:

HTTP Request
     │
     ▼
Application
     │
     ▼
PostgreSQL
     │
     ▼
Outbox
     │
     ▼
Debezium
     │
     ▼
SNS
     │
     ▼
SQS
     │
     ▼
Lambda
     │
     ▼
DynamoDB

24. Auditoría
Las operaciones relevantes deberán generar registros de auditoría.

Ejemplos:

Creación de solicitud.

Evaluación de riesgo.

Aprobación.

Rechazo.

Modificación de condiciones.

Desembolso.

Registro de pago.

Cambio de reglas.

Cambio de estado.

Un registro de auditoría deberá conservar información como:

id
timestamp
actor
action
entity
entityId
previousState
newState
metadata

La auditoría deberá tratarse como un registro histórico y no como una tabla que pueda modificarse arbitrariamente desde la lógica de negocio.

25. Indicadores
El sistema podrá proporcionar indicadores como:

Solicitudes recibidas.

Solicitudes aprobadas.

Solicitudes rechazadas.

Solicitudes en revisión manual.

Tasa de conversión.

Total desembolsado.

Cartera activa.

Cartera vencida.

Cuotas vencidas.

Mora temprana.

PAR (Portfolio at Risk).

Los indicadores deberán documentar claramente su fórmula y periodo de medición.

26. Requisitos no funcionales
Seguridad
Autenticación.

Autorización.

RBAC.

Validación de entrada.

Protección de secretos.

IAM de mínimo privilegio.

Auditoría.

Consistencia
Transacciones ACID.

Control de concurrencia.

Idempotencia.

Integridad referencial.

Disponibilidad
Health checks.

Readiness/liveness probes.

Reintentos controlados.

Dead Letter Queues.

HPA.

Rendimiento
Consultas optimizadas.

Índices adecuados.

Modelos de lectura especializados.

Carga diferida en frontend.

Procesamiento asíncrono.

Mantenibilidad
DDD.

Modularización.

Contratos explícitos.

Tests automatizados.

Documentación.

Arquitectura orientada a dependencias.

27. Testing
El proyecto deberá utilizar diferentes niveles de pruebas.

Unit Tests
Para:

Value Objects.

Entidades.

Agregados.

Reglas de riesgo.

Cálculos financieros.

Casos de uso.

Integration Tests
Para:

PostgreSQL.

Repositorios.

Transacciones.

Outbox.

DynamoDB.

Contract Tests
Para:

Eventos.

APIs.

Consumidores.

End-to-End
Para flujos completos como:

Customer
   ↓
Application
   ↓
Risk Assessment
   ↓
Approval
   ↓
Disbursement
   ↓
Payment

28. Arquitectura de alto nivel
La arquitectura objetivo será:

                         ┌──────────────────┐
                         │ Angular Web App  │
                         │ Signals/Zoneless │
                         └────────┬─────────┘
                                  │
                                  ▼
                         ┌──────────────────┐
                         │   NestJS API     │
                         └────────┬─────────┘
                                  │
                                  ▼
                  ┌───────────────────────────────┐
                  │        Application Layer      │
                  └──────────────┬────────────────┘
                                 │
                  ┌──────────────▼────────────────┐
                  │          Domain Layer          │
                  │                                │
                  │ Customer / Credit / Risk       │
                  │ Loan / Payment / Audit         │
                  └──────────────┬────────────────┘
                                 │
                                 ▼
                         ┌────────────────┐
                         │   PostgreSQL   │
                         └───────┬────────┘
                                 │
                                 ▼
                         ┌────────────────┐
                         │     Outbox     │
                         └───────┬────────┘
                                 │
                                 ▼
                            Debezium CDC
                                 │
                                 ▼
                         ┌────────────────┐
                         │    AWS SNS     │
                         └───────┬────────┘
                                 │
                       ┌─────────┴─────────┐
                       ▼                   ▼
                     SQS                 SQS
                       │                   │
                       ▼                   ▼
                    Lambda              Lambda
                       │                   │
                       ▼                   ▼
                Notifications         DynamoDB
                                      Read Model

29. Estrategia de evolución
El proyecto no implementará toda la arquitectura distribuida desde el primer día.

Se desarrollará incrementalmente.

Fase 1 — Domain Core
 Definir bounded contexts.

 Definir entidades.

 Definir value objects.

 Definir agregados.

 Definir eventos de dominio.

 Definir invariantes.

 Definir casos de uso.

Fase 2 — Monolith Core
 Nx monorepo.

 NestJS.

 PostgreSQL.

 Autenticación.

 Clientes.

 Solicitudes.

 Motor de riesgo.

 Préstamos.

 Amortización.

 Pagos.

 Auditoría.

Fase 3 — Transactional Integrity
 Transacciones.

 Idempotencia.

 Control de concurrencia.

 Outbox Pattern.

 Tests de consistencia.

Fase 4 — Event-Driven Architecture
 Contratos de eventos.

 Debezium.

 CDC.

 SNS.

 SQS.

 Dead Letter Queues.

 Consumidores.

Fase 5 — Read Models
 DynamoDB.

 Single Table Design.

 Proyecciones.

 CQRS.

 Dashboard optimizado.

Fase 6 — Serverless
 Lambda.

 IAM.

 Secrets Manager.

 Procesamiento asíncrono.

Fase 7 — Frontend moderno
 Angular.

 Signals.

 Zoneless.

 @defer.

 Dashboards.

 Lazy loading.

Fase 8 — Cloud Native
 Docker multi-stage.

 Kubernetes.

 Requests/limits.

 Liveness probes.

 Readiness probes.

 HPA.

Fase 9 — Observabilidad
 Structured logging.

 Correlation IDs.

 Métricas.

 Health checks.

 Trazabilidad.

 Dashboards operacionales.

30. Fuera del alcance inicial
Para evitar sobreingeniería, la primera versión no incluirá:

Integración real con bancos.

Transferencias bancarias reales.

Consulta real a centrales de riesgo.

KYC real con proveedores externos.

Pasarelas de pago reales.

Machine Learning para scoring.

Dinero real.

Datos reales de clientes.

El sistema utilizará datos ficticios y proveedores simulados cuando sea necesario.

Estas funcionalidades podrán incorporarse posteriormente como extensiones.

31. Principios arquitectónicos
El proyecto seguirá los siguientes principios:

1. El dominio primero
Las reglas de negocio no dependerán de frameworks.

2. PostgreSQL como fuente de verdad
Las operaciones financieras críticas tendrán un modelo transaccional consistente.

3. Eventos como mecanismo de desacoplamiento
Los procesos secundarios no deberán bloquear innecesariamente las operaciones principales.

4. Read Models especializados
Las consultas de lectura podrán utilizar modelos optimizados independientemente del modelo transaccional.

5. Idempotencia
Los mensajes y operaciones que puedan repetirse deberán diseñarse para tolerar duplicados.

6. Seguridad por defecto
Los permisos deberán ser mínimos y explícitos.

7. Observabilidad desde el diseño
Las operaciones críticas deberán poder rastrearse.

8. Infraestructura reproducible
Los entornos deberán poder construirse de manera consistente.

9. Evolución incremental
La complejidad arquitectónica deberá introducirse cuando exista una necesidad que la justifique.

32. Resultado esperado
Al finalizar, MicroCredit deberá demostrar la construcción de una plataforma empresarial que combine:

                 MICROCredit
                      │
        ┌─────────────┼─────────────┐
        │             │             │
       DDD        Event Driven    Cloud Native
        │             │             │
     NestJS        Debezium       Docker
     Nx            SNS/SQS        Kubernetes
     PostgreSQL    Lambda         HPA
        │             │             │
        └─────────────┼─────────────┘
                      │
                 Angular
              Signals/Zoneless
                      │
                      ▼
              Enterprise Core

El objetivo final no es demostrar una colección de tecnologías aisladas, sino demostrar la capacidad de diseñar y construir un sistema empresarial completo a partir de necesidades reales de negocio.

33. Definición de éxito
El proyecto se considerará exitoso cuando sea posible demostrar, mediante código, pruebas y documentación, un flujo completo como:

1. Cliente crea solicitud
        ↓
2. Sistema evalúa riesgo
        ↓
3. Solicitud es aprobada
        ↓
4. Se crea el préstamo
        ↓
5. Se genera el cronograma
        ↓
6. Se registra el desembolso
        ↓
7. Se genera un Domain Event
        ↓
8. Event llega al Outbox
        ↓
9. Debezium captura el evento
        ↓
10. SNS distribuye el evento
        ↓
11. SQS entrega el mensaje
        ↓
12. Lambda procesa el evento
        ↓
13. DynamoDB actualiza el Read Model
        ↓
14. Angular consulta el dashboard
        ↓
15. Cliente registra un pago
        ↓
16. PostgreSQL actualiza el estado
        ↓
17. Se genera un nuevo evento
        ↓
18. Las proyecciones se actualizan

Este flujo será uno de los principales escenarios utilizados para demostrar la arquitectura durante una entrevista técnica.

34. Nombre del proyecto
MicroCredit — Enterprise Lending Platform

Repositorio:

microcredit-platform

Descripción corta:

Enterprise lending platform built with DDD, NestJS, Angular, PostgreSQL, Transactional Outbox, Debezium CDC, AWS SNS/SQS, Lambda, DynamoDB and Kubernetes.


---
*Este sería mi docs/01-project-overview.md. A partir de aquí, el documento que más conviene construir es 02-domain-model.md: ahí definiremos los Bounded Contexts, agregados, entidades, Value Objects, estados, invariantes y eventos. Esa pieza será la base para diseñar PostgreSQL y la estructura de Nx sin tomar decisiones prematuras.*