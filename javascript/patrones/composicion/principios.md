
# 🧱 ESTRUCTURA GENERAL (conceptualmente)

```scss
[Módulos reutilizables]  → capacidades (canX)
[Base o estado]          → datos comunes (id, name, etc.)
[Función compositora]    → une base + módulos
[Objeto final]           → entidad con capacidades dinámicas
```





# ANATOMIA DEL PATRON DE COMPOSICION.


Entender la **anatomía del patrón de composición** es clave para dominar su aplicación — especialmente en JavaScript, donde se puede usar de forma muy natural gracias a las *funciones de orden superior* y los *objetos literales*.

Vamos a desmenuzarlo paso a paso 👇

---

## 🧩 1. Qué es el patrón de composición (en una frase)

> **El patrón de composición** consiste en construir objetos complejos **combinando pequeños comportamientos independientes**, en lugar de depender de una jerarquía de clases o herencia.

Dicho simple:
en lugar de decir “A *es un tipo de* B” (herencia), decimos
“A *tiene las capacidades de* X, Y y Z” (composición).

---

## ⚙️ 2. Anatomía del patrón

Podemos ver su estructura como **cinco piezas esenciales**:

| Elemento                             | Qué representa                                                        | Ejemplo                                                |
| ------------------------------------ | --------------------------------------------------------------------- | ------------------------------------------------------ |
| **1. Módulos o comportamientos**     | Pequeñas funciones puras que agregan una habilidad concreta.          | `const canFly = (obj) => ({ fly: () => ... })`         |
| **2. Base o estado inicial**         | Objeto inicial o datos comunes que todos los compuestos comparten.    | `{ name: "Bird" }`                                     |
| **3. Función compositora (factory)** | Une el estado base con varios módulos.                                | `return { ...base, ...canFly(base), ...canEat(base) }` |
| **4. Decoradores (opcional)**        | Añaden o modifican comportamientos dinámicamente.                     | `const withLogging = (obj) => ({ ...obj, log: ... })`  |
| **5. Objeto resultante**             | La combinación final: un objeto con todas las capacidades necesarias. | `{ name: "Bird", fly: [Function], eat: [Function] }`   |

---

## 🧬 3. Estructura visual

```
🧱 Pequeños módulos de comportamiento
   ↓
function canEat(entity) → { eat() {...} }
function canFly(entity) → { fly() {...} }

🏗️ Función compositora
   ↓
function createBird(name) {
  const bird = { name };
  return {
    ...bird,
    ...canEat(bird),
    ...canFly(bird)
  };
}

🚀 Objeto final (ya compuesto)
   ↓
const bird = createBird("Gaviota");
bird.fly();
bird.eat();
```

---

## 🧠 4. Principios que sostienen la composición

1. **Separación de responsabilidades:**
   Cada módulo hace una sola cosa y la hace bien.

2. **Bajo acoplamiento:**
   Los módulos no dependen unos de otros ni de una jerarquía común.

3. **Alta cohesión:**
   Las funciones relacionadas entre sí viven juntas en un mismo módulo.

4. **Reutilización flexible:**
   Los módulos pueden combinarse libremente para crear nuevos comportamientos.

5. **Sustituibilidad:**
   Puedes reemplazar o quitar módulos sin romper el sistema.

---

## 🧱 5. Ejemplo ultra simple: anatomía en acción

```js
// Módulos (cada uno aporta una capacidad)
const canWalk = (entity) => ({
  walk: () => console.log(`${entity.name} está caminando.`)
});

const canTalk = (entity) => ({
  talk: () => console.log(`${entity.name} dice hola.`)
});

const canSleep = (entity) => ({
  sleep: () => console.log(`${entity.name} está durmiendo.`)
});

// Base (datos comunes)
const createEntity = (name) => ({ name });

// Composición (factory)
function createHuman(name) {
  const entity = createEntity(name);
  return {
    ...entity,
    ...canWalk(entity),
    ...canTalk(entity),
    ...canSleep(entity),
  };
}

// Objeto compuesto
const human = createHuman("Elvis");
human.walk();
human.talk();
human.sleep();
```

---

## ⚔️ 6. Diferencia conceptual con la herencia

| Aspecto       | Herencia                      | Composición                             |
| ------------- | ----------------------------- | --------------------------------------- |
| Estructura    | Jerárquica, en árbol          | Plana, en bloques combinables           |
| Relación      | “Es un tipo de”               | “Tiene la capacidad de”                 |
| Reutilización | Vertical (de padre a hijo)    | Horizontal (entre módulos)              |
| Flexibilidad  | Baja (rígida)                 | Alta (modular)                          |
| Ejemplo       | `class Dog extends Animal {}` | `createDog = { ...canBark, ...canEat }` |

---

## 🧩 7. Patrón mental clave

> Piensa en composición como **"Lego para objetos"**:
> cada bloque añade una función, y tú decides cómo ensamblarlos según la necesidad.

---

