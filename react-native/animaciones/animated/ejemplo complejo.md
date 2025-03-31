```tsx

import {
  ActivityIndicator,
  View,
  Animated,
  Image,
  StyleSheet,
  Platform,
  TextInput,
  TouchableOpacity,
  Text,
} from "react-native";

import { router, useNavigation } from "expo-router";
import * as Device from "expo-device";
import { HelloWave } from "@/components/HelloWave";
import ParallaxScrollView from "@/components/ParallaxScrollView";
import { ThemedText } from "@/components/ThemedText";
import { ThemedView } from "@/components/ThemedView";
import scale from "@/utils/scale";
import { useEffect, useState, useRef } from "react";
import CustomInput from "@/components/TextInput";
import { useFetch } from "@/hooks/useFetch";
import { urls } from "@/utils/urls";
import { useFet } from "@/hooks/useFet";
import { storeUserData } from "@/utils/secureStore";

export default function HomeScreen() {
  const [loginData, setLoginData] = useState({email: '', password:'', remember: true})
  const [isSession, setIsSession] = useState(false)

  const options = {
    method: "POST",
    headers: {
      Accept: "application/json",
      "Content-Type": "application/json",
      "User-Agent": `Customer-Area/${process.env.EXPO_PUBLIC_VERSION} ${Device.osName}/${Device.osVersion} ${Device.brand} ${Device.modelName}`,
    },
    body: JSON.stringify(loginData),
  };
  const { fetching, data, error, loading } = useFet(urls.loginURL, options);
  const fadeAnim = useRef(new Animated.Value(0)).current;
  const anim2 = useRef(new Animated.Value(0)).current;
  const anim3 = useRef(new Animated.Value(0)).current;
  const anim4 = useRef(new Animated.Value(0)).current;

  const fadeIn = (duration: number, toValue?: number) => {
    Animated.timing(fadeAnim, {
      toValue: 1,
      duration: duration,
      useNativeDriver: true,
    }).start();
  };

  const fadeOut = (duration: number, toValue?: number) => {
    Animated.timing(fadeAnim, {
      toValue: 0,
      duration: duration,
      useNativeDriver: true,
    }).start();
  };

  const sequencAnim = (delay: number, duration: number, toValue?: number) => {
    Animated.stagger(300, [
      Animated.timing(fadeAnim, {
        toValue: 1,
        duration: duration,
        useNativeDriver: true,
      }),
      Animated.timing(anim2, {
        toValue: 1,
        duration: duration,
        useNativeDriver: true,
      }),
      Animated.timing(anim3, {
        toValue: 1,
        duration: duration,
        useNativeDriver: true,
      }),
      Animated.timing(anim4, {
        toValue: 1,
        duration: duration,
        useNativeDriver: true,
      }),
    ]).start();
  };

  // effect
  useEffect(() => {
    
    sequencAnim(200, 1000);
      
    if(data) {
        storeUserData(data)
        router.replace('/(tabs)')
    }

    return () => {
      fadeAnim.setValue(0);
      anim2.setValue(0);
      anim3.setValue(0);
      anim4.setValue(0);
    };

  }, [data]);

  if(loading) {
    return (
      <View style={{flex: 1, justifyContent: 'center', alignItems: 'center'}}>
        <ActivityIndicator size='large' />
      </View>
    )
  }
  return (
    <ThemedView style={styles.container}>
      {/* logo */}
      <Animated.View style={[ styles.logo, { marginBottom: scale(56), opacity: fadeAnim, scaleX: fadeAnim, scaleY: fadeAnim,
      }, ]}>
        <Image source={require("@/assets/images/logosnet.png")} resizeMode="center" style={{ width: "100%" , height: "100%"
        }} />
      </Animated.View>

      {/* form */}
      <Animated.View style={{ marginBottom: scale(66), gap: scale(25), borderWidth: 0, width: "100%" , opacity: anim2,
        scaleY: anim2, scaleX: anim2, }}>
        <CustomInput placeholder="email@example.com" setData={(value: string)=> {
          setLoginData({ ...loginData, email: value });
        }}
          labelText="Correo"
        />
        <CustomInput placeholder="*********" setData={(value: string)=> {
          setLoginData({ ...loginData, password: value });
        }}
          labelText="Contraseña"
          secure={true}
        />
      </Animated.View>

      {/* button */}
      <Animated.View style={{ marginBottom: scale(48), width: scale(164), borderWidth: 0, opacity: anim2, scaleY: anim3,
        scaleX: anim3, }}>
        <TouchableOpacity style={{ elevation: scale(8), backgroundColor: "#1565C0" , paddingVertical: scale(8),
          paddingHorizontal: scale(12), borderRadius: scale(8), }} onPress={()=> {
            fetching();
          }}
        >
          <Text style={{ color: "#fff" , textAlign: "center" , fontWeight: 600, fontSize: scale(18), }}>
            Enviar
          </Text>
        </TouchableOpacity>
      </Animated.View>

      {/* recover password */}
      <Animated.View style={{ opacity: anim3, scaleX: anim4, scaleY: anim4 }}>
        <Text style={{ color: "#006CE5" , textAlign: "center" , fontSize: scale(16) }}>
          Recuperar/Generar{"\n"}Contraseña
        </Text>
      </Animated.View>
    </ThemedView>
  );
}

const styles = StyleSheet.create({
  container: {
    borderWidth: 0,
    borderColor: "red",
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    paddingHorizontal: scale(50),
  },

  logo: {
    width: "95%",
    height: scale(120),
  },

  titleContainer: {
    flexDirection: "row",
    alignItems: "center",
    gap: 8,
  },
  stepContainer: {
    gap: 8,
    marginBottom: 8,
  },
  reactLogo: {
    height: 178,
    width: 290,
    bottom: 0,
    left: 0,
    position: "absolute",
  },
});

```
