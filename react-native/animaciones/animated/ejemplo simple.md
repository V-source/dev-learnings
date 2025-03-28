```tsx

import scale from "@/utils/scale";
import { useEffect, useRef, useState } from "react";
import { Animated, StyleSheet, Image, TouchableOpacity, View, Text } from "react-native";

export default function uiTest() {

  const [show, setShow] = useState<boolean>(false);
  const fadeAnimation = useRef(new Animated.Value(0)).current;
  const animationDuration = 300; // Duración de la animación en milisegundos

  const fadeIn = (duration: number) => {
    Animated.timing(fadeAnimation, {
      toValue: 1,
      duration: duration,
      useNativeDriver: true,
    }).start();
  };

  const fadeOut = (duration: number) => {
    Animated.timing(fadeAnimation, {
      toValue: 0,
      duration: duration,
      useNativeDriver: true,
    }).start(() => {
      // Ocultar el contenido después de la animación de fadeOut
      if (!show) {
        // Puedes realizar alguna acción aquí si es necesario, como desmontar componentes
      }
    });
  };

  useEffect(() => {
    if (show) {
      fadeIn(animationDuration);
    } else {
      fadeOut(animationDuration);
    }
  }, [show]);

  return (
    <View style={[styles.container]}>
      <View>
        <TouchableOpacity
          onPress={() => {
            setShow(!show);
          }}
        >
          <Text>show</Text>
        </TouchableOpacity>
      </View>

      {/* {show && ( */}
        <Animated.View style={[styles.formShow, { opacity: fadeAnimation, scaleX: fadeAnimation, scaleY: fadeAnimation }]}>
          {/* header - logo de banco */}
          <View style={[styles.hiddenView]}>
            <Image style={[styles.image]} resizeMode="cover" source={require('../../../assets/images/logoBdv.webp')} />
          </View>
          {/* titulo */}
          <Text> formulario bdv </Text>
          {/* formulario */}
        </Animated.View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: scale(25),
  },

  formHidden: {
    padding: scale(0),
    borderWidth: 0,
    height: 0,
    overflow: 'hidden',
  },

  formShow: {
    padding: scale(12),
    borderWidth: 1,
    flex: 1,
  },
  hiddenView: {
    flex: 1,
  },

  image: {
    width: '100%',
    height: 50,
  },
});

```
