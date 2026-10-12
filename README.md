# Rutify — Requisitos y decisión estructural

## Resumen del problema

Rutify ya opera creando pedidos a través de su aplicación móvil, validando inventario y asignando rutas. El negocio ahora necesita que el cliente pueda elegir una ventana horaria de entrega al confirmar su pedido, y que el sistema siga respondiendo con estabilidad cuando el tráfico se dispara durante campañas.

El reto no es simplemente agregar un campo nuevo: hay clientes usando versiones anteriores de la app que no se actualizan de inmediato, así que cualquier cambio en el contrato de creación de pedidos debe convivir con ellos sin romperles la experiencia. Y cuando llega una campaña, el módulo que más sufre es el cálculo de rutas, lo que no puede terminar bloqueando la confirmación de pedidos para el resto de los clientes.

## Requisitos prioritarios

De todo el catálogo de requisitos, los que definen la arquitectura son: crear el pedido, validar inventario, asignar una ruta viable, responder en menos de 2 segundos en el 95% de los casos, mantener compatibilidad con versiones anteriores de la app, proteger los datos sensibles de clientes y repartidores, y mantener los módulos lo bastante desacoplados para que un cambio en la regla de ventana de entrega no obligue a tocar inventario, rutas o notificaciones.

## Decisión arquitectónica

Se adopta un **monolito modular con límites claros entre dominios** (Pedidos, Inventario, Ruteo, Entregas), en lugar de distribuir el sistema en microservicios independientes.

La ventana de entrega se incorpora como un campo **opcional** en el contrato de creación de pedidos, de modo que las apps antiguas sigan funcionando sin cambios. El módulo de Ruteo queda aislado detrás de una interfaz propia, para que una falla o lentitud del proveedor externo de mapas no bloquee la confirmación de pedidos durante un pico de campaña.

Se descartó una arquitectura distribuida por dominios porque, para el volumen actual de Rutify, añadiría complejidad operativa sin evidencia de que el sistema la necesite. La separación se reconsiderará en el futuro si las métricas (por ejemplo, saturación del módulo de Ruteo) lo justifican.

El detalle completo de la decisión, las alternativas descartadas y los criterios de revisión está documentado en el [ADR-001](docs/actividad-2/adr-decision-estructural.md).

*(El catálogo completo de requisitos, la matriz de priorización, los escenarios de calidad y la comparación de alternativas están en `/docs/actividad-2`.)*
