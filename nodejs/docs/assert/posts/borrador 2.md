# **El módulo `assert` en Node.js: Tu aliado para pruebas sencillas (pero potentes)**  

¿Alguna vez has escrito código en Node.js y has pensado *"¿Y si esta función no recibe lo que espero?"* o *"¿Cómo verifico que esto funcione sin montar un test enorme?"* Ahí es donde entra `assert`, el módulo nativo de Node.js que te ayuda a hacer validaciones rápidas y efectivas sin complicaciones.  

En este artículo, te explico cómo usar `assert` como un pro, con ejemplos claros y sus pros y contras. ¡Vamos al lío!  

---  

## **¿Qué es `assert` y por qué usarlo?**  

`assert` es un módulo incorporado en Node.js que te permite hacer comprobaciones (**"aserciones"**) en tu código. Si algo no cumple con lo que esperas, ¡`assert` lanza un error de inmediato!  

### **¿En qué casos mola?**  
✔ **Pruebas rápidas**: Para verificar funciones sin instalar librerías externas.  
✔ **Validar entradas**: Asegurarte de que un valor no sea `null` o `undefined`.  
✔ **Debugging**: Confirmar que un objeto tiene la estructura correcta.  

### **¿Cuándo NO usarlo?**  
❌ Si necesitas tests avanzados con mocks, spies o reportes detallados (mejor usa Jest o Mocha).  
❌ En producción para manejo de errores (para eso están los `try/catch` y errores personalizados).  

---  

## **Los métodos más útiles (con ejemplos)**  

### **1. `assert.ok(valor, mensaje)` → "¿Esto es verdad?"**  
Verifica si un valor es *"truthy"* (que no sea `false`, `0`, `""`, `null`, `undefined`, etc.).  

```javascript
const assert = require('assert');  

assert.ok(true); // ✅ Todo bien  
assert.ok(1); // ✅  
assert.ok(false, "¡Esto no es verdad!"); // 💥 Lanza AssertionError  
```  

**✅ Pros:** Súper simple.  
**❌ Contras:** No te dice qué salió mal, solo que falló.  

---  

### **2. `assert.equal(real, esperado, mensaje)` → "¿Son iguales (con coerción)?"**  
Compara dos valores usando `==` (igualdad débil, permite coerción de tipos).  

```javascript
assert.equal(1, "1"); // ✅ (1 == "1" → true)  
assert.equal(2, 3, "¡2 no es igual a 3!"); // 💥  
```  

**✅ Pros:** Útil si no te importa el tipo.  
**❌ Contras:** Puede dar resultados raros (`"5" == 5` es `true`).  

---  

### **3. `assert.strictEqual(real, esperado, mensaje)` → "¿Son EXACTAMENTE iguales?"**  
Compara con `===` (igualdad estricta: mismo valor Y mismo tipo).  

```javascript
assert.strictEqual(1, 1); // ✅  
assert.strictEqual(1, "1", "No son iguales"); // 💥 (número vs string)  
```  

**✅ Pros:** Más seguro, evita bugs por tipos.  
**❌ Contras:** A veces demasiado estricto.  

---  

### **4. `assert.deepEqual(objeto1, objeto2)` → "¿Tienen las mismas propiedades (con coerción)?"**  
Compara objetos o arrays *en profundidad*, pero con coerción.  

```javascript
const obj1 = { a: 1, b: "2" };  
const obj2 = { a: 1, b: 2 };  

assert.deepEqual(obj1, obj2); // ✅ ("2" == 2)  
```  

**✅ Pros:** Ideal para objetos/arrays complejos.  
**❌ Contras:** La coerción puede jugarte malas pasadas.  

---  

### **5. `assert.deepStrictEqual(objeto1, objeto2)` → "¿Son EXACTAMENTE iguales (hasta los tipos)?"**  
Como `deepEqual`, pero con comparación estricta (`===` en cada propiedad).  

```javascript
assert.deepStrictEqual({ a: 1 }, { a: 1 }); // ✅  
assert.deepStrictEqual([1], ["1"], "No son iguales"); // 💥  
```  

**✅ Pros:** El más preciso para estructuras complejas.  
**❌ Contras:** Requiere que todo coincida al 100%.  

---  

### **Bonus: `assert.throws(funcion, error)` → "¿Esta función lanza un error?"**  
Verifica si una función lanza un error específico.  

```javascript
assert.throws(  
  () => { throw new Error("Ups!"); },  
  Error, // Tipo de error esperado  
  "Debería haber fallado"  
); // ✅  
```  

**✅ Pros:** Perfecto para probar manejo de errores.  
**❌ Contras:** Un poco más complejo de configurar.  

---  

## **Conclusión: ¿Vale la pena usar `assert`?**  

🔹 **Sí**, si quieres algo rápido y sin dependencias.  
🔹 **No**, si necesitas un framework de testing completo.  

### **Mi recomendación:**  
- Usa `assert.strictEqual` y `assert.deepStrictEqual` para evitar sorpresas.  
- Si tu proyecto crece, migra a **Jest** o **Mocha**.  

---  

🚀 **¿Quieres aprender más?**  
- [Documentación oficial de Node.js sobre `assert`](https://nodejs.org/api/assert.html)  
- Libro recomendado: *"Node.js: Guía práctica"* (Fernando Luna).  

¡Y eso es todo! Ahora ya sabes cómo usar `assert` como un experto. ¿Tienes dudas? ¡Déjalas en los comentarios! 👇😃
