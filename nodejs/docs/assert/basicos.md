
# Usando assert.throws() para funciones que deben lanzar errores
```javascript

const assert = require('node:assert').strict();

function dividir(a, b) {
  if (b === 0) {
    throw new Error('No se puede dividir por cero');
  }
  return a / b;
}

try {
  // Prueba que la función lanza el error esperado
  assert.throws(
    () => dividir(10, 0),
    Error,
    'No se puede dividir por cero'
  );
  console.log('✓ La función lanza el error correcto');
} catch (error) {
  console.error('Error en la prueba:', error.message);
}

```

- Verifica que la función lanza el error esperado
- Puede validar el mensaje de error exacto
- Ideal para pruebas de manejo de errores
- Solo funciona con funciones que deben lanzar errores
- Requiere estructura específica de prueba
Este método es ideal cuando necesitas verificar que una función lanza el error correcto 11:6. La función `assert.throws()` ejecuta la función dentro de un callback y verifica que lance el tipo y mensaje de error esperados.Usando assert.strictEqual() para verificar resultados

---

# Usando assert.strictEqual() para verificar resultados

```javascript

const assert = require('node:assert').strict();

function sumar(a, b) {
  return a + b;
}

try {
  // Ejecuta la función y verifica su resultado
  const resultado = sumar(5, 3);
  assert.strictEqual(resultado, 8, 'La suma debe ser 8');
  console.log('✓ La función retorna el valor correcto');
} catch (error) {
  console.error('Error en la prueba:', error.message);
}
```
**pros**:
- Verifica resultados de funciones simples
- Compara valores con igualdad estricta
- Fácil de implementar

**cons**:
- No verifica el comportamiento interno de la función
- Solo compara el resultado final

Este método es útil para verificar que una función retorna el valor esperado 13:3. La función `assert.strictEqual()` compara el resultado usando el operador `===`, asegurando que tanto el valor como el tipo sean iguales.Usando assert.deepEqual() para objetos y arrays

---

# Usando assert.deepEqual() para objetos y arrays

```javascript

const assert = require('node:assert').strict();

function procesarDatos(datos) {
  return {
    ...datos,
    timestamp: new Date()
  };
}

try {
  const entrada = { id: 1, nombre: 'Prueba' };
  const resultado = procesarDatos(entrada);
  
  assert.deepEqual(resultado, {
    id: 1,
    nombre: 'Prueba',
    timestamp: expect.any(Date)
  }, 'La función debe retornar el objeto con timestamp');
  console.log('✓ La función retorna el objeto correcto');
} catch (error) {
  console.error('Error en la prueba:', error.message);
}
```
**pros**:
- Verifica objetos y arrays complejos
- Permite comparaciones recursivas
- Útil para estructuras de datos anidadas

**cons**:
- Puede ser más lento que strictEqual
- Requiere estructura de objeto esperado completa


Este método es ideal para verificar funciones que retornan objetos o arrays 11:5. La función `assert.deepEqual()` compara recursivamente todas las propiedades del objeto, permitiendo verificar estructuras de datos complejas.Para usar estos métodos, asegúrate de:

---

# asegurate de:


1. Importar el módulo assert correctamente
```javascript

const assert = require('node:assert').strict();
```

2. Ejecutar cada prueba dentro de un bloque try/catch

3. Usar el método de assert más apropiado según tu caso:
- `assert.throws()` para funciones que deben lanzar errores
- `assert.strictEqual()` para valores simples
- `assert.deepEqual()` para objetos y arrays

---

Cada método de assert te permite verificar diferentes aspectos de la función que estás probando, desde su comportamiento con errores hasta los valores que retorna.
