```tsx
import React from 'react';
import { View, StyleSheet } from 'react-native';
import Svg, { Defs, RadialGradient as SvgRadialGradient, Rect, Stop } from 'react-native-svg';

const RadialGradientBackground = ({ children }) => {
  return (
    <View style={styles.container}>
      {/* Fondo con gradiente radial */}
      <Svg height="100%" width="100%" style={StyleSheet.absoluteFillObject}>
        <Defs>
          <SvgRadialGradient
            id="radialGradient"
            cx="50%"
            cy="50%"
            rx="50%"
            ry="50%"
            fx="50%"
            fy="50%"
            gradientUnits="userSpaceOnUse"
          >
            <Stop offset="0%" stopColor="#0047ff" stopOpacity="1" />
            <Stop offset="50%" stopColor="#38fcfc" stopOpacity="1" />
            <Stop offset="100%" stopColor="#0047ff" stopOpacity="1" />
          </SvgRadialGradient>
        </Defs>
        <Rect x="0" y="0" width="100%" height="100%" fill="url(#radialGradient)" />
      </Svg>

      {/* Children se renderiza aquí */}
      {children}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default RadialGradientBackground;

```
