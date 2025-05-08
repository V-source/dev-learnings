```tsx



import scale from '@/utils/scale';
import { LinearGradient } from 'expo-linear-gradient';
import React, { ReactNode } from 'react';
import { Text, View, StyleSheet, Image } from 'react-native';
import { ResponsiveGrid } from 'react-native-flexible-grid';

interface Item {
  imageUrl: ReactNode;
  widthRatio: number;
  heightRatio: number;
  text: string;
  id: number;
}

interface RenderItemProps {
  item: Item;
  index: number;
}
export default function App() {

const ej = (

    <LinearGradient 
      colors={["rgb(80, 152, 236)", "rgb(0, 71, 255)"]}
      start={{ x: 1.5, y: 0 }}
      end={{ x: 2, y: 2 }}
      style={[ styles.box, {position: 'relative',} ]}
    >

      <View style={[{ 
        justifyContent: 'center', 
        alignItems: 'center',
        width: scale(21), 
        height: scale(21),
        position: 'absolute', 
        top: scale(5), 
        right: scale(5), 
        backgroundColor: '#E36F5F', 
        padding: scale(4), 
        borderRadius: scale(10)

      }]}>

        <Text style={[styles.whiteText, {fontSize: scale(10), fontWeight: '900'}]}>3</Text>
      </View>
        <Text style={[styles.title]}>Facturas Pendientes</Text>
        <View style={{ flexDirection: 'row',  justifyContent: 'space-between', gap: scale(13)}}>
          <Text style={[ styles.whiteText, {flex: 2,}]} numberOfLines={1} ellipsizeMode="tail">Nº 197656 Plan residencial molestiae</Text>
          <Text style={[styles.label]}>Bs 6.330</Text>
        </View>
        {/* <View style={{ flexDirection: 'row', justifyContent: 'space-between', gap: scale(13)}}> */}
        {/*   <Text style={[{flex: 2}]} numberOfLines={1} ellipsizeMode="tail">Nº 197657 Plan residencial mega 160MB</Text> */}
        {/*   <Text style={[styles.label]}>Bs 6.330</Text> */}
        {/* </View> */}
      </LinearGradient>
)

  const bdv = (
      <LinearGradient 
      style={[styles.box, { alignItems: 'center',}]}
      colors={["rgb(80, 152, 236)", "rgb(0, 71, 255)"]}
      start={{ x: 1.5, y: 0 }}
      end={{ x: 2, y: 2 }}
    >
        <Text style={[styles.whiteText]}>Tasa BCV</Text>
        <Text style={[styles.label]}>Bs 91.00</Text>
      </LinearGradient>
  )

const planes = (
      <LinearGradient 
      style={[styles.box]}
      colors={["rgb(80, 152, 236)", "rgb(0, 71, 255)"]}
      start={{ x: 1.5, y: 0 }}
      end={{ x: 2, y: 2 }}
    >
        <Text style={[styles.whiteText]}>Servicios/Planes</Text>
        <View style={{ flexDirection: 'row', justifyContent: 'space-between', gap: scale(13)}}>
          <Text style={[styles.whiteText, ]}
        >Activo |</Text>
          <Text 
          numberOfLines={2}
          ellipsizeMode="tail"
          style={[styles.whiteText, {flex: 2,}]}>
          Lorem ipsum dolor sit amet consectetur adipisicing elit. Velit, nemo!
        </Text>
          <Text style={[styles.whiteText]}>$24.00</Text>
        </View>
      </LinearGradient>
)

  const referidos = (
      <LinearGradient 
      style={[styles.box, {position: 'relative'} ]}
      colors={["rgb(80, 152, 236)", "rgb(0, 71, 255)"]}
      start={{ x: 1.5, y: 0 }}
      end={{ x: 2, y: 2 }}
    >
        <View style={{ flexDirection: 'row', justifyContent: 'space-between', gap: scale(13)}}>
        <Text style={[styles.whiteText]}>Referidos</Text>
        <Text style={[styles.whiteText]}>4</Text>
        </View>
        <View style={{ flexDirection: 'row', justifyContent: 'space-between', gap: scale(13)}}>
        <Text style={[styles.whiteText]}>Balance:</Text>
        <Text style={[styles.whiteText]}>$4.0</Text>
        </View>
      </LinearGradient>
  )

  const monedero = (
    <LinearGradient
      colors={["rgb(80, 152, 236)", "rgb(0, 71, 255)"]}
      start={{ x: 1.5, y: 0 }}
      end={{ x: 2, y: 2 }}
      style={[styles.box]}
    >
         <Text style={[styles.title]}>Saldo en Monedero</Text>
         <Text style={[styles.title]}>$31.0</Text>
       </LinearGradient>
  )

  const data: Item[] = [
    { imageUrl: ej, widthRatio: 3, heightRatio: 1, text: "Item 1", id: 1 },
    { imageUrl: bdv, widthRatio: 2, heightRatio: 1, text: "Item 2", id: 2 },
    { imageUrl: planes, widthRatio: 4, heightRatio: 1, text: "Item 3", id: 3 },
    { imageUrl: referidos, widthRatio: 2, heightRatio: 1, text: "Item 4", id: 4 },
    { imageUrl: monedero, widthRatio: 3, heightRatio: 1, text: "Item 4", id: 5 },
    // { imageUrl: 'https://i.pinimg.com/737x/c0/fd/c1/c0fdc1b184cb7014e48c2f2df456d129.jpg', widthRatio: 3, heightRatio: 2, text: "Item 5", id: 5 },
  ];

  const renderItem = ({ item }: RenderItemProps) => (
    <View style={styles.boxContainer}>
        {item.imageUrl}
    </View>

  );

  return (
    <View style={styles.container}>
      <ResponsiveGrid
        keyExtractor={(item) => item.id.toString()}
        maxItemsPerColumn={4}
        data={data}
        renderItem={renderItem}
        itemUnitHeight={81}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: scale(10),
    backgroundColor: '#fff',
  },
  boxContainer: {
    flex: 1,
    margin: scale(5),
    // backgroundColor: 'red',
  },
  box: {
    flex: 1,
    backgroundColor: '#fff',
    elevation: scale(3),
    borderRadius: 12,
    padding: scale(12),
    justifyContent: 'center',
  },
  title: {
    fontSize: scale(16),
    marginBottom: scale(8),
    fontWeight: 500,
    color: '#fff',
  },
  label: {
    fontWeight: 500,
    color: '#fff',
  },
  whiteText: {
    color: '#fff',
  }
  // item: {
  //   borderRadius: scale(12),
  // },
});




// import scale from '@/utils/scale';
// import React from 'react';
// import { View, Text, StyleSheet } from 'react-native';
//
// const Testui = () => {
//   return (
//     <View style={styles.container}>
//       {/* Primera fila */}
//
//       <View style={[styles.item, styles.square, { width: '68%',}]}>
//         <Text style={{marginBottom: scale(8)}}>Facturas Pendientes</Text>
//         <View style={{ flexDirection: 'row',  justifyContent: 'space-between', gap: scale(12)}}>
//           <Text style={{flex: 1}} numberOfLines={1} ellipsizeMode="tail">Nº 197656 Plan residencial molestiae</Text>
//           <Text style={{}}>Bs 5.330</Text>
//         </View>
//         <View style={{ flexDirection: 'row', justifyContent: 'space-between', gap: scale(12)}}>
//           <Text style={{flex: 1}} numberOfLines={1} ellipsizeMode="tail">Nº 197657 Plan residencial mega 160MB</Text>
//           <Text>Bs 5.330</Text>
//         </View>
//       </View>
//       <View style={[styles.item, styles.square]}>
//         <Text>Tasa BCV</Text>
//         <Text>Bs 90.00</Text>
//       </View>
//       <View style={[styles.item, styles.wide]}>
//         <Text>Servicios/Planes</Text>
//         <View style={{ flexDirection: 'row', justifyContent: 'space-between', gap: scale(12)}}>
//           <Text>Lorem ipsum dolor sit.</Text>
//           <Text>$23.00</Text>
//         </View>
//       </View>
//
//       <View style={[styles.item, styles.wide]} />
//
//       {/* Segunda fila */}
//       <View style={[styles.item, styles.square]}>
//         <Text>Referidos:      3</Text>
//         <Text>Balance:   $3.0</Text>
//       </View>
//       <View style={[styles.item, styles.square]}>
//         <Text>Saldo en Monedero</Text>
//         <Text>$30.0</Text>
//       </View>
//       <View style={[styles.item, styles.square]} />
//       <View style={[styles.item, styles.wide]} />
//       <View style={[styles.item, styles.square]} />
//
//       {/* Tercera fila */}
//       <View style={[styles.item, styles.wide]} />
//       <View style={[styles.item, styles.square]} />
//       <View style={[styles.item, styles.square]} />
//     </View>
//   );
// };
//
// const styles = StyleSheet.create({
//   container: {
//     flex: 1,
//     flexDirection: 'row',
//     // justifyContent: 'space-evenly',
//     flexWrap: 'wrap',
//     padding: scale(12),
//     backgroundColor: '#f8f8f8',
//     gap: scale(12),
//
//   },
//   item: {
//     elevation: scale(4),
//     borderRadius: scale(12),
//     // margin: 4,
//     backgroundColor: '#fff',
//     padding: scale(12),
//     // alignItems: 'center',
//   },
//   square: {
//     borderRadius: scale(12),
//     width: 'auto',
//     height: 'auto',
//     // justifyContent: 'space-around',
//   },
//   wide: {
//     elevation: scale(4),
//     borderRadius: scale(12),
//     width: '100%',
//     // height: 100,
//     // width: 'auto',
//     height: 'auto',
//   }
// });
//
// export default Testui;
```
