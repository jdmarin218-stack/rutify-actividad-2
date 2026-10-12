# ADR-001: Estructura del sistema para soportar ventana de entrega y picos de campaña

## Estado
Aceptado

## Contexto

Rutify necesita que el cliente pueda seleccionar una ventana horaria de entrega al confirmar un pedido, sin romper la experiencia de los clientes que usan versiones anteriores de la app. Además, el sistema debe mantenerse estable cuando el tráfico se multiplica durante campañas (hasta 20 veces el volumen normal), especialmente en el módulo de cálculo de rutas, que es el más sensible a la carga.

No se trata solo de agregar un campo nuevo: cualquier cambio en el contrato de creación de pedidos debe convivir con clientes que no se han actualizado, y el crecimiento del tráfico no puede comprometer la confirmación de pedidos para el resto de los usuarios.

## Decisión

Se adopta un **monolito modular con límites claros entre dominios** (Pedidos, Inventario, Ruteo, Entregas), en lugar de distribuir el sistema en microservicios independientes.

El campo de ventana de entrega se agrega como un **atributo opcional/nulo** en el contrato de creación de pedidos: las apps actualizadas lo envían y lo usan; las apps antiguas simplemente no lo envían y el pedido se crea igual, sin ventana asignada.

El módulo de Ruteo se aísla internamente detrás de una interfaz clara (por ejemplo, un `RouteEstimator`), de modo que si el proveedor externo de mapas falla o responde lento durante un pico de campaña, eso no bloquee la creación ni confirmación de pedidos en los demás módulos.

## Alternativas consideradas

**Opción descartada — Arquitectura distribuida por dominios y servicios:** separar cada dominio como un servicio independiente con su propia base de datos. Se descartó porque, para el volumen y la necesidad actual de Rutify, añadiría complejidad operativa (despliegues múltiples, monitoreo distribuido, versionado de contratos entre servicios) sin evidencia de que el sistema la necesite todavía. Es una opción válida a futuro, no ahora.

**Opción descartada — Forzar actualización de la app para el nuevo campo:** exigir que todos los clientes actualicen la app antes de habilitar la ventana de entrega. Se descartó porque rompe la experiencia de los usuarios con versiones antiguas y no es una solución realista para una base de clientes activa.

## Costo aceptado

Se acepta que, al mantener todo en un monolito modular, la disciplina de mantener los límites entre módulos depende del equipo y del código (no hay un aislamiento forzado por infraestructura como en microservicios). Si no se respetan esos límites, existe el riesgo de acoplamiento progresivo entre Pedidos, Inventario, Ruteo y Entregas.

## Condición de revisión

Esta decisión se revisará si:
- El módulo de Ruteo satura de forma recurrente durante campañas y el 95% de las solicitudes supera los 2 segundos de respuesta.
- El equipo necesita escalar o desplegar un dominio de forma independiente del resto.

Cuando eso ocurra, se evaluará extraer ese dominio específico (probablemente Ruteo) como un servicio independiente, manteniendo el resto del sistema como monolito modular — siguiendo una evolución guiada por evidencia, no por moda tecnológica.

## Responsable

Equipo de arquitectura de Rutify (revisión periódica según métricas de rendimiento y disponibilidad).
