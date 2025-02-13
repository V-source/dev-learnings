```tsx
import { View, StyleSheet, Text, FlatList, TouchableOpacity } from "react-native";
import Card from "@/components/Card";
import Entypo from "@expo/vector-icons/Entypo";
import scale from "@/utils/scale";

const referrals = [
  {
    id: "1",
    name: "Referido 1",
    status: "activo",
  },
  {
    id: "2",
    name: "Referido 1",
    status: "activo",
  },
  {
    id: "3",
    name: "Referido 1",
    status: "activo",
  },
  {
    id: "4",
    name: "Referido 1",
    status: "activo",
  },
  {
    id: "5",
    name: "referido 1",
    status: "activo",
  },
  {
    id: "6",
    name: "referido 1",
    status: "activo",
  },
];

export default function ReferralsList() {
  return (
    <View style={{backgroundColor: '#fff', flex: 1}}>
      <FlatList
        style={{borderWidth: 0, borderColor: 'red',paddingVertical: scale(22),}}
        data={referrals}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => {
          return (
            <View style={[styles.container]}>
              <View style={[styles.bullet]}>
                {
                  referrals.indexOf(item) == 0 || <View style={[styles.bulletLineTop]} />
                }
                {
                   referrals.indexOf(item) == referrals.length - 1
                  ? null 
                  : (<View style={[styles.bulletLineBottom]} />)
                }
              </View>
              <TouchableOpacity
                style={[styles.referral]}
              >
                <Text style={[]}>Referido</Text>
              </TouchableOpacity>
              <View style={[styles.statusIcon]}></View>
            </View>
          );
        }}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    borderWidth: 0,
    position: "relative",
    flexDirection: "row",
    alignItems: "center",
    gap: scale(25),
    paddingHorizontal: scale(25),
    marginBottom: scale(12),
  },

  referral: {
    borderWidth: 0,
    flex: 1,
    padding: scale(12),
    elevation: scale(4),
    backgroundColor: '#fff',
    borderRadius: scale(10),
  },

  bullet: {
    width: scale(9),
    height: scale(9),
    borderRadius: 100,
    backgroundColor: '#242424',
    position: 'relative',
    alignItems: 'center',
  },

  bulletLineTop: {
    width: scale(2),
    position: 'absolute',
    backgroundColor: '#242424',
    height: scale(27),
    top: -27,
  },

  bulletLineBottom: {
    width: scale(2),
    height: scale(27),
    position: 'absolute',
    backgroundColor: '#242424',
    top: 7,
  },

  statusIcon: {
    borderWidth: 1,
  },
});

```
