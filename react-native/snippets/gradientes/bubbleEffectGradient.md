```tsx

import Button from "@/components/Button";
import { router } from "expo-router";
import { View, Text, StyleSheet } from "react-native";
import { LinearGradient } from "expo-linear-gradient";
import Svg, {
  Defs,
  RadialGradient as SvgRadialGradient,
  Rect,
  Stop,
  Circle,
} from "react-native-svg";
import { ReactNode } from "react";
import { SafeAreaView } from "react-native-safe-area-context";
import NewsCard from "@/components/NewsCard";

type RadialProps = {
  children: ReactNode;
};

function RadialGradientBackground({ children }: RadialProps) {
  return (
    <SafeAreaView style={{ flex: 1 }}>
      <View style={gradientStyles.container}>
        {/* Fondo con gradiente radial principal */}
        <Svg height="100%" width="100%" style={StyleSheet.absoluteFillObject}>
          <Defs>
            {/* Gradiente radial principal */}
            <SvgRadialGradient
              id="mainGradient"
              cx="50%"
              cy="50%"
              rx="100%"
              ry="50%"
              fx="50%"
              fy="50%"
              gradientUnits="userSpaceOnUse"
            >
          <Stop offset="0%" stopColor="#0047ff" stopOpacity="1" />
              <Stop offset="50%" stopColor="#38fcfc" stopOpacity="1" />
              <Stop offset="100%" stopColor="#0047ff" stopOpacity="1" />
            </SvgRadialGradient>

            {/* Gradiente radial para las burbujas */}
            <SvgRadialGradient
              id="bubbleGradient"
              cx="50%"
              cy="50%"
              rx="50%"
              ry="50%"
              fx="50%"
              fy="50%"
              gradientUnits="userSpaceOnUse"
            >
              <Stop offset="0%" stopColor="#38fcfc" stopOpacity="0.8" />
              <Stop offset="100%" stopColor="#0047ff" stopOpacity="0.8" />
            </SvgRadialGradient>
          </Defs>

          {/* Fondo principal */}
          <Rect
            x="0"
            y="0"
            width="100%"
            height="100%"
            fill="url(#mainGradient)"
          />

          {/* Burbujas */}
          <Circle cx="20%" cy="30%" r="50" fill="url(#bubbleGradient)" />
          <Circle cx="70%" cy="20%" r="40" fill="url(#bubbleGradient)" />
          <Circle cx="50%" cy="60%" r="60" fill="url(#bubbleGradient)" />
          <Circle cx="80%" cy="70%" r="30" fill="url(#bubbleGradient)" />
          <Circle cx="10%" cy="80%" r="50" fill="url(#bubbleGradient)" />
        </Svg>

        {/* Children se renderiza aquí */}
        <View style={[gradientStyles.chidlrenContainer]}> {children} </View>
      </View>
    </SafeAreaView>
  );
}

export default function News() {
  return (
    <RadialGradientBackground>
      <View style={[{ borderWidth: 0, alignItems: "center" }]}>
        <Text style={[gradientStyles.title]}>Gradiente con burbujas</Text>
      </View>
    </RadialGradientBackground>
  );
}

// child container
const gradientStyles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
  },
  title: {
    borderWidth: 0,
    padding: 12,
    fontSize: 27,
    fontWeight: "regular",
    color: "#fff",
    textShadowRadius: 4,
  },
  chidlrenContainer: {
    flex: 1,
    padding: 25,
    gap: 22,
    backgroundColor: "#0047ff37",
  },
});

```
