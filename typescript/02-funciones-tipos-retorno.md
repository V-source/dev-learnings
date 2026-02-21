# Clase 02: Funciones y Tipos de Retorno

> **Método MAAR:** Aprendizaje por Contraste y Laboratorio de Errores

---

## 🗺️ HOJA DE RUTA: Funciones en TypeScript

| Aspecto | JavaScript | TypeScript |
|---------|------------|------------|
| **Parámetros** | Sin tipos | Tipados obligatoriamente |
| **Retorno** | Implícito (cualquier cosa) | Explícito (`: tipo`) o inferido |
| **Parámetros opcionales** | Todos son opcionales | Se marca con `?` |
| **Valores por defecto** | `param = valor` | Igual + inferencia de tipo |
| **Funciones flecha** | `() => {}` | `(): tipo => {}` |

---

## 🔄 SINTAXIS EN ESPEJO: Funciones Básicas

### JavaScript (Sin restricciones)

```javascript
// JS: Sin tipos, cualquier cosa funciona
function sumar(a, b) {
    return a + b;
}

sumar(5, 3);        // 8
sumar("hola", 3);   // "hola3" ← WTF?
sumar(5);           // NaN ← Sin advertencia
```

### TypeScript (Tipado explícito)

```typescript
// TS: Parámetros y retorno tipados
function sumar(a: number, b: number): number {
    return a + b;
}

sumar(5, 3);        // ✅ 8
sumar("hola", 3);   // ❌ Error: Argument of type 'string' is not assignable
sumar(5);           // ❌ Error: Expected 2 arguments, but got 1
```

---

## 🔬 LABORATORIO DE ERRORES #2

### Instrucciones:

1. **Agrega** al archivo `test.ts`:

```typescript
// Función con tipos explícitos
function saludar(nombre: string, edad: number): string {
    return `Hola ${nombre}, tienes ${edad} años`;
}

// Función sin tipo de retorno (inferido)
function multiplicar(a: number, b: number) {
    return a * b;  // TS infiere que retorna number
}

// Función que no retorna nada
function loguear(mensaje: string): void {
    console.log(mensaje);
}
```

2. **Rompe el código** intencionalmente:

```typescript
saludar(123, "Ana");           // ¿Qué error?
saludar("Ana");                // ¿Qué error?
multiplicar(5, "hola");        // ¿Qué error?
loguear("test").length;        // ¿Qué error?
```

3. **Ejecuta:**
```bash
pnpm exec tsc test.ts --noEmit
```

---

## 🧠 CONCEPTO CLAVE: Tipos de Retorno

### Inferido vs Explícito

```typescript
// Retorno INFERIDO (TS lo deduce)
function duplicar(n: number) {
    return n * 2;  // TS sabe que retorna number
}

// Retorno EXPLÍCITO (Tú lo declaras)
function duplicar(n: number): number {
    return n * 2;
}

// Sin retorno (void)
function saludar(nombre: string): void {
    console.log(`Hola ${nombre}`);
    // No hay return
}
```

### ¿Cuándo usar explícito vs inferido?

| Situación | Recomendación |
|-----------|---------------|
| Función simple | Inferido está bien |
| Función pública/API | Explícito (documentación) |
| Función compleja | Explícito (claridad) |
| Validación estricta | Explícito + `noImplicitReturns` |

---

## 🔧 Parámetros Opcionales y Valores por Defecto

```typescript
// Parámetro opcional (con ?)
function saludar(nombre: string, edad?: number): string {
    if (edad) {
        return `Hola ${nombre}, tienes ${edad} años`;
    }
    return `Hola ${nombre}`;
}

saludar("Ana");        // ✅ "Hola Ana"
saludar("Ana", 25);    // ✅ "Hola Ana, tienes 25 años"

// Valor por defecto
function crearUsuario(nombre: string, activo: boolean = true): object {
    return { nombre, activo };
}

crearUsuario("Ana");           // { nombre: "Ana", activo: true }
crearUsuario("Luis", false);   // { nombre: "Luis", activo: false }
```

---

## ⚠️ NUNCA OLVIDAR: Parámetros Opcionales

### ❌ Error común: Orden de parámetros

```typescript
// ❌ MAL: Parámetro opcional ANTES del obligatorio
function crearPerfil(edad?: number, nombre: string): string {
    return `${nombre} tiene ${edad} años`;
}
// Error: A required parameter cannot follow an optional parameter

// ✅ BIEN: Parámetro opcional AL FINAL
function crearPerfil(nombre: string, edad?: number): string {
    return `${nombre} tiene ${edad ?? 'edad desconocida'} años`;
}
```

### Regla de oro:
> **Los parámetros opcionales (`?`) SIEMPRE van después de los obligatorios**

---

## 🎯 Funciones Flecha (Arrow Functions)

```typescript
// Sintaxis completa
const sumar = (a: number, b: number): number => {
    return a + b;
};

// Sintaxis corta (retorno implícito)
const sumar = (a: number, b: number): number => a + b;

// Sin llaves = retorno automático
const duplicar = (n: number): number => n * 2;

// Tipo de la función completa
type Operacion = (a: number, b: number) => number;

const multiplicar: Operacion = (a, b) => a * b;  // Tipos inferidos del type
```

---

## 📝 DESAFÍO DE RECALL ACTIVO

**Sin mirar arriba**, responde:

1. ¿Qué significa `: void` en una función?
2. ¿Cuál es la diferencia entre parámetro opcional (`?`) y valor por defecto?
3. ¿Por qué los parámetros opcionales deben ir al final?

---

## ✅ CHECKPOINT

Antes de continuar, asegúrate de:

- [ ] Haber ejecutado los ejemplos con errores
- [ ] Entender la diferencia entre retorno inferido y explícito
- [ ] Saber cuándo usar `void`
- [ ] Conocer la regla de orden de parámetros opcionales

---

## 🧪 EJERCICIO PRÁCTICO

Escribe una función `calcularPrecio` que:
1. Reciba un `precio` (number, obligatorio)
2. Reciba un `descuento` (number, opcional, por defecto 0)
3. Retorne el precio final como `number`

```typescript
// Tu código aquí:
function calcularPrecio(precio: number, descuento?: number): number {
    // ...
}

// Tests (deben funcionar):
calcularPrecio(100);      // 100
calcularPrecio(100, 10);  // 90
```

---

**Siguiente clase:** Interfaces y Tipos Personalizados

---

*Generado con MAAR Engine v1 - Meta-Aprendizaje Adaptativo de Alto Rendimiento*
