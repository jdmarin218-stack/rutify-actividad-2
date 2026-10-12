# Criterios verificables — Rutify

Estos son los criterios concretos para comprobar que la decisión estructural (ADR-001) resuelve el problema planteado.

## 1. Compatibilidad con versiones anteriores

- Una app desactualizada puede crear un pedido sin enviar la ventana de entrega, y el pedido se confirma correctamente (sin error, sin rechazo).
- Una app actualizada puede crear un pedido enviando la ventana de entrega, y esta queda correctamente registrada.

**Cómo se comprueba:** pruebas automatizadas que simulan ambos escenarios (con y sin el campo de ventana de entrega) contra el mismo endpoint de creación de pedidos.

## 2. Rendimiento bajo carga normal y en campaña

- El 95% de las solicitudes de creación de pedido responde en menos de 2 segundos, tanto en tráfico normal como en picos de campaña (hasta 20x).

**Cómo se comprueba:** pruebas de carga (por ejemplo, con k6 o JMeter) que simulan el volumen normal y el pico de campaña, midiendo el percentil 95 del tiempo de respuesta.

## 3. Aislamiento del módulo de Ruteo

- Si el proveedor externo de mapas falla o responde lento, la creación y confirmación del pedido no se bloquea.

**Cómo se comprueba:** prueba de falla simulada (mockear el proveedor de mapas con timeout o error) y verificar que el pedido se confirma igual, quedando la ruta como pendiente o reintentando en segundo plano.

## 4. Bajo acoplamiento entre módulos

- Un cambio en la regla de ventana de entrega (por ejemplo, agregar nuevas franjas horarias) no requiere modificar código de Inventario, Ruteo ni Notificaciones.

**Cómo se comprueba:** revisión de los commits asociados a un cambio de este tipo — deben tocar únicamente el módulo de Pedidos.

## 5. Seguridad de los datos

- Solo usuarios con el rol adecuado pueden consultar el histórico completo de un pedido (dato sensible de soporte).

**Cómo se comprueba:** prueba de acceso con un usuario sin el rol correspondiente, verificando que la solicitud es rechazada.

## 6. Evidencia para revisar la decisión a futuro

- Se registra el porcentaje de solicitudes de Ruteo que superan los 2 segundos, como métrica para decidir si en el futuro se justifica extraer ese dominio como servicio independiente.

**Cómo se comprueba:** dashboard o log con esta métrica disponible y revisable por el equipo.
