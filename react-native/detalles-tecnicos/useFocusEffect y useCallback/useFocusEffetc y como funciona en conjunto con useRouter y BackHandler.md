¡Hola! Claro, con gusto te explico sobre `useFocusEffect`, `useRouter()` y `BackHandler` en React Native (o React Navigation) de una manera sencilla. 😊

---

### **1. `useFocusEffect` - ¿Qué es?**
Es un hook de **React Navigation** que te permite ejecutar código cuando una pantalla **entra en foco** (es decir, cuando el usuario navega a ella) o **pierde el foco** (cuando sale de ella).  

- **¿Para qué sirve?**  
  Por ejemplo, si quieres cargar datos cada vez que el usuario vuelve a una pantalla, o limpiar recursos cuando la abandona.

- **Ejemplo básico:**
  ```jsx
  import { useFocusEffect } from '@react-navigation/native';
  import React, { useCallback } from 'react';

  function MiPantalla() {
    useFocusEffect(
      useCallback(() => {
        console.log('La pantalla está en foco (visible)');
        // Aquí puedes cargar datos o hacer algo útil.

        return () => {
          console.log('La pantalla ya no está en foco');
          // Aquí puedes limpiar (ej. cancelar peticiones).
        };
      }, [])
    );

    return <Text>¡Hola!</Text>;
  }
  ```

---

### **2. `useRouter()` - ¿Qué es?**
Es un hook de **Expo Router** (para navegación en Expo) que te permite manejar la navegación entre pantallas, similar a `useNavigation` de React Navigation.  

- **¿Para qué sirve?**  
  Para navegar entre pantallas (`router.push`), volver atrás (`router.back`), o pasar parámetros.

- **Ejemplo básico:**
  ```jsx
  import { useRouter } from 'expo-router';

  function MiPantalla() {
    const router = useRouter();

    return (
      <Button 
        title="Ir a Otra Pantalla" 
        onPress={() => router.push('/otra-pantalla')}
      />
    );
  }
  ```

---

### **3. `BackHandler` - ¿Qué es?**
Es una API de React Native que controla el botón **Atrás** del dispositivo (en Android o gestos en iOS).  

- **¿Para qué sirve?**  
  Para personalizar lo que pasa cuando el usuario presiona "Atrás" (ej. mostrar un mensaje de confirmación).

- **Ejemplo básico:**
  ```jsx
  import { BackHandler } from 'react-native';
  import { useEffect } from 'react';

  function MiPantalla() {
    useEffect(() => {
      const backAction = () => {
        console.log('¡Se presionó Atrás!');
        // Si retornas `true`, evitas que vuelva atrás.
        return true;
      };

      BackHandler.addEventListener('hardwareBackPress', backAction);

      return () => {
        BackHandler.removeEventListener('hardwareBackPress', backAction);
      };
    }, []);

    return <Text>Pantalla con BackHandler personalizado</Text>;
  }
  ```

---

### **¿Cómo funcionan juntos?**
Imagina que tienes una app donde:  
1. Cuando el usuario entra a una pantalla (`useFocusEffect`), cargas datos.  
2. Usas `useRouter()` para navegar a otras pantallas.  
3. Personalizas el botón Atrás con `BackHandler` para evitar salir accidentalmente.  

**Ejemplo integrado:**
```jsx
import { useFocusEffect } from '@react-navigation/native';
import { useRouter } from 'expo-router';
import { BackHandler } from 'react-native';
import { useCallback } from 'react';

function PantallaDetalle() {
  const router = useRouter();

  // 1. Carga datos al enfocar la pantalla
  useFocusEffect(
    useCallback(() => {
      console.log('Cargando datos...');
      return () => console.log('Limpiando datos...');
    }, [])
  );

  // 2. Personalizar el botón Atrás
  useEffect(() => {
    const backAction = () => {
      if (deboEvitarSalir) {
        alert('¡Confirma antes de salir!');
        return true; // Bloquea la acción predeterminada.
      }
      return false; // Permite volver atrás.
    };

    BackHandler.addEventListener('hardwareBackPress', backAction);
    return () => BackHandler.removeEventListener('hardwareBackPress', backAction);
  }, []);

  return (
    <Button 
      title="Volver" 
      onPress={() => router.back()}
    />
  );
}
```

---

### **Resumen:**
- **`useFocusEffect`**: Para acciones al mostrar/ocultar una pantalla.  
- **`useRouter()`**: Para navegar entre pantallas (Expo Router).  
- **`BackHandler`**: Para controlar el botón Atrás del dispositivo.  

¡Espero que te ayude! Si tienes dudas, dime. 🚀
