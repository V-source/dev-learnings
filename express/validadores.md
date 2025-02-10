Un validador de correos electrónicos y otro de contraseñas en **JavaScript con TypeScript**. El validador de contraseñas asegura que la contraseña tenga al menos un carácter en mayúscula, un número y un guion bajo (`_`) como carácter especial.

---

### **Código de los Validadores**

```typescript
// Validador de correos electrónicos
const validateEmail = (email: string): boolean => {
  // Expresión regular para validar correos electrónicos
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
};

// Validador de contraseñas
const validatePassword = (password: string): boolean => {
  // Expresión regular para validar contraseñas
  const passwordRegex = /^(?=.*[A-Z])(?=.*\d)(?=.*_)[A-Za-z\d_]{8,}$/;
  return passwordRegex.test(password);
};

// Ejemplos de uso
const email = "usuario@example.com";
const password = "Password_123";

console.log(`¿El correo "${email}" es válido?`, validateEmail(email)); // true
console.log(`¿La contraseña "${password}" es válida?`, validatePassword(password)); // true
```

---

### **Explicación del Código**

#### **Validador de Correos Electrónicos**
- **Expresión regular**: `^[^\s@]+@[^\s@]+\.[^\s@]+$`
  - `^[^\s@]+`: Asegura que no haya espacios ni `@` antes del símbolo `@`.
  - `@[^\s@]+`: Valida que haya un `@` seguido de caracteres que no sean espacios ni `@`.
  - `\.[^\s@]+$`: Valida que haya un punto (`.`) seguido de caracteres que no sean espacios ni `@`.

#### **Validador de Contraseñas**
- **Expresión regular**: `^(?=.*[A-Z])(?=.*\d)(?=.*_)[A-Za-z\d_]{8,}$`
  - `(?=.*[A-Z])`: Asegura que haya al menos una letra mayúscula.
  - `(?=.*\d)`: Asegura que haya al menos un número.
  - `(?=.*_)`: Asegura que haya al menos un guion bajo (`_`).
  - `[A-Za-z\d_]{8,}`: Asegura que la contraseña tenga al menos 8 caracteres y solo contenga letras, números y guiones bajos.

---

### **Pruebas de los Validadores**

```typescript
// Pruebas de correos electrónicos
console.log(validateEmail("correo@dominio.com")); // true
console.log(validateEmail("correo@dominio")); // false (falta el dominio)
console.log(validateEmail("correo@.com")); // false (dominio inválido)
console.log(validateEmail("correo@dominio..com")); // false (dominio inválido)

// Pruebas de contraseñas
console.log(validatePassword("Password_123")); // true
console.log(validatePassword("password_123")); // false (falta mayúscula)
console.log(validatePassword("Password123")); // false (falta guion bajo)
console.log(validatePassword("Pass_1")); // false (menos de 8 caracteres)
```

---

### **Uso en un Formulario (Ejemplo)**

```typescript
const form = {
  email: "usuario@example.com",
  password: "Password_123",
};

if (!validateEmail(form.email)) {
  console.log("Correo electrónico inválido.");
} else if (!validatePassword(form.password)) {
  console.log("Contraseña inválida.");
} else {
  console.log("Formulario válido. Registro exitoso.");
}
```

---

### **Resumen**
- **Validador de correos**: Usa una expresión regular para verificar el formato básico de un correo electrónico.
- **Validador de contraseñas**: Asegura que la contraseña tenga al menos una mayúscula, un número, un guion bajo y un mínimo de 8 caracteres.

