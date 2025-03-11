# `useLocalSearchParams()`

> [!NOTE]
> no recibe parametros.

Su función es simplemente recuperar los parámetros de búsqueda de la URL actual y devolverlos como un objeto.

**Ejemplo**:

```tsx


import { useLocalSearchParams } from 'expo-router';

function MiComponente() {
  const params = useLocalSearchParams();

  // params es un objeto que contiene los parámetros de búsqueda
  // Ejemplo: { id: '123', nombre: 'Juan' }

  // ...
}
```
