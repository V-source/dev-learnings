# CUANDO USER METODOS REGULARES Y ESTATICOS.

Excelente pregunta 👏 — esta diferencia es **fundamental** para usar Mongoose de forma elegante y mantener tu código limpio y bien estructurado.
Veamos **cuándo usar un método a nivel de documento (instance method)** y **cuándo usar uno a nivel de modelo (static method)**, con ejemplos y criterios prácticos.

---

## 🧩 1. Métodos de **documento** (`schema.methods`)

### 🔹 Cuándo usarlos

Usa un **método de documento** cuando la operación **depende de los datos de un documento en particular**.

👉 En otras palabras: necesitas acceder a `this` que representa una **instancia concreta del documento**.

---

### 💡 Ejemplos típicos

#### 1. Hashear contraseñas antes de guardar

```js
userSchema.methods.comparePassword = async function (plainPassword) {
  return bcrypt.compare(plainPassword, this.password);
};
```

> Se usa `this.password` → depende del documento actual.

#### 2. Calcular propiedades personalizadas

```js
userSchema.methods.getFullName = function () {
  return `${this.firstName} ${this.lastName}`;
};
```

#### 3. Ejecutar acciones relacionadas a un documento

```js
invoiceSchema.methods.markAsPaid = function () {
  this.status = 'paid';
  this.paidAt = new Date();
  return this.save();
};
```

---

### ✅ Cuándo es ideal

* La lógica **se enfoca en un solo documento**.
* Necesitas acceder o modificar propiedades del documento.
* Quieres mantener la lógica cerca del modelo, pero **no** globalmente en todos los documentos.

---

## 🧱 2. Métodos **estáticos** (`schema.statics`)

### 🔹 Cuándo usarlos

Usa un **método estático** cuando la operación **afecta o consulta varios documentos** o **no depende de una instancia específica**.

👉 Es decir, cuando la lógica pertenece al **modelo completo**, no a un solo documento.

---

### 💡 Ejemplos típicos

#### 1. Búsquedas personalizadas

```js
userSchema.statics.findByEmail = function (email) {
  return this.findOne({ email });
};
```

#### 2. Consultas masivas

```js
userSchema.statics.findActive = function () {
  return this.find({ active: true });
};
```

#### 3. Operaciones globales

```js
orderSchema.statics.getTotalSales = async function () {
  const result = await this.aggregate([
    { $group: { _id: null, total: { $sum: "$amount" } } }
  ]);
  return result[0]?.total || 0;
};
```

---

### ✅ Cuándo es ideal

* La operación **no depende de un documento concreto**.
* Quieres extender el modelo con funciones reutilizables de consulta o agregación.
* Piensas en “consultas” o “acciones globales” sobre la colección.

---

## 🧠 Comparación rápida

| Tipo de método   | Acceso a `this`       | Cuándo usarlo                                            | Ejemplo                  |
| ---------------- | --------------------- | -------------------------------------------------------- | ------------------------ |
| `schema.methods` | Documento (instancia) | Lógica que actúa sobre un documento específico           | `user.comparePassword()` |
| `schema.statics` | Modelo (colección)    | Lógica global que consulta o afecta múltiples documentos | `User.findByEmail()`     |

---

## ⚙️ Consejo práctico

Piensa así:

* Si tu función podría escribirse como `document.hacerAlgo()`, es un **método**.
* Si la escribirías como `Modelo.buscarAlgo()`, es un **estático**.

---

¿Quieres que te dé un **ejemplo completo y moderno** (Node.js + Mongoose 8) donde se usan ambos tipos correctamente en un mismo esquema (por ejemplo, un modelo `User` con login y búsquedas personalizadas)?
