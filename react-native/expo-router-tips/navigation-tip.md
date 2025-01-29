La prop `{ navigation }` se le pasa a `CustomDrawerContent` porque es proporcionada automáticamente por `expo-router/drawer`.  

### **Motivo**:
`expo-router/drawer` utiliza `react-navigation` internamente, lo que significa que el contenido del Drawer (menú lateral) necesita acceso a la navegación para poder cambiar de pantalla cuando el usuario toca un botón en el Drawer.  

### **Explicación**:
- `drawerContent={(props) => <CustomDrawerContent {...props} />}`  
  - Esto pasa todas las props que `Drawer` proporciona, incluyendo `{ navigation }`.
- `navigation.navigate("index")`  
  - Esto permite que al tocar el botón de "Home", la navegación cambie a la pantalla `"index"`.
- `navigation` proviene del `Drawer.Navigator`, y sin esta prop, los botones no podrían cambiar de pantalla dentro del Drawer.  

Si necesitas personalizar más, puedes usar `useNavigation()` de `react-navigation` en vez de recibir `{ navigation }` como prop. 🚀
