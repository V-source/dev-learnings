```tsx

import { Alert, ActivityIndicator } from 'react-native'
import { storeUserData } from "@/utils/secureStore";
import { useState, useEffect, useRef } from "react";
import { router } from 'expo-router';

export function useFetch<T>(url: string, options: Record<string, any> = {}) {
  const [data, setData] = useState<T | null>(null);
  const [error, setError] = useState<T | null>(null);
  const [loading, setLoading] = useState(false);
  
  // Referencia para mantener el controlador de abort
  const abortControllerRef = useRef<AbortController | null>(null);

  const handleErrors: {[key: string | number]: (url:string) => void} = {
    422: () => {
      if (url.includes('login')) {
        Alert.alert("Usuario y/o Credenciales Incorrectos");
      } else {
        Alert.alert('Datos  Incorrectos')
      }
    },
    400: () => {
      Alert.alert('No se pudo procesar tu solicitud,\nVuelva a intentarlo.')
    },
    404: () => {
      Alert.alert('Recurso no encontrado')
    },
    500: () => {
      Alert.alert('No se estableció conexión con el servicio.')
    },
    401: () => {
      Alert.alert('Tu sesión ha Expirado. Vuelve a iniciar sesión')
      router.replace('/login')
    },
    403: () => {
      Alert.alert('No tienes permisos para esta acción')
    },
    406: () => {
      Alert.alert('Nueva versión disponible, descargala ya!')
    }
  }


  const fetching = async () => {
    // Cancelar cualquier petición anterior
    if (abortControllerRef.current) {
      abortControllerRef.current.abort();
    }
    console.log('fetching')
    setLoading(true);
    setError(null); 

    // Crear nuevo controlador de abort
    abortControllerRef.current = new AbortController();

    try {
      const response = await fetch(url, {
        ...options,
        signal: abortControllerRef.current.signal
      });

      if (!response.ok) {
        handleErrors[response.status]?.(response.url);
        // throw new Error(`${response.status}`);
      }

      const json = await response.json();
      // console.log(json)
      setData(json);
      if(response.status === 422) {
       setError(json.errors); 
      }
    } catch (err: any) {
      // Ignorar errores de abort
      if (err.name === 'AbortError') {
        console.log('Petición cancelada');
        return;
      }
      
      setError(err);
      console.log(err.message);
    } finally {
      setLoading(false);
    }
  };

  // Limpiar el controlador al desmontar
  useEffect(() => {
    return () => {
      if (abortControllerRef.current) {
        abortControllerRef.current.abort();
      }
    };
  }, []);

  return { fetching, data, error, loading, setData };
}

```
