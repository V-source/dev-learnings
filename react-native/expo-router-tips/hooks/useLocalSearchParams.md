# `useLocalSearchParams()`

Su función es simplemente recuperar los parámetros de búsqueda de la URL actual y devolverlos como un objeto.

> [!NOTE]
> no recibe parametros.

> [!NOTE]Consideraciones:
> - useLocalSearchParams() devuelve un objeto que puede estar vacío si no hay parámetros de búsqueda en la URL.
> - Los valores de los parámetros de búsqueda se devuelven como cadenas de texto.
> - Si necesitas convertir los valores a otros tipos (por ejemplo, números), debes hacerlo manualmente.


**Ejemplo de uso**:

```tsx


import { useLocalSearchParams } from 'expo-router';

function MiComponente() {
  const params = useLocalSearchParams();

  // params es un objeto que contiene los parámetros de búsqueda
  // Ejemplo: { id: '123', nombre: 'Juan' }

  // ...
}
```

