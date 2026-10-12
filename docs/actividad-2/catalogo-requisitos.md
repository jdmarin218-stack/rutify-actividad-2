# Catálogo de requisitos y matriz de priorización — Rutify

## 1. Requisitos funcionales

| ID | Requisito |
|----|-----------|
| RF-01 | El cliente puede crear un pedido desde la aplicación móvil. |
| RF-02 | El cliente puede seleccionar una ventana horaria de entrega al confirmar el pedido. |
| RF-03 | El sistema valida la disponibilidad de inventario antes de confirmar el pedido. |
| RF-04 | El sistema asigna una ruta de entrega viable al pedido confirmado. |
| RF-05 | El sistema notifica al cliente los cambios de estado del pedido (confirmado, en ruta, entregado). |
| RF-06 | El cliente puede consultar el estado actual de su pedido en cualquier momento. |
| RF-07 | El equipo de soporte puede consultar el histórico de un pedido para resolver incidencias. |

## 2. Requisitos no funcionales (de calidad)

| ID | Requisito |
|----|-----------|
| RNF-01 | **Rendimiento:** el 95% de las solicitudes de creación de pedido deben responder en menos de 2 segundos. |
| RNF-02 | **Disponibilidad:** el sistema debe mantenerse operativo durante picos de campaña con hasta 20 veces el tráfico normal. |
| RNF-03 | **Compatibilidad/evolución:** las versiones anteriores de la app móvil deben seguir funcionando aunque cambie el contrato de creación de pedidos. |
| RNF-04 | **Seguridad:** los datos personales de clientes y repartidores deben protegerse y accederse solo con rol autorizado. |
| RNF-05 | **Mantenibilidad:** los módulos deben mantener bajo acoplamiento, de modo que un cambio en una regla de negocio (ej. ventana de entrega) no obligue a modificar otros módulos. |
| RNF-06 | **Escalabilidad:** el cálculo de rutas debe poder escalar de forma independiente si se convierte en el cuello de botella durante campañas. |

## 3. Matriz de priorización

| Requisito | Prioridad | Impacto en negocio | Impacto en arquitectura | Complejidad |
|-----------|-----------|---------------------|--------------------------|--------------|
| RF-01 Crear pedido | Alta | Alto | Alto | Media |
| RF-02 Ventana de entrega | Media | Medio | Alto | Media |
| RF-03 Validar inventario | Alta | Alto | Alto | Media |
| RF-04 Asignar ruta | Alta | Alto | Alto | Alta |
| RF-05 Notificar estado | Media | Medio | Medio | Baja |
| RF-06 Consultar estado | Media | Medio | Bajo | Baja |
| RF-07 Histórico para soporte | Baja | Bajo | Medio | Baja |
| RNF-01 Rendimiento (<2s) | Alta | Alto | Alto | Alta |
| RNF-02 Disponibilidad en picos | Alta | Alto | Alto | Alta |
| RNF-03 Compatibilidad con versiones antiguas | Alta | Alto | Alto | Alta |
| RNF-04 Seguridad | Alta | Alto | Medio | Media |
| RNF-05 Mantenibilidad / bajo acoplamiento | Alta | Alto | Alto | Media |
| RNF-06 Escalabilidad del ruteo | Media | Medio | Alto | Alta |

### Lectura rápida

Los requisitos de **alta prioridad** (crear pedido, validar inventario, asignar ruta, rendimiento, disponibilidad en picos, compatibilidad con versiones antiguas, seguridad y mantenibilidad) son los que realmente definen la arquitectura: exigen una estructura que responda rápido, que no se caiga en campaña, y que no obligue a romper clientes antiguos cada vez que se agrega una regla de negocio.

El requisito RF-02 (ventana de entrega) es de prioridad **media** en sí mismo —no es el más crítico del negocio—, pero es el que **dispara el reto arquitectónico**: agregarlo sin romper la compatibilidad (RNF-03) y sin afectar el rendimiento en campaña (RNF-01, RNF-02) es lo que realmente hay que resolver con la decisión estructural.
