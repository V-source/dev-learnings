Perfecto 💪 — veamos **casos de uso reales** (como los que te encontrarías en una API profesional con Express + Mongoose).
Te mostraré varios **escenarios prácticos** donde tiene sentido usar **métodos de documento** y **métodos estáticos**, explicando la **lógica detrás de cada uno**.

---

## 🧱 Modelo base: `User`

Imagina una app típica con usuarios registrados, autenticación y roles.

```js
import mongoose from "mongoose";
import bcrypt from "bcryptjs";

const userSchema = new mongoose.Schema({
  name: String,
  email: { type: String, unique: true, required: true },
  password: { type: String, required: true },
  role: { type: String, enum: ["user", "admin"], default: "user" },
  active: { type: Boolean, default: true },
}, { timestamps: true });
```

---

## 🧩 Caso 1: Método de **documento** → lógica específica de una instancia

### 🎯 Escenario real:

Necesitas verificar si la contraseña ingresada coincide con la del usuario.

### 📘 Implementación

```js
userSchema.methods.checkPassword = async function (plainPassword) {
  return await bcrypt.compare(plainPassword, this.password);
};
```

### ⚙️ Lógica

* Se llama desde **una instancia concreta** del usuario (por eso usamos `this.password`).
* Es útil durante el login o la validación de sesión.

### 🧠 Uso real

```js
const user = await User.findOne({ email });
if (!user) throw new Error("Usuario no encontrado");

const valid = await user.checkPassword(password);
if (!valid) throw new Error("Contraseña incorrecta");
```

---

## 🧩 Caso 2: Método de **documento** → acciones internas del documento

### 🎯 Escenario real:

Marcar al usuario como inactivo (soft delete), sin eliminarlo físicamente.

```js
userSchema.methods.deactivate = async function () {
  this.active = false;
  await this.save();
};
```

### ⚙️ Lógica

* Se modifica **solo este documento** (`this`).
* Ideal para evitar borrados físicos y mantener integridad referencial.

### 🧠 Uso real

```js
const user = await User.findById(id);
await user.deactivate();
```

---

## 🧩 Caso 3: Método **estático** → búsquedas personalizadas

### 🎯 Escenario real:

Necesitas una función reutilizable para buscar usuarios por email (sin repetir `findOne` en cada lugar).

```js
userSchema.statics.findByEmail = function (email) {
  return this.findOne({ email });
};
```

### ⚙️ Lógica

* No se necesita un documento existente, solo el modelo.
* Ideal para búsquedas recurrentes o validaciones previas al registro.

### 🧠 Uso real

```js
const existing = await User.findByEmail(email);
if (existing) throw new Error("El correo ya está registrado");
```

---

## 🧩 Caso 4: Método **estático** → filtros globales

### 🎯 Escenario real:

Obtener todos los usuarios activos.

```js
userSchema.statics.findActiveUsers = function () {
  return this.find({ active: true });
};
```

### ⚙️ Lógica

* Es una **consulta global** que aplica un filtro común.
* Útil para paneles de administración o reportes.

### 🧠 Uso real

```js
const users = await User.findActiveUsers();
```

---

## 🧩 Caso 5: Mezclando ambos — autenticación completa

### 🎯 Escenario real:

Un endpoint de login necesita buscar un usuario y verificar la contraseña.

```js
userSchema.statics.login = async function (email, password) {
  const user = await this.findOne({ email });
  if (!user) throw new Error("Usuario no encontrado");

  const valid = await user.checkPassword(password);
  if (!valid) throw new Error("Contraseña incorrecta");

  return user;
};
```

### ⚙️ Lógica combinada

* `login()` es un método **estático** (opera sobre el modelo completo).
* Internamente llama a un método **de documento** (`checkPassword`) para la lógica individual.

### 🧠 Uso real

```js
try {
  const user = await User.login(req.body.email, req.body.password);
  res.json({ message: "Login exitoso", user });
} catch (err) {
  res.status(401).json({ error: err.message });
}
```

---

## 🧩 Caso 6: Modelo `Order` con cálculos y agregaciones

### 🎯 Escenario real:

Tienes un sistema de órdenes y quieres calcular el total de ventas o aplicar descuentos.

#### Método de documento → aplicar descuento

```js
orderSchema.methods.applyDiscount = function (percent) {
  this.total = this.total - (this.total * percent / 100);
  return this.save();
};
```

#### Método estático → total de ventas

```js
orderSchema.statics.totalSales = async function () {
  const result = await this.aggregate([
    { $group: { _id: null, total: { $sum: "$total" } } }
  ]);
  return result[0]?.total || 0;
};
```

#### Uso:

```js
const order = await Order.findById(id);
await order.applyDiscount(10);

const total = await Order.totalSales();
console.log("Ventas totales:", total);
```

---

## 🧠 Conclusión: reglas mentales simples

| Tipo                    | Piensa en...                                                           | Ejemplo típico         |
| ----------------------- | ---------------------------------------------------------------------- | ---------------------- |
| **Método de documento** | “Yo, este documento, quiero hacer algo”                                | `user.checkPassword()` |
| **Método estático**     | “El modelo completo quiere hacer algo”                                 | `User.findByEmail()`   |
| **Ambos combinados**    | “Buscar algo globalmente y luego operar sobre el resultado individual” | `User.login()`         |

---

¿Quieres que te cree un **mini proyecto Express + Mongoose** (con rutas `/register`, `/login`, `/users`) que implemente estos métodos en práctica para que veas cómo se integran con controladores y middleware?
