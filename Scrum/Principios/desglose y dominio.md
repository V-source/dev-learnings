## 1. Principio de Dependencia Técnica (Bloqueadores)

Este principio se enfoca en la **secuencia obligatoria** de las tareas basada en los requisitos de infraestructura o código. Si la Tarea A necesita que exista la Tarea B, entonces la Tarea B tiene una prioridad más alta.

### Puntos Importantes

- **Identificación de Bloqueadores:** Cualquier tarea que introduzca una **dependencia externa** (librería, servicio, API) o cree un componente que otros necesitan usar (como un Repositorio o una entidad central).
- **Fundación y Estabilidad:** Las tareas de **Infraestructura** (configuración de _deploy_, bases de datos, _error handling_ general) y los **módulos base** (como el Repositorio de Notificaciones) siempre tienen prioridad, ya que garantizan la estabilidad y la posibilidad de construir encima.
- **Dependencia Estricta:** Las tareas que consumen datos o lógica de otras tareas deben esperar a que el proveedor de datos/lógica esté **terminado y probado**.

### Para Dominarlo (Puesta en Práctica)

1.  **Mapeo de Prerrequisitos:** Antes de priorizar, dibuja (o lista) cada _Feature_ y la funcionalidad de la que depende. Usa flechas para visualizar el flujo.
2.  **Definir Interfaces Primero:** En la capa de **Aplicación**, define las **Interfaces de los Repositorios/Gateways** que necesitarás. Esto permite que los Casos de Uso se escriban de forma paralela, aunque la implementación concreta (Infraestructura) sea la prioridad 1.
3.  **Priorizar la Integración Externa:** Si dependes de un proveedor externo (ej. Expo, Stripe), la tarea de **integrar y encapsular** esa librería en un **Gateway** debe ser la primera para mitigar el riesgo de incompatibilidad.

---

## 2. Principio de Valor Mínimo Viable (MVF)

Este principio se enfoca en entregar la **menor cantidad de trabajo** posible que genere el **mayor valor** o beneficio para el usuario final o el negocio.

### Puntos Importantes

- **Enfoque en el Usuario:** ¿Qué acción o _Feature_ le permite al usuario **resolver su problema principal**? (Ejemplo: el valor principal de un servicio de notificaciones es **`Enviar notificaciones`**, no la paginación de notificaciones leídas).
- **Excluir la Perfección:** Evita el _over-engineering_ y las funcionalidades que son "bonitas de tener" (Nice-to-Have). El **MVF** no tiene filtros de búsqueda avanzados ni personalizaciones; solo la funcionalidad _core_ que hace que el producto sea utilizable.
- **Rendimiento y Feedback Rápido:** Entregar valor rápidamente permite obtener _feedback_ temprano para validar si la dirección es correcta.

### Para Dominarlo (Puesta en Práctica)

1.  **Definición Clara del "Hecho":** Para cada _Feature_, pregúntate: "¿Cuál es el mínimo que necesito para que esto sea considerado funcional y útil para el usuario?"
2.  **Técnica MoSCoW:** Clasifica todas las tareas antes de priorizar:
    - **M**ust Have (Imprescindible para el MVF).
    - **S**hould Have (Importante, pero no bloquea el MVF).
    - **C**ould Have (Deseable, si hay tiempo).
    - **W**on't Have (Se pospone).
3.  **Buscar el Camino más Corto al Éxito:** Prioriza las tareas que, con las bases ya creadas (Dependencia Técnica), requieren el menor esfuerzo para alcanzar el estado de MVF.

---

## 3. Principio de Flujo de Datos

Este principio dicta el orden de las tareas siguiendo el **camino lógico** que los datos (o el usuario) recorrerán a través del sistema.

### Puntos Importantes

- **Entrada -> Procesamiento -> Salida:** Prioriza las tareas siguiendo esta secuencia:
  1.  **Creación/Ingreso de Datos:** Tareas que generan o capturan información (`crear notificaciones`, `almacenar cliente`).
  2.  **Procesamiento/Acción:** Tareas que actúan sobre esos datos (`enviar notificaciones`, `Cambio de plan`).
  3.  **Consulta/Salida de Datos:** Tareas que recuperan o presentan la información (`listar notificaciones`, `filtrar clientes`).
- **Completitud del Ciclo:** Es más valioso completar el ciclo de vida de una entidad (ej. Crear -> Actualizar -> Eliminar -> Consultar) antes de empezar el ciclo de otra entidad completamente diferente.

### Para Dominarlo (Puesta en Práctica)

1.  **Definir el Flujo del Caso de Uso:** Escribe el _storyboard_ o el flujo de trabajo principal. Si es un servicio de notificaciones, el flujo principal es: _Crear Contenido -> Enviar -> El Cliente Recibe y Consulta_.
2.  **Evitar Tareas de Salida Prematuras:** No implementes las funcionalidades de **listado, paginación o filtrado** (tareas de Salida) hasta que las funcionalidades de **creación y procesamiento** (Entrada/Procesamiento) estén listas y sepas qué datos exactamente necesitas recuperar.
3.  **Priorizar la "Acción" sobre la "Consulta":** La capacidad de hacer algo con la data (el _cambio_ o la _acción_) siempre tiene más valor inmediato que la capacidad de ver el historial de esa acción.
