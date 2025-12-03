### **Sistema: "Semáforo de Priorización"**

#### **1. Criterios Claros para Clasificar**

Usa esta tabla como guía para asignar prioridades. **Pregúntate esto por cada tarea**:

Criterios para Priorizar

| Prioridad   | Preguntas Clave                                                                                        | Ejemplo en Node.js + MongoDB                                                                                  |
| ----------- | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- |
| **🔴 Alta** | - ¿Bloquea a otras tareas?<br>- ¿Es esencial para el MVP?<br>- ¿El proyecto no puede avanzar sin ella? | - Configurar conexión a MongoDB.<br>- Crear API de autenticación.<br>- Resolver un bug crítico en producción. |

| Prioridad    | Preguntas Clave                                                                                                            | Ejemplo en Node.js + MongoDB                                                                                         |
| ------------ | -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **🟡 Media** | - ¿Aporta valor pero no es urgente?<br>- ¿Mejora la experiencia pero no es crítica?<br>- ¿Puede esperar al próximo Sprint? | - Optimizar consultas a MongoDB.<br>- Implementar caché para reducir latencia.<br>- Añadir validaciones adicionales. |

| Prioridad   | Preguntas Clave                                                                                                              | Ejemplo en Node.js + MongoDB                                                                         |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| **🟢 Baja** | - ¿Es un "nice-to-have"?<br>- ¿No resuelve un problema directo del usuario?<br>- ¿Requiere mucho esfuerzo para poco impacto? | - Refactorizar código sin bugs.<br>- Documentación detallada.<br>- Pruebas de rendimiento avanzadas. |

---

#### **2. Flujo de Trabajo (Paso a Paso)**

1. **Lista todas las tareas** del proyecto en un tablero (Trello, Notion, o incluso post-its).
2. **Aplica los criterios** de la tabla anterior a cada tarea.
3. **Asigna un color**:
   - **🔴 Rojo**: Alta.
   - **🟡 Amarillo**: Media.
   - **🟢 Verde**: Baja.
4. **Revisa con el equipo** (si hay uno) para validar la clasificación.
5. **Ajusta cada semana** en tu reunión de planificación.

---

**Ejemplo visual en Trello:**

- **Columna "🔴 Alta"**: Tareas con etiqueta roja.
- **Columna "🟡 Media"**: Tareas con etiqueta amarilla.
- **Columna "🟢 Baja"**: Tareas con etiqueta verde.

---

#### **3. Plantilla Rápida para Clasificar**

Usa esta **lista de verificación** para cada tarea nueva:

Checklist de Priorización

| Tarea | Preguntas                                      | 🔴 Alta | 🟡 Media | 🟢 Baja |
|- | --------------------------------------------- | ------- | -------- | ------- |
| tarea 1 | ¿El proyecto no puede avanzar sin esta tarea? | ✅      |          |         |
| | ¿Resuelve un problema crítico del usuario?    | ✅      | ✅       |         |
| | ¿Bloquea a otras tareas del equipo?           | ✅      |          |         |
| | ¿Es una mejora que puede esperar?             |         | ✅       | ✅      |
| | ¿El esfuerzo es alto pero el impacto es bajo? |         |          | ✅      |
| | ¿Es parte del MVP (Producto Mínimo Viable)?   | ✅      | ✅       |         |

---

**Instrucciones:**

- Si hay **2 o más ✅ en 🔴 Alta**, la tarea es **Alta**.
- Si hay **2 o más ✅ en 🟡 Media**, la tarea es **Media**.
- Si hay **2 o más ✅ en 🟢 Baja**, la tarea es **Baja**.

---

#### **4. Ejemplo Práctico**

**Tarea:** _"Implementar búsqueda por texto en MongoDB"_

- ¿Bloquea a otras tareas? **No**.
- ¿Es esencial para el MVP? **Depende** (si el MVP requiere búsqueda, sí; si no, no).
- ¿Resuelve un problema crítico? **Sí** (si los usuarios necesitan buscar datos).
- ¿Puede esperar? **No** (si es crítica para el flujo del usuario).

**Resultado:** **🔴 Alta** (si es esencial para el MVP) o **🟡 Media** (si es una mejora posterior).

---

#### **5. Herramientas Recomendadas**

- **Trello/Notion**: Para tableros visuales.
- **Google Sheets**: Para llevar un registro histórico de prioridades.
- **Script en Node.js**: Automatiza la creación de issues en GitHub/GitLab por prioridad (ej: prefijo `[ALTA]` en el título).

---

**Ejemplo de script para GitHub:**

```javascript
// Ejemplo para crear un issue con prioridad en GitHub
const { Octokit } = require("@octokit/rest");
const octokit = new Octokit({ auth: "TU_TOKEN" });

async function crearIssue(titulo, prioridad) {
  await octokit.request("POST /repos/{owner}/{repo}/issues", {
    owner: "tu_usuario",
    repo: "tu_proyecto",
    title: `[${prioridad}] ${titulo}`,
    body: `Prioridad: ${prioridad}`,
  });
}

crearIssue("Configurar conexión a MongoDB", "ALTA");
```

---

#### **6. Consejos para Perfeccionar el Sistema**

- **Revisa las prioridades cada semana**: Lo que es urgente hoy puede no serlo mañana.
- **Involucra al equipo**: La priorización es más efectiva si todos están alineados.
- **Mide el impacto**: Después de cada Sprint, revisa si las tareas **🔴 Alta** realmente resolvieron problemas críticos.

---

**¿Qué necesitas ahora?**

- ¿Quieres que desarrolle una plantilla en Trello o Notion para tu proyecto?
- ¿O prefieres un ejemplo más detallado con tareas específicas de tu backend?
