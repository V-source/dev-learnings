```typescript
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';
import { Tabs, TabList, TabTrigger, TabSlot } from 'expo-router/ui';

const App: React.FC = () => {
  return (
    <View style={styles.container}>
      <Tabs>
        <TabList>
          <TabTrigger value="tab1">Pestaña 1</TabTrigger>
          <TabTrigger value="tab2">Pestaña 2</TabTrigger>
          <TabTrigger value="tab3">Pestaña 3</TabTrigger>
        </TabList>

        <TabSlot value="tab1">
          <View style={styles.tabContent}>
            <Text style={styles.tabText}>Contenido de la Pestaña 1</Text>
          </View>
        </TabSlot>

        <TabSlot value="tab2">
          <View style={styles.tabContent}>
            <Text style={styles.tabText}>Contenido de la Pestaña 2</Text>
          </View>
        </TabSlot>

        <TabSlot value="tab3">
          <View style={styles.tabContent}>
            <Text style={styles.tabText}>Contenido de la Pestaña 3</Text>
          </View>
        </TabSlot>
      </Tabs>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 50, // Ajusta el margen superior según sea necesario
  },
  tabContent: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  tabText: {
    fontSize: 20,
    fontWeight: 'bold',
  },
});

export default App;
```

**Explicación:**

1. **Importaciones:**
   - `React`: Para crear el componente funcional.
   - `View`, `Text`, `StyleSheet`: Componentes nativos de React Native para construir la interfaz de usuario.
   - `Tabs`, `TabList`, `TabTrigger`, `TabSlot`: Componentes de la librería `expo-router/ui` para crear la navegación por pestañas.

2. **Componente `App`:**
   - Envuelve todo el contenido de las pestañas con el componente `Tabs`.
   - Utiliza `TabList` para definir la lista de pestañas.
   - `TabTrigger` define cada pestaña individual con un `value` único y un texto visible para el usuario.
   - `TabSlot` define el contenido que se mostrará para cada pestaña, utilizando el mismo `value` que el `TabTrigger` correspondiente.

3. **Estilos `StyleSheet`:**
   - Define los estilos para el contenedor principal y el contenido de cada pestaña.
   - Puedes personalizar los estilos según tus necesidades.

**Cómo funciona:**

- El componente `Tabs` gestiona el estado de la pestaña activa.
- `TabList` muestra las pestañas como botones en la parte superior.
- Al hacer clic en un `TabTrigger`, se cambia el estado de la pestaña activa.
- `TabSlot` muestra el contenido correspondiente a la pestaña activa según su `value`.

**Personalización:**

- Puedes personalizar el aspecto de las pestañas y el contenido utilizando los estilos de `StyleSheet`.
- Puedes agregar más pestañas y contenido según sea necesario.
- Puedes utilizar otros componentes dentro de `TabSlot` para crear interfaces de usuario más complejas.

