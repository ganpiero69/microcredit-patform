1. Estrategia

El desarrollo será incremental y orientado al MVP.

No se diseñará anticipadamente todo el dominio, todos los bounded contexts ni toda la infraestructura. El modelo evolucionará conforme se descubran y validen nuevas reglas de negocio.

La prioridad será:

MODELO DE NEGOCIO
↓
REGLAS DE NEGOCIO
↓
RELACIONES E INVARIANTES
↓
CASOS DE USO
↓
DISEÑO
↓
CÓDIGO


Nunca se debe comenzar diseñando código, base de datos o infraestructura sin comprender primero la necesidad de negocio.

2. Alcance inicial

El MVP comenzará con las capacidades necesarias para validar el producto.

Modelos iniciales:

Customer
Loan
Payment


Estos modelos son un punto de partida y podrán evolucionar.

No deben crearse anticipadamente entidades, agregados, eventos, bounded contexts o abstracciones que todavía no sean necesarios.

3. Orden de implementación

Cada funcionalidad se priorizará según valor para el MVP.

Un orden inicial de referencia es:

1. Customer
2. Loan
3. Payment


Las funcionalidades concretas dentro de cada capacidad se determinarán progresivamente.

Por ejemplo:

Customer
  ↓
Loan
  ↓
Payment


El orden podrá cambiar si las reglas o prioridades del negocio lo requieren.

4. Evolución del dominio

Después de cada funcionalidad se revisará el conocimiento obtenido.

Podrán aparecer nuevos:

Conceptos.

Reglas.

Relaciones.

Invariantes.

Value Objects.

Entidades.

Agregados.

Eventos.

Bounded contexts.

Solo se incorporarán cuando exista una necesidad real.

Los bounded contexts iniciales son hipótesis y podrán dividirse, fusionarse o reorganizarse.

5. Infraestructura bajo demanda

La infraestructura se implementará cuando una funcionalidad la necesite.

No se debe construir anticipadamente:

Queues
Workers
Redis
Event Bus
Caching
AWS Services


si todavía no existe un caso de uso que lo justifique.

La arquitectura debe estar preparada para evolucionar, pero la implementación debe responder a necesidades actuales.

6. Criterio para cada incremento

Antes de comenzar una nueva funcionalidad debe existir una necesidad de negocio clara.

El ciclo será:

Necesidad
↓
Implementación
↓
Validación
↓
Aprendizaje
↓
Refinamiento
↓
Siguiente incremento


El objetivo es maximizar aprendizaje y valor entregado, evitando trabajo especulativo.