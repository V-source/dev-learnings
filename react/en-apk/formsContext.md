```jsx


import { Factura } from "@/interfaces";
import { createContext, useState, useContext, ReactNode } from "react";
import { View, Text } from "react-native";


interface FormsProviderProps {
  invoiceId: string | number | null;
  invoiceAmount: string | number | null;
  showForm: boolean;
  isForPasarela: string | null;
  setShowForm: (show: boolean) => void
}

const FormsContext = createContext<FormsProviderProps>({
  invoiceId: null,
  invoiceAmount: null,
  showForm: false,
  isForPasarela: '',
  setShowForm: () => {},
})

type ProviderProps = {
  children: ReactNode
}
export function FormsProvider({ children }: ProviderProps) {
  // Estados con tipos definidos e inicializados correctamente
  const [invoiceId, setInvoiceId] = useState<string | number | null>(null);
  const [invoiceAmount, setInvoiceAmount] = useState<string | number | null>(null);
  const [isForPasarela, setIsForPasarela] = useState<string | null>(null);
  const [showForm, setShowForm] = useState<boolean>(false);

  const value: FormsProviderProps = {
    invoiceId,
    invoiceAmount,
    showForm,
    setShowForm, // Ahora coincide con la interfaz
    isForPasarela,
  };

  return (
    <FormsContext.Provider value={value}>
      {children}
    </FormsContext.Provider>
  );
}
```
