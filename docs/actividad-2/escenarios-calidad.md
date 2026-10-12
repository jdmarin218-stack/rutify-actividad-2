# Escenarios de calidad — Rutify

Formato: **Estímulo / Condición / Respuesta medible**, aplicado a los requisitos no funcionales más críticos para la decisión estructural.

## Escenario 1 — Creación de pedido en condiciones normales

- **Estímulo:** un cliente confirma un pedido desde la app móvil, seleccionando una ventana horaria de entrega.
- **Condición:** tráfico normal, inventario disponible.
- **Respuesta medible:** el sistema confirma el pedido en menos de 2 segundos en el 95% de los casos, con la ventana de entrega correctamente registrada.

## Escenario 2 — Pico de campaña

- **Estímulo:** múltiples clientes crean pedidos simultáneamente durante una campaña de alto tráfico.
- **Condición:** el volumen de solicitudes aumenta hasta 20 veces el tráfico normal.
- **Respuesta medible:** el sistema sigue respondiendo dentro del umbral de 2 segundos en el 95% de las solicitudes, sin caídas del servicio de creación de pedidos, aunque el cálculo de rutas se vea más lento.

## Escenario 3 — App móvil desactualizada

- **Estímulo:** un cliente con una versión anterior de la app (sin soporte para ventana de entrega) crea un pedido.
- **Condición:** el cliente no se ha actualizado y no envía el campo de ventana horaria.
- **Respuesta medible:** el pedido se crea exitosamente sin ventana de entrega asignada (valor nulo/opcional), sin error ni rechazo de la solicitud.

## Escenario 4 — Falla del proveedor de mapas

- **Estímulo:** el proveedor externo de mapas responde con latencia alta o no disponible.
- **Condición:** ocurre durante la asignación de ruta de un pedido ya confirmado.
- **Respuesta medible:** la falla del proveedor de mapas no bloquea la creación ni confirmación del pedido; el sistema reintenta o marca la ruta como pendiente, con un timeout máximo de 2 segundos antes de responder al cliente.

## Escenario 5 — Consulta de soporte

- **Estímulo:** un agente de soporte necesita revisar el histórico de un pedido para resolver una incidencia.
- **Condición:** el pedido tiene varios cambios de estado registrados.
- **Respuesta medible:** el histórico completo del pedido se recupera en menos de 3 segundos, sin necesidad de consultar directamente la base de datos de otros módulos (inventario, ruteo).

## Escenario 6 — Cambio en la regla de ventana de entrega

- **Estímulo:** el negocio decide modificar la regla de la ventana horaria (por ejemplo, agregar franjas nocturnas).
- **Condición:** el cambio se implementa en el módulo de pedidos.
- **Respuesta medible:** el cambio no requiere modificar el módulo de inventario, ruteo ni notificaciones; se despliega de forma aislada y verificable mediante pruebas automatizadas.
