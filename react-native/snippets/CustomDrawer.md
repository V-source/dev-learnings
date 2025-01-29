
```tsx 
import { Drawer } from "expo-router/drawer";
import React from "react";
import { View, Text, TouchableOpacity } from "react-native";
import { DrawerContentComponentProps } from "@react-navigation/drawer";

function CustomDrawerContent({ navigation }: DrawerContentComponentProps) {
  return (
    <View style={{ flex: 1, backgroundColor: 'white', marginTop: 0, borderTopRightRadius: 16, borderBottomRightRadius: 16, overflow: 'hidden' }}>
      <View style={{marginTop: 117, flex: 1,}}>
        <TouchableOpacity onPress={() => navigation.navigate("index")} style={{ padding: 20, borderWidth: 0 }}>
          <Text style={{ color: '#242424' }}>Home</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={() => navigation.navigate("explore")} style={{ padding: 20, borderWidth: 0, }}>
          <Text style={{ color: '#242424' }}>Explore</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={() => navigation.navigate("explore")} style={{ padding: 20, marginTop: 'auto', backgroundColor: 'lightblue' }}>
          <Text style={{ color: '#242424' }}>Explore</Text>
        </TouchableOpacity>
      </View>
    </View>
  );
}

export default function CustomDrawer() {
  return (
    <Drawer
      drawerContent={(props: DrawerContentComponentProps) => <CustomDrawerContent {...props} />}
      screenOptions={{
        drawerStyle: {
          backgroundColor: '#ffffff50',
          width: 250,
        },
        headerStyle: {
          backgroundColor: 'white',
        },
        headerTintColor: '#242424',
      }}
    >
      <Drawer.Screen
        name="index"
        options={{
          title: "Home",
        }}
      />
      <Drawer.Screen
        name="explore"
        options={{
          title: "Explore",
        }}
      />
    </Drawer>
  );
}
```
