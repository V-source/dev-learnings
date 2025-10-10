
---

## 🧩 **Métodos de Documento (`schema.methods`)**

> Se aplican a instancias específicas de documentos creados con el modelo.

### Casos de uso comunes:

1. **Validar contraseñas** antes de autenticar un usuario.
2. **Cifrar datos sensibles** (como tokens o contraseñas) antes de guardar.
3. **Generar tokens JWT** personalizados por usuario.
4. **Verificar roles o permisos** del documento actual.
5. **Actualizar estados** (por ejemplo, marcar una orden como “enviada” o “cancelada”).
6. **Comparar versiones o revisiones** entre documentos.
7. **Aplicar lógica de negocio específica del documento**, como calcular el total de un carrito.
8. **Normalizar o transformar datos** antes de enviarlos en una respuesta (por ejemplo, ocultar campos internos).
9. **Registrar actividad** (por ejemplo, “último inicio de sesión” o “última modificación”).
10. **Generar URLs o slugs** a partir de campos del documento (por ejemplo, `titulo → /articulo/mi-titulo`).
11. **Sincronizar datos relacionados** (por ejemplo, actualizar la disponibilidad del stock cuando se guarda una venta).
12. **Controlar transiciones de estado** (por ejemplo, pasar de “pendiente” a “aprobado”).
13. **Calcular propiedades derivadas** (por ejemplo, edad a partir de la fecha de nacimiento).
14. **Generar notificaciones** asociadas a cambios del documento (por ejemplo, enviar correo al actualizar el estado).
15. **Verificar relaciones** (por ejemplo, si un usuario pertenece a cierto grupo o proyecto).
16. **Auditorías locales**, guardando un historial dentro del propio documento.
17. **Implementar comportamiento de autosalvado** con validaciones personalizadas.
18. **Formatear datos para salida JSON o APIs REST**.
19. **Actualizar valores compuestos** (por ejemplo, promedio de calificaciones del usuario).
20. **Aplicar lógica condicional** en función de los atributos del documento actual.

---

## 🧠 **Métodos Estáticos (`schema.statics`)**

> Se aplican al **modelo completo** y permiten lógica que actúa sobre muchos documentos o el conjunto de datos.

### Casos de uso comunes:

1. **Buscar usuarios por criterios complejos** (por nombre, rol, estado, etc.).
2. **Autenticación** (buscar usuario por email y verificar contraseña).
3. **Validar unicidad de datos** antes de crear un nuevo documento.
4. **Realizar consultas agregadas** o reportes globales (por ejemplo, ventas totales).
5. **Obtener estadísticas o resúmenes** (por ejemplo, número de usuarios activos).
6. **Eliminar múltiples registros** con cierta condición (por ejemplo, limpiar datos viejos).
7. **Implementar búsquedas avanzadas** (filtros, paginación, ordenamientos).
8. **Inicializar datos de sistema** (por ejemplo, crear roles por defecto).
9. **Importar o sincronizar datos externos** con la base de datos.
10. **Implementar funciones de utilidad** comunes a toda la colección.
11. **Calcular rankings o posiciones** (por ejemplo, usuarios con más puntos).
12. **Controlar procesos por lote** (por ejemplo, actualizar estados en masa).
13. **Verificar relaciones entre múltiples documentos** (por ejemplo, usuarios conectados a proyectos).
14. **Lógica administrativa o de mantenimiento** (limpieza, migraciones, backups).
15. **Buscar documentos por índices secundarios** o campos virtuales.
16. **Integrar servicios externos**, como APIs o bases de datos paralelas.
17. **Controlar la creación de instancias** (por ejemplo, patrón fábrica para crear documentos con reglas).
18. **Registrar auditorías globales** de cambios o accesos.
19. **Establecer procesos de verificación previos a operaciones masivas**.
20. **Definir comportamientos reutilizables** en todos los documentos del modelo.

---

¿Quieres que te prepare después una **tabla comparativa** (tipo “cuándo usar uno u otro” + ejemplos conceptuales)?
Eso te ayudaría a visualizar las diferencias con claridad y aplicar el conocimiento en tus propios modelos.
