```tsx

import React, { useState, useEffect } from 'react';
import { View, Text, FlatList, ActivityIndicator } from 'react-native';
import * as Device from 'expo-device'; // Asegúrate de instalar expo-device
import { useFetch } from './useFetch'; // Asegúrate de que la ruta sea correcta
import { getTok } from './auth'; // Asegúrate de que la ruta sea correcta

interface Factura {
  id: number;
  servicio_id: number;
  estado: string;
  saldo: number;
  descuento: number;
  impuestos_total: number;
  pasarela: {
    id: number | null;
    url: string | null;
    banco: string | null;
  };
  fecha_emision: string;
  fecha_pago: string | null;
  fecha_vencimiento: string;
  precio: number;
  total: number;
  pagado: number;
  pendiente: number;
  deuda: number;
  is_for_pasarela: boolean;
  is_pagable: boolean;
}

interface FacturasResponse {
  current_page: number;
  data: Factura[];
  first_page_url: string;
  from: number;
  last_page: number;
  last_page_url: string;
  links: {
    url: string | null;
    label: string;
    active: boolean;
  }[];
  next_page_url: string | null;
  path: string;
  per_page: number;
  prev_page_url: string | null;
  to: number;
  total: number;
}

const FacturasPaginadas: React.FC = () => {
  const [facturas, setFacturas] = useState<Factura[]>([]);
  const [currentPage, setCurrentPage] = useState(1);
  const [loadingMore, setLoadingMore] = useState(false);
  const [totalPages, setTotalPages] = useState(1);

  const url = `http://localhost:8090/api/v1/facturas?page=${currentPage}`;

  const options = {
    method: "GET",
    headers: {
      Accept: "application/json",
      "Content-Type": "application/json",
      Authorization: `Bearer ${getTok()}`,
      "User-Agent": `Customer-Area/${process.env.EXPO_PUBLIC_VERSION} ${Device.osName}/${Device.osVersion} ${Device.brand} ${Device.modelName}`,
    },
  };

  const { data, loading, error, setData } = useFetch<FacturasResponse>(url, options);

  useEffect(() => {
    if (data) {
      if (currentPage === 1) {
        setFacturas(data.data);
      } else {
        setFacturas(prevFacturas => [...prevFacturas, ...data.data]);
      }
      setTotalPages(data.last_page);
      setLoadingMore(false);
    }
  }, [data, currentPage]);

  useEffect(() => {
    if (currentPage > 1) {
      setLoadingMore(true);
    }
  }, [currentPage])

  const renderItem = ({ item }: { item: Factura }) => (
    <View style={{ padding: 10 }}>
      <Text>Factura ID: {item.id}</Text>
    </View>
  );

  const loadMore = () => {
    if (currentPage < totalPages && !loadingMore) {
      setCurrentPage(prevPage => prevPage + 1);
    }
  };

  const renderFooter = () => {
    if (!loadingMore) return null;
    return (
      <View style={{ paddingVertical: 20 }}>
        <ActivityIndicator size="large" />
      </View>
    );
  };

  if (loading && currentPage === 1) {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <ActivityIndicator size="large" />
      </View>
    );
  }

  if (error) {
    return (
        <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
            <Text>Error: {error.message}</Text>
        </View>
    );
  }

  return (
    <FlatList
      data={facturas}
      renderItem={renderItem}
      keyExtractor={item => item.id.toString()}
      onEndReached={loadMore}
      onEndReachedThreshold={0.5}
      ListFooterComponent={renderFooter}
    />
  );
};

export default FacturasPaginadas;
```
