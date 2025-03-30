# INICIO DE SESIÓN CON HUELLA DACTILAR O BIOMETRÍA, AQUÍ TIENES UN EJEMPLO COMPLETO QUE PUEDES INTEGRAR EN TU APLICACIÓN:


```javascript
import React, { useState, useEffect } from 'react';
import { View, Text, TouchableOpacity, StyleSheet } from 'react-native';
import * as SecureStore from 'expo-secure-store';
import * as LocalAuthentication from 'expo-local-authentication';

const LoginScreen = () => {
  const [isBiometricAvailable, setIsBiometricAvailable] = useState(false);
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  const [storedToken, setStoredToken] = useState(null);

  useEffect(() => {
    checkBiometricAvailability();
    checkPreviousAuthentication();
  }, []);

  const checkBiometricAvailability = async () => {
    const compatible = await LocalAuthentication.hasHardwareAsync();
    const enrolled = await LocalAuthentication.isEnrolledAsync();
    setIsBiometricAvailable(compatible && enrolled);
  };

  const checkPreviousAuthentication = async () => {
    const storedValue = await SecureStore.getItemAsync('sessionToken');
    setStoredToken(storedValue);
    if (storedValue) {
      setIsAuthenticated(true);
    }
  };

  const handleBiometricLogin = async () => {
    try {
      const authResult = await LocalAuthentication.authenticateAsync({
        promptMessage: 'Autenticación biométrica',
        cancelLabel: 'Cancelar',
        disableDeviceFallback: true,
        biometricsSecurityLevel: 'strong' // Solo huella dactilar o reconocimiento facial 3D
      });

      if (authResult.success) {
        // Una vez autenticado, guardamos el token de sesión
        await SecureStore.setItemAsync(
          'sessionToken',
          'tu_token_aqui',
          {
            requireAuthentication: true,
            authenticationPrompt: 'Autenticación requerida'
          }
        );
        setIsAuthenticated(true);
      }
    } catch (error) {
      console.error('Error en autenticación:', error);
    }
  };

  const handleLogout = async () => {
    try {
      await SecureStore.deleteItemAsync('sessionToken');
      setIsAuthenticated(false);
      setStoredToken(null);
    } catch (error) {
      console.error('Error al cerrar sesión:', error);
    }
  };

  return (
    <View style={styles.container}>
      {!isAuthenticated ? (
        <>
          <Text style={styles.title}>Inicio de Sesión Biométrico</Text>
          {isBiometricAvailable ? (
            <TouchableOpacity 
              style={styles.button}
              onPress={handleBiometricLogin}
            >
              <Text style={styles.buttonText}>
                Iniciar Sesión con Huella Dactilar
              </Text>
            </TouchableOpacity>
          ) : (
            <Text style={styles.errorText}>
              Dispositivo no compatible con autenticación biométrica
            </Text>
          )}
        </>
      ) : (
        <>
          <Text style={styles.title}>Sesión Iniciada</Text>
          <TouchableOpacity 
            style={styles.logoutButton}
            onPress={handleLogout}
          >
            <Text style={styles.buttonText}>Cerrar Sesión</Text>
          </TouchableOpacity>
        </>
      )}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20
  },
  title: {
    fontSize: 24,
    marginBottom: 20,
    textAlign: 'center'
  },
  button: {
    backgroundColor: '#007AFF',
    padding: 15,
    borderRadius: 8,
    width: '100%',
    marginBottom: 10
  },
  logoutButton: {
    backgroundColor: '#ff4444',
    padding: 15,
    borderRadius: 8,
    width: '100%'
  },
  buttonText: {
    color: 'white',
    textAlign: 'center',
    fontSize: 16
  },
  errorText: {
    color: 'red',
    textAlign: 'center',
    marginBottom: 20
  }
});

export default LoginScreen;
```

Para implementar este código, necesitas:

 Instalar las dependencias

```bash
npx expo install expo-local-authentication expo-secure-store
```

 Configurar en tu `app.json`

```json
{
  "expo": {
    "plugins": [
      [
        "expo-local-authentication",
        {
          "faceIDPermission": "Allow $(PRODUCT_NAME) to use Face ID"
        }
      ]
    ]
  }
}
```

Este código implementa:

- Verificación de compatibilidad con biometría
- Autenticación con huella dactilar o reconocimiento facial
- Almacenamiento seguro del token de sesión
- Opción de cierre de sesión
- Interfaz de usuario intuitiva

La autenticación biométrica es segura porque:

- Utiliza el sistema de autenticación nativo del dispositivo
- Los datos se almacenan cifrados 3:1
- Solo permite autenticación "strong" (huella dactilar o reconocimiento facial 3D) 4:12
- No almacena datos biométricos en la aplicación

¿Necesitas alguna aclaración sobre la implementación?
