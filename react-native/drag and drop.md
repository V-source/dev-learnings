### `DragToResize`

Este componente de React Native implementa una caja que puede cambiar de altura al arrastrar una barra. Utiliza `PanResponder` para gestionar los gestos táctiles y `Animated` para animar la altura de la caja.

---

```tsx
import React, { useRef } from "react";
import {
  View,
  Animated,
  PanResponder,
  StyleSheet,
  Text,
  Dimensions,
} from "react-native";

// Obtener la altura de la pantalla para usarla si es necesario
const { height: SCREEN_HEIGHT } = Dimensions.get("window");

const DragToResize = () => {
  // **Estado Animado**: Controla dinámicamente la altura de la caja
  const heightAnim = useRef(new Animated.Value(150)).current; // Altura inicial de la caja

  // **PanResponder**: Maneja los gestos del usuario (arrastrar)
  const panResponder = useRef(
    PanResponder.create({
      // Determina si este componente debe responder al toque inicial
      onStartShouldSetPanResponder: () => true,

      // Maneja el movimiento táctil del usuario
      onPanResponderMove: (_, gestureState) => {
        // Comprueba si el usuario arrastra hacia arriba (dy < 0)
        if (gestureState.dy < 0) {
          // Ajusta la altura animada incrementándola en función de la distancia arrastrada
          heightAnim.setValue(150 - gestureState.dy); // `dy` es negativo, así que lo restamos
        }
      },

      // Acción cuando el usuario suelta el dedo
      onPanResponderRelease: () => {
        // Devuelve la altura de la caja a su valor original (150) con una animación suave
        Animated.spring(heightAnim, {
          toValue: 150, // Altura original
          useNativeDriver: false, // Desactivado porque estamos animando propiedades de diseño
        }).start();
      },
    })
  ).current;

  return (
    <View style={styles.container}>
      {/* Barra de Arrastre */}
      <Animated.View
        {...panResponder.panHandlers} // Conecta los manejadores del PanResponder a esta vista
        style={styles.dragHandle}
      >
        <Text style={styles.handleText}>⬆️ Arrastra aquí</Text>
      </Animated.View>

      {/* Caja Redimensionable */}
      <Animated.View
        style={[
          styles.resizableBox,
          { height: heightAnim }, // Aplica la altura animada
        ]}
      >
        <Text style={styles.boxText}>Arrastra hacia arriba</Text>
      </Animated.View>
    </View>
  );
};

export default DragToResize;

// **Estilos**: Define los estilos para los componentes del diseño
const styles = StyleSheet.create({
  container: {
    flex: 1, // Ocupa todo el espacio disponible
    justifyContent: "flex-end", // Alinea los elementos al fondo de la pantalla
    alignItems: "center", // Centra los elementos horizontalmente
    backgroundColor: "#f8f8f8",
  },
  dragHandle: {
    backgroundColor: "#6200ee", // Color de la barra de arrastre
    width: "100%", // Ocupa todo el ancho del contenedor
    height: 50, // Altura fija de la barra
    justifyContent: "center", // Centra el texto verticalmente
    alignItems: "center", // Centra el texto horizontalmente
  },
  handleText: {
    color: "#fff", // Texto blanco
    fontSize: 16,
    fontWeight: "bold",
  },
  resizableBox: {
    backgroundColor: "#03dac5", // Color de la caja
    width: "100%", // Ocupa todo el ancho del contenedor
    justifyContent: "center", // Centra el texto verticalmente
    alignItems: "center", // Centra el texto horizontalmente
  },
  boxText: {
    color: "#000", // Texto negro
    fontSize: 18,
  },
});
```

---

### **Explicación del Componente**

1. **Altura Animada (`heightAnim`)**:
   - Usamos `Animated.Value` para controlar dinámicamente la altura de la caja.
   - Este valor se actualiza a medida que el usuario arrastra la barra hacia arriba.

2. **Gestión de Gestos con `PanResponder`**:
   - **`onPanResponderMove`**: Calcula la nueva altura de la caja mientras el usuario arrastra.
   - **`onPanResponderRelease`**: Devuelve la altura a su valor inicial cuando se suelta la barra.

3. **Animaciones con `Animated`**:
   - La altura de la caja (`resizableBox`) se enlaza al valor de `heightAnim`.
   - `Animated.spring` devuelve la altura al valor inicial de forma suave.

4. **Estilo y Diseño**:
   - **`dragHandle`**: Barra interactiva que el usuario puede arrastrar.
   - **`resizableBox`**: Caja que cambia de tamaño según la interacción.

---

### **Funcionamiento**
1. Al iniciar el gesto, el componente detecta el movimiento.
2. Si el usuario arrastra hacia arriba, se aumenta dinámicamente la altura de la caja.
3. Al soltar, una animación devuelve la altura de la caja a su valor inicial.

¿Te gustaría ajustar algo o agregar funcionalidad adicional, como límites de altura? 😊
