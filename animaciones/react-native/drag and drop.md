# AUMENTAR ALTURA DE UN ELEMENTO


```jsx jsx

import React, { useRef } from "react";
import {
  View,
  Animated,
  PanResponder,
  StyleSheet,
  Text,
  Dimensions,
} from "react-native";

const { height: SCREEN_HEIGHT } = Dimensions.get("window");

const DragToResize = () => {
  const heightAnim = useRef(new Animated.Value(150)).current; // Altura inicial de la caja
  const panResponder = useRef(
    PanResponder.create({
      onStartShouldSetPanResponder: () => true,
      onPanResponderMove: (_, gestureState) => {
        // Solo aumenta la altura si el usuario arrastra hacia arriba
        if (gestureState.dy < 0) {
          heightAnim.setValue(150 - gestureState.dy); // Reducimos el valor negativo de dy para aumentar la altura
        }
      },
      onPanResponderRelease: () => {
        // Animar suavemente la altura cuando se suelta
        Animated.spring(heightAnim, {
          toValue: 150, // Volver a la altura inicial
          useNativeDriver: false,
        }).start();
      },
    })
  ).current;

  return (
    <View style={styles.container}>
    {/* Barra o Botón para arrastrar */}
      <Animated.View
        {...panResponder.panHandlers}
        style={styles.dragHandle}
      >
        <Text style={styles.handleText}>⬆️ Arrastra aquí</Text>
      </Animated.View>
      <Animated.View
        style={[
          styles.resizableBox,
          { height: heightAnim }, // Altura animada
        ]}
      >
        <Text style={styles.boxText}>Arrastra hacia arriba</Text>
      </Animated.View>

      
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "flex-end", // Coloca la caja en la parte inferior
    alignItems: "center",
    backgroundColor: "#f5f5f5",
  },
  resizableBox: {
    width: "100%",
    backgroundColor: "#4CAF50",
    justifyContent: "center",
    alignItems: "center",
  },
  boxText: {
    color: "#fff",
    fontWeight: "bold",
    fontSize: 16,
  },
  dragHandle: {
    width: "100%",
    height: 50,
    backgroundColor: "#333",
    justifyContent: "center",
    alignItems: "center",
  },
  handleText: {
    color: "#fff",
    fontSize: 14,
    fontWeight: "bold",
  },
});

export default DragToResize;
```


