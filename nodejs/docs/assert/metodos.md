En Node.js, el módulo `assert` proporciona varios métodos de verificación que permiten realizar pruebas y validaciones en el código. A continuación, te listaré todos los métodos junto con un ejemplo práctico y bien documentado de cada uno.

---

## 📌 Lista de Métodos de `assert` en Node.js

1. **`assert.ok(value[, message])`**
2. **`assert.strictEqual(actual, expected[, message])`**
3. **`assert.notStrictEqual(actual, expected[, message])`**
4. **`assert.deepStrictEqual(actual, expected[, message])`**
5. **`assert.notDeepStrictEqual(actual, expected[, message])`**
6. **`assert.throws(fn[, error[, message]])`**
7. **`assert.doesNotThrow(fn[, error[, message]])`**
8. **`assert.ifError(value)`**

A continuación, se explican cada uno con ejemplos prácticos y documentados.

---

## 1 `assert.ok(value[, message])`

Verifica si el valor es verdadero (`true`). Si el valor es `false`, lanza un error.

### ✅ **Ejemplo práctico**:

```javascript
const assert = require("node:assert").strict();

const esMayorDeEdad = (edad) => edad >= 18;

// Verifica si una persona de 20 años es mayor de edad
assert.ok(esMayorDeEdad(20), "La persona debe ser mayor de edad"); // ✅ Pasa

// Si la persona tiene 16 años, la prueba fallará
assert.ok(esMayorDeEdad(16), "La persona debe ser mayor de edad"); // ❌ Falla
```

---

## 2 `assert.strictEqual(actual, expected[, message])`

Verifica si `actual === expected` (comparación estricta).

### ✅ **Ejemplo práctico**:

```javascript
const assert = require("node:assert").strict();

const suma = (a, b) => a + b;

assert.strictEqual(suma(2, 3), 5, "La suma debe ser exactamente 5"); // ✅ Pasa
assert.strictEqual(5, "5", "No deberían ser estrictamente iguales"); // ❌ Falla
```

---

## 3 `assert.notStrictEqual(actual, expected[, message])`

Verifica si `actual !== expected`.

### ✅ **Ejemplo práctico**:

```javascript
const assert = require("node:assert").strict();

const obtenerMensaje = () => "Hola";

assert.notStrictEqual(
  obtenerMensaje(),
  "Adiós",
  "Los mensajes no deben ser iguales",
); // ✅ Pasa
assert.notStrictEqual(
  5,
  "5",
  'El número 5 y la cadena "5" son distintos en tipos',
); // ✅ Pasa
```

---

## 4 `assert.deepStrictEqual(actual, expected[, message])`

Verifica si dos objetos o arreglos son idénticos en valores y estructura.

### ✅ **Ejemplo práctico**:

```javascript
const assert = require("node:assert").strict();

const usuario1 = { nombre: "Juan", edad: 30 };
const usuario2 = { nombre: "Juan", edad: 30 };

assert.deepStrictEqual(
  usuario1,
  usuario2,
  "Los objetos deben ser exactamente iguales",
); // ✅ Pasa
```

---

## 5 `assert.notDeepStrictEqual(actual, expected[, message])`

Verifica si dos objetos o arreglos **NO** son idénticos en valores y estructura.

### ✅ **Ejemplo práctico**:

```javascript
const assert = require("node:assert").strict();

const persona1 = { nombre: "Ana", edad: 25 };
const persona2 = { nombre: "Ana", edad: 26 };

assert.notDeepStrictEqual(
  persona1,
  persona2,
  "Los objetos no deben ser idénticos",
); // ✅ Pasa
```

---

## 6 `assert.throws(fn[, error[, message]])`

Verifica que una función lanza un error.

### ✅ **Ejemplo práctico**:

```javascript
const assert = require("node:assert").strict();

function dividir(a, b) {
  if (b === 0) throw new Error("No se puede dividir por cero");
  return a / b;
}

// Verifica que la función lanza un error al dividir por cero
assert.throws(
  () => dividir(10, 0),
  Error,
  "Debe lanzar un error al dividir por cero",
); // ✅ Pasa
```

---

## 7 `assert.doesNotThrow(fn[, error[, message]])`

Verifica que una función **NO** lanza un error.

### ✅ **Ejemplo práctico**:

```javascript
const assert = require("node:assert").strict();

function obtenerDatos() {
  return { nombre: "Carlos", edad: 35 };
}

// Verifica que la función no lanza errores
assert.doesNotThrow(
  () => obtenerDatos(),
  Error,
  "La función no debe lanzar errores",
); // ✅ Pasa
```

---

## 8 `assert.ifError(value)`

Si `value` es un error (`instanceof Error`), lo lanza. Si `value` es `null` o `undefined`, no hace nada.

### ✅ **Ejemplo práctico**:

```javascript
const assert = require("node:assert").strict();

function validarEmail(email) {
  if (!email.includes("@")) {
    return new Error("Email inválido");
  }
  return null;
}

const error = validarEmail("correo_invalido.com");

assert.ifError(error); // ❌ Falla porque 'error' es una instancia de Error
```

---

## **Conclusión**

El módulo `assert` de Node.js proporciona herramientas poderosas para verificar valores y estructuras en pruebas de código. Su uso es ideal en entornos de desarrollo y testing.
