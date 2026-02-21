# Clase 01: Tipos Básicos - TypeScript vs JavaScript

> **Método MAAR:** Aprendizaje por Contraste y Laboratorio de Errores

---

## 🗺️ HOJA DE RUTA: Los 5 Pilares

| Pilar | JavaScript | TypeScript |
|-------|------------|------------|
| **Variables** | Dinámico (`let x = 5; x = "hola"`) | Estático (`let x: number = 5`) |
| **Control** | Igual | Igual + type guards |
| **Colecciones** | `Array`, `Object` | `Array<T>`, interfaces, `Record<K,V>` |
| **Funciones** | Sin tipos en params | Params tipados + return types |
| **Runtime** | Node/Browser directo | Compila a JS primero |

---

## 🔄 SINTAXIS EN ESPEJO: Tipos Primitivos

### JavaScript (Dinámico - Sin restricciones)

```javascript
// JS: Una variable puede cambiar de tipo
let edad = 25;
edad = "veinticinco";  // ✅ Válido, pero peligroso
edad = true;           // ✅ Válido, pero ¿por qué?

let nombre = "Ana";
let activo = true;
```

### TypeScript (Estático - Tipado explícito)

```typescript
// TS: El tipo se declara y se respeta
let edad: number = 25;
let nombre: string = "Ana";
let activo: boolean = true;

edad = "treinta";  // ❌ ERROR: Type 'string' is not assignable to type 'number'
```

---

## 🔬 LABORATORIO DE ERRORES #1

### Instrucciones:

1. **Crea** un archivo `test.ts` con el siguiente código:

```typescript
let edad: number = 25;
let nombre: string = "Ana";
let activo: boolean = true;

edad = "treinta";  // <- El error está aquí
```

2. **Ejecuta** el compilador:
```bash
npx tsc test.ts --noEmit
```

3. **Lee** el mensaje de error del compilador.

4. **Rompe más:** Intenta estos errores adicionales:
```typescript
nombre = 123;        // ¿Qué pasa?
activo = "si";       // ¿Qué pasa?
let x;               // ¿Qué tipo tiene x? (inferencia)
x = 5;
x = "hola";          // ¿Error o no?
```

---

## 🧠 CONCEPTO CLAVE: Inferencia de Tipos

TypeScript puede **inferir** el tipo sin que lo declares explícitamente:

```typescript
// Con anotación explícita
let edad: number = 25;

// Con inferencia (TS deduce que es number)
let edad = 25;  // TS sabe que es number por el valor inicial

// PERO: Si no hay valor inicial, es 'any' (cualquier tipo)
let x;          // Tipo: any
x = 5;
x = "hola";     // ✅ Válido porque es 'any'
```

---

## ⚠️ NUNCA OLVIDAR: Errores Comunes de Concepto

### ❌ Confusión #1: Type Guard vs Tipado Estático

| Concepto | ¿Qué es? | ¿Cuándo ocurre? | Ejemplo |
|----------|----------|-----------------|---------|
| **Type Guard** | Verificación manual de tipo | **Runtime** (ejecución) | `if (typeof x === 'number')` |
| **Tipado Estático** | Verificación del compilador | **Compile time** (antes de ejecutar) | `let x: number = 5` |

```typescript
// Type Guard (runtime check)
function procesar(valor: unknown) {
    if (typeof valor === 'number') {  // ← Esto es un type guard
        return valor * 2;
    }
}

// Tipado Estático (compile time check)
let edad: number = 25;
edad = "hola";  // ← Error detectado por el compilador, NO en runtime
```

---

### ❌ Confusión #2: ¿El error detiene la ejecución?

**NO.** TypeScript es un "linter con superpoderes":

```bash
# Compilar A PESAR del error
pnpm exec tsc test.ts

# Resultado: Se genera test.js de todas formas
```

```javascript
// test.js generado (JavaScript puro)
var edad = 25;
var nombre = "Ana";
var activo = true;
edad = "treinta";  // ← JavaScript ejecuta esto sin problemas
```

**Conclusión:**
- TypeScript **te advierte**, pero **no impide** que se genere el código JavaScript
- El error es **en tiempo de compilación**, no en runtime
- Si quieres que TS no compile cuando hay errores, usa: `pnpm exec tsc --noEmit`

---

### 📌 Resumen de Diferencias Clave

| Aspecto | TypeScript | JavaScript |
|---------|------------|------------|
| **Verificación de tipos** | Compile time (antes de ejecutar) | Runtime (durante ejecución) |
| **Comportamiento ante error** | Advierte, pero puede seguir compilando | Lanza excepción o falla silenciosamente |
| **Type safety** | Garantizado por el compilador | Responsabilidad del desarrollador |

---

## 📝 DESAFÍO DE RECALL ACTIVO

**Sin mirar arriba**, responde:

1. ¿Qué significa que TypeScript es de "tipado estático"?
que detecta errores en tiempo de compilacion
2. ¿Cuál es la diferencia entre `let x: number` y `let x = 5`?
x con tipo statico y x con tipo dinamico
3. ¿Qué tipo tiene una variable declarada sin valor inicial?
tiene any

---

## ✅ CHECKPOINT

Antes de continuar, asegúrate de:

- [ ] Haber ejecutado el código con errores
- [ ] Haber leído y entendido el mensaje del compilador
- [ ] Poder explicar con tus propias palabras qué es "inferencia de tipos"

---

**Siguiente clase:** Funciones y Tipos de Retorno

---

*Generado con MAAR Engine v1 - Meta-Aprendizaje Adaptativo de Alto Rendimiento*
