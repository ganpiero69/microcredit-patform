1. Regla
Cada funcionalidad se implementará como un Vertical Slice.

Un slice debe atravesar todas las capas necesarias para entregar una funcionalidad de negocio funcional.

No se desarrollará primero una capa completa para después pasar a la siguiente.

❌ Domain completo
❌ Application completa
❌ Infrastructure completa
❌ API completa
❌ Frontend completo

En su lugar:

Feature A
  ↓
Feature B
  ↓
Feature C

Cada feature se completa antes de comenzar la siguiente cuando las prioridades del MVP así lo permitan.

2. Flujo de implementación
Para cada funcionalidad, el agente seguirá este orden:

1. Comprender la necesidad
        ↓
2. Identificar reglas de negocio
        ↓
3. Determinar el modelo mínimo necesario
        ↓
4. Implementar el caso de uso
        ↓
5. Implementar adaptadores necesarios
        ↓
6. Implementar interfaz cuando corresponda
        ↓
7. Validar mediante tests

No se deben crear componentes que la funcionalidad no necesite.

3. Modelo mínimo
Cada slice debe introducir únicamente los conceptos necesarios para resolver su caso de uso.

Evitar:

Modelos especulativos.

Abstracciones prematuras.

Interfaces sin necesidad.

Eventos que todavía no tengan consumidores.

Bounded contexts artificiales.

Infraestructura futura.

La complejidad debe aparecer como consecuencia de las necesidades reales del negocio.

4. Domain First
Cuando una funcionalidad contiene reglas de negocio, estas deben quedar representadas en el dominio y no en controllers, repositories o componentes Angular.

El dominio debe proteger sus invariantes.

Los detalles tecnológicos deben adaptarse al dominio y no modificar sus reglas para facilitar una implementación técnica.

5. Caso de uso
Cada operación relevante del negocio debe tener un caso de uso claro.

Ejemplos:

CreateCustomer
CreateLoan
ApproveLoan
RegisterPayment

El caso de uso coordina la operación sin convertirse en un contenedor de todas las reglas del dominio.

Las reglas pertenecientes al negocio deben permanecer en el modelo correspondiente.

6. Adaptadores
Los detalles externos se implementarán únicamente cuando sean necesarios para completar el slice.

Ejemplos:

HTTP
PostgreSQL
External APIs
Messaging
Frontend

El slice puede atravesar una o varias de estas capas dependiendo de la funcionalidad.

No todas las funcionalidades requieren la misma estructura.

7. Validación
Cada slice debe incluir la validación correspondiente a su nivel.

Como mínimo:

Domain
→ reglas e invariantes

Application
→ comportamiento del caso de uso

Infrastructure
→ integración técnica cuando corresponda

API
→ contrato y comportamiento HTTP cuando corresponda

Frontend
→ comportamiento de la funcionalidad cuando corresponda

La cantidad de tests debe corresponder al riesgo y complejidad de la funcionalidad.

8. Definition of Done
Una funcionalidad está terminada cuando:

✓ Resuelve una necesidad real.
✓ Implementa las reglas necesarias.
✓ Mantiene las invariantes.
✓ Funciona de extremo a extremo cuando corresponde.
✓ Tiene validación suficiente.
✓ No introduce arquitectura especulativa.

El objetivo de un Vertical Slice no es crear muchas capas.

El objetivo es entregar una capacidad de negocio funcional y validada.