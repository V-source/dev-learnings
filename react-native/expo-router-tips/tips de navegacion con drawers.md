# tip 1.

el valor de la propiedad name de `Drawer.Screen` debe ser el mismo nombre de la funcion exportada en en los archivos.
en este ejemplo se existen los archivos `index.tsx` y `dash.tsx`.
y exportan los los componentes `Home` y `Dash`.
de no hacerlo no aparecera el nombre en el los elementos del drawer.
el nombre que aparece en el drawer es el nombre del archivo
 asi que deben ser iguales
```tsx
// mala implementtacion

      <Drawer.Screen name="index" options={{ title: "" }} />
      <Drawer.Screen name="dash" options={{ title: "" }} />
```
