
¡Excelente pregunta! La **limpieza de estados al desmontar** es clave para evitar bugs de memoria y comportamientos inesperados. En tu caso, hay dos enfoques (dependiendo de qué necesites limpiar):

---

### 🔥 **1. Limpieza en el componente que consume el contexto**  
**¿Cuándo usarlo?**  
- Si solo quieres resetear estados **cuando se cierra una pantalla específica** (ej: al salir de `InvoiceScreen`).  
- Ideal para estados **locales a esa pantalla** (como `activeForm`).  

**Ejemplo en `InvoiceScreen`:**  
```jsx
import { useEffect } from 'react';
import { usePayment } from './PaymentContext';

const InvoiceScreen = ({ route }) => {
  const { invoiceId } = route.params;
  const { fetchInvoice, activeForm, setActiveForm, resetPaymentState } = usePayment();

  useEffect(() => {
    fetchInvoice(invoiceId);

    return () => {
      // Limpieza al desmontar el componente
      setActiveForm(null); // Cierra los formularios
      // resetPaymentState(); // O usa una función más completa (definida en el Provider)
    };
  }, [invoiceId]);

  // ... resto del código
};
```

---

### 🛠️ **2. Limpieza en el Provider**  
**¿Cuándo usarlo?**  
- Si quieres resetear **todos los estados globales** cuando la app cambia de módulo (ej: cerrar sesión, navegar a otra sección).  
- Útil para estados que deben persistir **mientras la pantalla esté activa**, pero no después.  

**Ejemplo en `PaymentProvider`:**  
```jsx
export const PaymentProvider = ({ children }) => {
  const [state, setState] = useState({
    invoiceData: null,
    activeForm: null,
    userType: 'standard',
  });

  // Función para resetear todo
  const resetPaymentState = () => {
    setState({
      invoiceData: null,
      activeForm: null,
      userType: 'standard',
    });
  };

  // Provee el estado + función de limpieza
  const value = {
    ...state,
    resetPaymentState,
  };

  return (
    <PaymentContext.Provider value={value}>
      {children}
    </PaymentContext.Provider>
  );
};
```

**Luego, en cualquier componente:**  
```jsx
const SomeComponent = () => {
  const { resetPaymentState } = usePayment();

  const handleLogout = () => {
    resetPaymentState(); // Limpia todo antes de navegar
    navigation.navigate('Login');
  };
};
```

---

### 📌 **¿Qué enfoque elegir?**  
| **Criterio**               | **Limpieza en Componente** | **Limpieza en Provider** |
|----------------------------|---------------------------|--------------------------|
| **Alcance**                | Solo afecta a una pantalla | Afecta a toda la app     |
| **Persistencia**           | Estados locales            | Estados globales         |
| **Ejemplo de uso**         | Cerrar formulario al salir | Cerrar sesión            |

---

### 🚀 **Recomendación para tu caso**  
1. **Para el formulario de pago (`activeForm`)**:  
   - Limpia en el componente (`InvoiceScreen`), ya que es lógica de UI.  
   ```jsx
   useEffect(() => {
     return () => setActiveForm(null); // Al desmontar la pantalla
   }, []);
   ```

2. **Para los datos de la factura (`invoiceData`)**:  
   - Si son **globales** (ej: usados en otras pantallas), déjalos en el Provider y limpia con una acción explícita (como `resetPaymentState`).  
   - Si son **locales a la pantalla**, limpia en el `useEffect` de `InvoiceScreen`.  

---

### ⚠️ **¡Cuidado con esto!**  
- Si limpias estados globales en el `useEffect` de un componente, podrías afectar a **otros componentes que los usen**.  
- **Solución:** Usa un estado local en el componente si no debe compartirse:  
  ```jsx
  const [localFormState, setLocalFormState] = useState(null);
  ```

---

## 📌 **Conclusión**  
- **Limpia en el componente** si es un estado de UI (como animaciones o formularios).  
- **Limpia en el Provider** si es un estado global que debe resetearse en ciertas acciones (como logout).  

**Ejemplo completo combinado:**  
```jsx
// En PaymentProvider
const resetForms = () => setActiveForm(null);

// En InvoiceScreen
useEffect(() => {
  return () => resetForms(); // Solo limpia el formulario
}, []);
``` 
