# Comparación de alternativas estructurales — Rutify

## Contexto de la decisión

Rutify necesita incorporar la ventana horaria de entrega sin romper la compatibilidad con versiones anteriores de la app, y mantener el rendimiento (RNF-01) y la disponibilidad (RNF-02) durante picos de campaña. La pregunta no es qué tecnología usar, sino **cómo estructurar el sistema** para que ese cambio sea seguro y el sistema pueda crecer sin acoplarse innecesariamente.

## Opción A — Monolito modular con límites claros

Un único despliegue, pero organizado en módulos internos con fronteras explícitas: Pedidos, Inventario, Ruteo, Entregas. Cada módulo expone una interfaz clara hacia los demás, aunque todos corran en el mismo proceso.

**Ventajas:**
- Desarrollo e implementación más rápidos para el estado actual del negocio.
- Menor complejidad operativa: un solo despliegue, un solo pipeline de CI/CD.
- Trazabilidad más simple (los logs y el flujo de un pedido viven en un mismo sistema).
- Permite validar el nuevo requisito (ventana de entrega) sin necesidad de coordinar múltiples servicios.

**Desventajas:**
- Si los límites entre módulos no se respetan desde el código, hay riesgo de acoplamiento con el tiempo.
- Puede requerir un refactor futuro si algún módulo (por ejemplo, ruteo) empieza a consumir muchos más recursos que el resto.

## Opción B — Arquitectura distribuida por dominios y servicios

Cada dominio (Pedidos, Inventario, Ruteo, Entregas) se despliega como un servicio independiente, con su propia base de datos y comunicándose por contratos (APIs o eventos).

**Ventajas:**
- Mejor separación real entre dominios, cada uno puede evolucionar y escalar por separado.
- Permite que equipos distintos trabajen cada módulo sin pisarse.
- Facilita integrar nuevos proveedores externos (mapas, pagos) de forma aislada.

**Desventajas:**
- Mayor complejidad operativa: múltiples despliegues, monitoreo distribuido, manejo de fallos de red entre servicios.
- Requiere más coordinación e infraestructura (service discovery, versionado de contratos entre servicios, resiliencia ante fallos parciales).
- Riesgo de sobrediseño: para el volumen actual de Rutify, se estaría pagando el costo de la distribución sin tener todavía la necesidad real de escalar cada dominio por separado.

## Comparación directa

| Criterio | Opción A — Monolito modular | Opción B — Arquitectura distribuida |
|----------|------------------------------|----------------------------------------|
| Velocidad de implementación | Alta | Baja |
| Complejidad operativa | Baja | Alta |
| Facilidad para mantener compatibilidad (RNF-03) | Alta (un solo contrato que versionar) | Media (hay que versionar contratos entre varios servicios) |
| Rendimiento en picos de campaña (RNF-01, RNF-02) | Aceptable si se optimiza el módulo de ruteo | Mejor en teoría, pero añade latencia de red entre servicios |
| Riesgo de acoplamiento (RNF-05) | Medio (depende de disciplina en el código) | Bajo (aislamiento forzado por el despliegue) |
| Costo de evolución actual | Bajo | Alto |
| Nivel de madurez del equipo/operación necesario | Bajo-medio | Alto |

## Decisión

Se elige la **Opción A: Monolito modular con límites claros entre dominios** (Pedidos, Inventario, Ruteo, Entregas), evitando distribuir el sistema en microservicios de forma prematura.

**Razón principal:** el reto actual de Rutify (agregar la ventana de entrega sin romper clientes antiguos, y sostener picos de campaña) no requiere escalar los dominios de forma independiente todavía. Separar en servicios ahora añadiría complejidad operativa real sin evidencia de que el sistema la necesite. La decisión correcta es resolver el problema con buenos límites internos y revisar la separación en el futuro, solo si las métricas (por ejemplo, saturación del módulo de ruteo) lo justifican — siguiendo la misma lógica de evolución por evidencia que se vio en el roadmap arquitectónico (horizontes 1, 2 y 3).
