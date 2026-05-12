# 🏗️ MASTER CLASS: FEATURE-SLICED DESIGN (FSD)

> **Metodología de Arquitectura Frontend para proyectos escalables**

---

## 🔰 EL FUNDAMENTO

FSD es una metodología de **estructura de proyecto** que establece fronteras estrictas entre capas. Su objetivo es eliminar el caos organizativo que aparece cuando un proyecto crece, evitando el acoplamiento circular y la dispersión de lógica de negocio.

### Los 6 Principios Fundamentales

1. **Coupling** — Acoplamiento entre módulos solo hacia adelante (capas inferiores no pueden importar superiores)
2. **Public API** — Cada módulo expone una API pública via `index.ts`
3. **Segmentation** — Separación por responsabilidades (no por tipo de archivo)
4. **Unity** — Convenciones uniformes en todo el proyecto
5. **Self-documentation** — La estructura cuenta la historia del sistema
6. **Testability** — Cada módulo es testeable de forma aislada

---

## 🏛️ LAS 6 CAPAS (LAYERS)

```
┌─────────────────────────────────────────────────────────┐
│  APP (Aplicación)                                       │
│  Configuración global, providers, routing                │
├─────────────────────────────────────────────────────────┤
│  PAGES (Páginas)                                        │
│  Composición de páginas completas                       │
├─────────────────────────────────────────────────────────┤
│  WIDGETS (Widgets)                                      │
│  Bloques de UI autocontenidos (Header, Sidebar)        │
├─────────────────────────────────────────────────────────┤
│  FEATURES (Features)                                    │
│  Funcionalidades que aportan valor al usuario           │
├─────────────────────────────────────────────────────────┤
│  ENTITIES (Entidades)                                   │
│  Objetos de negocio (User, Product, Order)              │
├─────────────────────────────────────────────────────────┤
│  SHARED (Compartido)                                    │
│  Código agnóstico al negocio                            │
└─────────────────────────────────────────────────────────┘
```

### Flujo de Importación (单向)

```
shared → entities → features → widgets → pages → app
   ↑                                              │
   └──────────────────────────────────────────────┘
```

**Regla Oro:** Las capas inferiores NUNCA pueden importar de capas superiores.

---

## 📦 CAPA: SHARED (Lo Compartido)

### Definición

Código **completamente agnóstico** al negocio. Cualquier módulo de negocio debería poder usar estos componentes sin saber dónde vive la lógica.

### Estructura

```
shared/
├── api/                  # Axios instance, endpoints base
├── config/               # Environment variables, constants
├── lib/                  # Libraries wrapper (date-fns, lodash)
├── ui/                   # UI-Kit atómico
│   ├── Button/
│   ├── Input/
│   ├── Modal/
│   └── ...
├── hooks/                # Hooks genéricos (useDebounce, useLocalStorage)
├── utils/               # Funciones helper puras
└── types/                # Tipos genéricos (Response, Error)
```

### Ejemplo: Button del UI-Kit

```tsx
// shared/ui/Button/Button.tsx
import { ButtonHTMLAttributes, ReactNode } from 'react';

export interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  children: ReactNode;
}

export const Button = ({ variant = 'primary', size = 'md', ...props }: ButtonProps) => {
  // Implementación sin conocimiento de negocio
  return <button data-variant={variant} data-size={size} {...props} />;
};
```

### Regla de Identificación

Si en `shared/ui/Button` tienes que preguntar "¿Para qué tipo de usuario es este botón?", entonces **no pertenece a shared**.

---

## 🧱 CAPA: ENTITIES (Entidades)

### Definición

Representan los **objetos de negocio** del dominio. Son "tontos" — solo saben de datos, no de acciones.

### Diferencia Clave

| Entity | Feature |
|--------|---------|
| "¿Qué es un usuario?" | "¿Qué hace el usuario?" |
| Modelo de datos | Acción del usuario |
| UI para mostrar datos | Interacción con datos |
| `User`, `Product`, `Order` | `AddToCart`, `ChangePassword` |

### Estructura

```
entities/
├── user/
│   ├── model/           # Tipos, interfaces, selectors
│   │   ├── user.ts
│   │   └── user.selectors.ts
│   ├── ui/              # Componentes de presentación
│   │   ├── UserCard.tsx
│   │   ├── UserAvatar.tsx
│   │   └── UserList.tsx
│   └── index.ts         # Public API
│
├── product/
│   ├── model/
│   │   ├── product.ts
│   │   └── product.selectors.ts
│   ├── ui/
│   │   ├── ProductCard.tsx
│   │   ├── ProductImage.tsx
│   │   └── ProductPrice.tsx
│   └── index.ts
│
└── order/
    ├── model/
    ├── ui/
    └── index.ts
```

### Ejemplo: Public API de Entity

```ts
// entities/user/index.ts
export { UserCard } from './ui/UserCard/UserCard';
export { UserAvatar } from './ui/UserAvatar/UserAvatar';
export { UserList } from './ui/UserList/UserList';
export type { User, UserSchema } from './model/user';
export { selectUsers, selectUserById } from './model/user.selectors';
```

### Ejemplo: Componente Entity

```tsx
// entities/user/ui/UserCard/UserCard.tsx
import { User } from '../../model/user';
import { Avatar } from '@shared/ui/Avatar/Avatar';
import { Typography } from '@shared/ui/Typography/Typography';

interface UserCardProps {
  user: User;
  className?: string;
}

export const UserCard = ({ user, className }: UserCardProps) => {
  return (
    <div className={className}>
      <Avatar src={user.avatar} alt={user.name} />
      <Typography variant="h3">{user.name}</Typography>
      <Typography variant="body2">{user.email}</Typography>
    </div>
  );
};
```

---

## ⚡ CAPA: FEATURES (Features)

### Definición

**Funcionalidades** que representan acciones del usuario. Aportan valor de negocio. Son "smart" — saben interacturar con datos y estado.

### Estructura

```
features/
├── auth/
│   ├── model/           # Estado, acciones, thunks
│   │   └── authSlice.ts
│   ├── ui/              # Componentes específicos de auth
│   │   ├── LoginForm/
│   │   └── AuthModal/
│   └── lib/             # Funciones de autenticación
│
├── cart/
│   ├── model/
│   │   └── cartSlice.ts
│   ├── ui/
│   │   ├── AddToCartButton/
│   │   └── CartItem/
│   └── lib/
│
├── wishlist/
├── profile/
├── comments/
└── filters/
```

### Ejemplo: Feature "Add to Cart"

```tsx
// features/cart/ui/AddToCartButton/AddToCartButton.tsx
import { useDispatch, useSelector } from 'react-redux';
import { addItem } from '../../model/cartSlice';
import { selectProductById } from '@entities/product';
import { Button } from '@shared/ui/Button/Button';
import { Product } from '@entities/product/model/product';

interface AddToCartButtonProps {
  product: Product;
}

export const AddToCartButton = ({ product }: AddToCartButtonProps) => {
  const dispatch = useDispatch();

  const handleAdd = () => {
    dispatch(addItem({ product, quantity: 1 }));
  };

  return (
    <Button onClick={handleAdd}>
      Add to Cart
    </Button>
  );
};
```

### Justificación: ¿Por qué es Feature?

1. **Tiene lógica de negocio:** dispatch de actions, estado
2. **Representa acción del usuario:** "Agregar al carrito"
3. **Interactúa con Entities:** usa `Product` del modelo
4. **No es "tonto":** sabe qué hacer con los datos

---

## 📐 CAPA: WIDGETS (Widgets)

### Definición

Bloques de UI **autocontenidos** que combinan entidades y features. Son las "secciones" de una página.

### Estructura

```
widgets/
├── header/
│   ├── ui/
│   │   └── Header.tsx
│   ├── index.ts
│   └── Header.tsx (re-export)
│
├── sidebar/
│   ├── ui/
│   │   └── Sidebar.tsx
│   └── index.ts
│
├── product-grid/
│   ├── ui/
│   │   └── ProductGrid.tsx
│   └── index.ts
│
└── checkout-form/
    ├── ui/
    └── index.ts
```

### Ejemplo: ProductGrid (Widget)

```tsx
// widgets/product-grid/ui/ProductGrid/ProductGrid.tsx
import { Product } from '@entities/product';
import { ProductCard } from '@entities/product/ui/ProductCard';
import { AddToCartButton } from '@features/cart/ui/AddToCartButton';
import { ProductSort } from '@features/sorting/ui/ProductSort';
import styles from './ProductGrid.module.css';

interface ProductGridProps {
  products: Product[];
}

export const ProductGrid = ({ products }: ProductGridProps) => {
  return (
    <div className={styles.container}>
      <ProductSort />
      <div className={styles.grid}>
        {products.map((product) => (
          <ProductCard key={product.id} product={product}>
            <AddToCartButton product={product} />
          </ProductCard>
        ))}
      </div>
    </div>
  );
};
```

---

## 📄 CAPA: PAGES (Páginas)

### Definición

Las **vistas completas** de la aplicación. Son el punto de entrada para el router.

### Estructura

```
pages/
├── app/
│   ├── index.ts
│   └── routes.ts
│
├── home/
│   ├── index.ts
│   └── HomePage.tsx
│
├── profile/
│   ├── index.ts
│   └── ProfilePage.tsx
│
├── product/
│   ├── index.ts
│   └── ProductPage.tsx
│
└── checkout/
    ├── index.ts
    └── CheckoutPage.tsx
```

### Ejemplo: ProductPage

```tsx
// pages/product/ProductPage.tsx
import { useParams } from 'react-router-dom';
import { ProductDetails } from '@widgets/product-details';
import { ProductReviews } from '@widgets/product-reviews';
import { RelatedProducts } from '@widgets/related-products';
import { useProduct } from '@entities/product/model/useProduct';

export const ProductPage = () => {
  const { id } = useParams();
  const { product, isLoading } = useProduct(id);

  if (isLoading) return <Loader />;

  return (
    <div>
      <ProductDetails product={product} />
      <ProductReviews productId={product.id} />
      <RelatedProducts categoryId={product.categoryId} />
    </div>
  );
};
```

---

## ⚙️ CAPA: APP (Aplicación)

### Definición

El **punto de entrada** de la aplicación. Configuración global, providers, routing.

### Estructura

```
app/
├── providers/
│   ├── with-router.tsx
│   ├── with-store.tsx
│   └── with-theme.tsx
│
├── routes/
│   └── index.tsx
│
├── styles/
│   ├── global.css
│   └── theme.css
│
├── index.tsx
└── App.tsx
```

---

## 🎯 PUBLIC API (index.ts)

### La Regla de Oro

**NUNCA** importes directamente desde un archivo interno:

```tsx
// ❌ PROHIBIDO - Import profunda
import { UserCard } from '@/entities/user/ui/UserCard/UserCard';

// ✅ PERMITIDO - API pública
import { UserCard } from '@/entities/user';
```

### Ejemplo de Public API

```ts
// entities/user/index.ts
// Exports públicos del módulo

// UI Components
export { UserCard } from './ui/UserCard/UserCard';
export { UserAvatar } from './ui/UserAvatar/UserAvatar';
export { UserList } from './ui/UserList/UserList';

// Model
export type { User, UserSchema } from './model/user';

// Selectors
export { selectUsers, selectUserById, selectUserLoading } from './model/user.selectors';

// API
export { fetchUsers, createUser, updateUser } from './model/user.api';
```

---

## 🔗 ANTI-PATRONES Y ERRORES COMUNES

### 1. Import Circular

```
❌ PROHIBIDO:
features/cart → entities/user (si user necesita cart)
```

**Solución:** Revisa la dirección de las capas.

### 2. Feature en Entity

```tsx
// ❌ entity/user/ui/UserCard.tsx contiene lógica de "login"
// Este componente sabe demasiado

// ✅ UserCard solo muestra datos
// La lógica de login está en features/auth
```

### 3. Shared que sabe de Negocio

```tsx
// ❌ shared/ui/Notification/Notification.tsx
// conditional rendering para "order-confirmed"

export const Notification = ({ type }) => {
  if (type === 'order-confirmed') return <OrderConfirmToast />;
  // ❌ Ahora shared sabe de "orders"
}
```

### 4. Pages con lógica de negocio

```tsx
// ❌ pages/ProfilePage.tsx
// contiene código de validación de password

// ✅ Esa lógica va en features/auth
// ProfilePage solo compone widgets
```

### 5. Widgets que mutan estado directamente

```tsx
// ❌ widgets/CartWidget/index.tsx
// hace directamente dispatch(cartActions.add)

// ✅ El widget solo usa features
// La lógica está en la feature
```

---

## 🔄 MIGRACIÓN DESDE OTRA ESTRUCTURA

### Paso 1: Identifica las entidades

```
旧 (Old)                →  新 (New)
─────────────────────────────────────
components/UserCard     →  entities/user/ui/UserCard
components/ProductCard  →  entities/product/ui/ProductCard
components/Header       →  widgets/header
```

### Paso 2: Extrae features

```
旧                        →  新
─────────────────────────────────────────
hooks/useAuth            →  features/auth
hooks/useCart            →  features/cart
context/AuthContext      →  features/auth/model
```

### Paso 3: Crea la Public API

```ts
// entities/user/index.ts
export * from './ui/UserCard';
export * from './ui/UserAvatar';
export type * from './model/user';
```

---

## 🧪 TESTING CON FSD

### Unit Testing

```ts
// entities/user/ui/UserCard.test.tsx
// Test del componente sin dependencias de negocio
```

### Integration Testing

```ts
// features/cart/model/cartSlice.test.ts
// Test del estado y acciones
```

### E2E Testing

```ts
// pages/ProfilePage.test.ts
// Test completo del flujo
```

---

## 📊 COMPARATIVA: FSD vs Atomic Design

| Aspecto | Atomic Design | FSD |
|---------|---------------|-----|
| **Enfoque** | Composición UI | Arquitectura de proyecto |
| **Capas** | 5 (Átomo → Página) | 6 (Shared → App) |
| **Unidad** | Componente | Módulo de dominio |
| **Adecuado para** | Design Systems | Aplicaciones completas |
| **Flexibilidad** | Más flexible | Más estructurado |

### Se Complementan

```
FSD (Arquitectura) + Atomic Design (Componentes)

src/
├── shared/ui/           ← Atomic: Átomos y Moléculas
├── entities/product/ui ← Atomic: Organismos
├── widgets/             ← Atomic: Organismos complejos
└── pages/               ← Atomic: Plantillas + Páginas
```

---

## 📚 RECURSOS

- **Web oficial:** feature-sliced.design
- **Documentación:** github.com/feature-sliced/documentation
- **Linter:** eslint-plugin-feature-sliced
- **Herramienta:** [Template oficial](https://github.com/feature-sliced/example)

---

## 🗂️ ESTRUCTURA COMPLETA FSD

```
src/
├── app/
│   ├── providers/
│   ├── routes/
│   └── styles/
│
├── pages/
│   ├── home/
│   ├── profile/
│   ├── product/
│   └── ...
│
├── widgets/
│   ├── header/
│   ├── sidebar/
│   ├── product-grid/
│   └── ...
│
├── features/
│   ├── auth/
│   │   ├── model/
│   │   ├── ui/
│   │   └── lib/
│   ├── cart/
│   ├── wishlist/
│   └── ...
│
├── entities/
│   ├── user/
│   │   ├── model/
│   │   └── ui/
│   ├── product/
│   │   ├── model/
│   │   └── ui/
│   └── ...
│
└── shared/
    ├── api/
    ├── config/
    ├── lib/
    ├── ui/
    ├── hooks/
    ├── utils/
    └── types/
```

---

> *"La arquitectura no es un lujo, es una necesidad. FSD hace que el caos sea структура."*
> — Feature-Sliced Design