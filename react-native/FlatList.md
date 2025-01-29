# elemento `FlatList`


```jsx
    <FlatList
      data={[]}
      renderItem={({item}) => <Component />}
      keyExtractor={item => item.id}
    />

```

---

### **1. `data`**
- **Propósito**: Proporciona la fuente de datos que el `FlatList` debe renderizar.
- **Tipo**: `Array`
- **Detalles**:
  - Es un arreglo que contiene los elementos a mostrar.
  - Cada elemento del arreglo será pasado como `item` al `renderItem`.
  - Si el arreglo está vacío (`data={[]}`), el `FlatList` no mostrará nada.
- **Ejemplo**:
  ```javascript
  const data = [
    { id: '1', name: 'Juan' },
    { id: '2', name: 'Ana' },
  ];
  ```

---

### **2. `renderItem`**
- **Propósito**: Define cómo se renderiza cada elemento del arreglo `data`.
- **Tipo**: `(info: { item: any; index: number }) => React.Element`
- **Detalles**:
  - Es una función que toma como argumento un objeto con dos propiedades principales:
    - **`item`**: El elemento del arreglo actual que está siendo renderizado.
        - si es un arreglo de objetos podemos acceder a las propiedades del objeto mediante `item.propiedadDelOjeto`.
    - **`index`**: La posición del elemento en el arreglo (opcional, no utilizado en este ejemplo).
  - Debe devolver un componente de React para representar visualmente el elemento.
- **Ejemplo**:
  ```javascript
  renderItem={({ item }) => <Text>{item.name}</Text>}
  ```

---

### **3. `keyExtractor`**
- **Propósito**: Proporciona una clave única para cada elemento renderizado.
- **Tipo**: `(item: any, index: number) => string`
- **Detalles**:
  - FlatList usa esta clave para identificar de forma única cada elemento y optimizar el rendimiento del renderizado.
  - La clave debe ser única para cada elemento y debe ser consistente entre renders.
  - Si los elementos tienen una propiedad única (como un `id`), esta propiedad se suele usar como clave.
- **Ejemplo**:
  ```javascript
  keyExtractor={(item) => item.id}
  ```

---

### Cómo Funciona Este `FlatList`

1. **Entrada de Datos (`data`)**:
   - `FlatList` espera un arreglo en `data`.
   - En este caso, el arreglo está vacío (`data={[]}`), por lo que no se renderiza nada.

2. **Renderizado de Elementos (`renderItem`)**:
   - La función `renderItem` especifica cómo se verá cada elemento de la lista.
   - El `item` actual se pasa como argumento, pero no se está usando en este ejemplo:
     ```javascript
     renderItem={({ item }) => <Component />}
     ```
   - Aquí, siempre se renderiza el mismo componente `<Component />`, sin importar el contenido de `item`.

3. **Claves Únicas (`keyExtractor`)**:
   - Aunque `data` está vacío, si tuviera datos, `keyExtractor` extraería la clave única de cada `item` usando su propiedad `id`.

---

### Ejemplo Completo con Datos

```tsx
import React from 'react';
import { FlatList, Text, View, StyleSheet } from 'react-native';

const App = () => {
  const data = [
    { id: '1', name: 'Juan' },
    { id: '2', name: 'Ana' },
    { id: '3', name: 'Carlos' },
  ];

  return (
    <FlatList
      data={data} // Datos a renderizar
      renderItem={({ item }) => (
        <View style={styles.item}>
          <Text>{item.name}</Text>
        </View>
      )} // Renderizado de cada elemento
      keyExtractor={(item) => item.id} // Clave única para cada elemento
    />
  );
};

const styles = StyleSheet.create({
  item: {
    padding: 16,
    borderBottomWidth: 1,
    borderBottomColor: '#ddd',
  },
});

export default App;
```

---

### Propiedades Adicionales de `FlatList`

- **`ListHeaderComponent`**: Renderiza un componente en la parte superior de la lista.
- **`ListFooterComponent`**: Renderiza un componente en la parte inferior de la lista.
- **`onEndReached`**: Función que se ejecuta cuando el usuario llega al final de la lista (útil para paginación).
- **`horizontal`**: Si se establece en `true`, convierte la lista en horizontal.

¿Te gustaría explorar alguna propiedad adicional? 😊
