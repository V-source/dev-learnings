# ⚛️ MASTER CLASS: ATOMIC DESIGN

> **Metodología de Brad Frost para crear Sistemas de Diseño Escalables**

---

## 🔰 EL FUNDAMENTO

Atomic Design no es un producto, es un **proceso mental**. Es la forma de pensar tu sistema de UI como una jerarquía de materia, donde cada nivel tiene una responsabilidad específica y un nivel de reutilización diferente.

### Los 5 Niveles de la Materia

```
┌─────────────────────────────────────────────────────────┐
│  PÁGINAS (Pages)                                        │
│  Instancias finales con contenido real                  │
├─────────────────────────────────────────────────────────┤
│  PLANTILLAS (Templates)                                 │
│  Esqueletos de layout, estructura de wireframe          │
├─────────────────────────────────────────────────────────┤
│  ORGANISMOS (Organisms)                                 │
│  Secciones complejas de la interfaz                     │
├─────────────────────────────────────────────────────────┤
│  MOLÉCULAS (Molecules)                                 │
│  Grupos de átomos que funcionan juntos                  │
├─────────────────────────────────────────────────────────┤
│  ÁTOMOS (Atoms)                                         │
│  Lo indivisible, bloques básicos de construcción        │
└─────────────────────────────────────────────────────────┘
```

---

## 🧱 NIVEL 1: ÁTOMOS

Los átomos son los **bloques más pequeños** de la materia. No se pueden dividir más sin perder su identidad o funcionalidad.

### Definición

- Son componentes **sin contexto de negocio**
- No saben de dónde vienen los datos
- Son **puros** en su responsabilidad
- Reciben todo via props

### Ejemplos de Átomos

```
├── atoms/
│   ├── Button/
│   │   ├── Button.tsx
│   │   └── Button.stories.tsx
│   ├── Input/
│   ├── Label/
│   ├── Typography/
│   │   ├── Heading.tsx
│   │   ├── Body.tsx
│   │   └── Caption.tsx
│   ├── Icon/
│   ├── Badge/
│   ├── Spinner/
│   ├── Avatar/
│   └── ColorPalette/
```

### Ejemplo de Código: Button Átomo

```tsx
// atoms/Button/Button.tsx
import { ButtonHTMLAttributes, ReactNode } from 'react';

export interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'ghost' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
  leftIcon?: ReactNode;
  rightIcon?: ReactNode;
  children: ReactNode;
}

export const Button = ({
  variant = 'primary',
  size = 'md',
  isLoading = false,
  leftIcon,
  rightIcon,
  children,
  disabled,
  className = '',
  ...props
}: ButtonProps) => {
  const baseStyles = 'inline-flex items-center justify-center font-medium rounded-lg transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2';

  const variantStyles = {
    primary: 'bg-blue-600 text-white hover:bg-blue-700 focus:ring-blue-500',
    secondary: 'bg-gray-100 text-gray-900 hover:bg-gray-200 focus:ring-gray-500',
    ghost: 'bg-transparent text-gray-700 hover:bg-gray-100 focus:ring-gray-500',
    danger: 'bg-red-600 text-white hover:bg-red-700 focus:ring-red-500',
  };

  const sizeStyles = {
    sm: 'px-3 py-1.5 text-sm',
    md: 'px-4 py-2 text-base',
    lg: 'px-6 py-3 text-lg',
  };

  return (
    <button
      className={`${baseStyles} ${variantStyles[variant]} ${sizeStyles[size]} ${className}`}
      disabled={disabled || isLoading}
      {...props}
    >
      {isLoading && <Spinner size="sm" className="mr-2" />}
      {leftIcon && <span className="mr-2">{leftIcon}</span>}
      {children}
      {rightIcon && <span className="ml-2">{rightIcon}</span>}
    </button>
  );
};
```

### Reglas del Átomo

1. **No saber de negocio:** El `Button` no se llama `LoginButton`
2. **Recibe todo por props:** Sin hardcoded de contenido
3. **Un nivel de responsabilidad:** Un botón sabe manejar estados, no hacer fetch de datos
4. **Ser reutilizable:** Debe funcionar en cualquier contexto

---

## 🔬 NIVEL 2: MOLÉCULAS

Las moléculas son grupos de átomos que **funcionan juntos** como una unidad. Son el primer nivel de **composición**.

### Definición

- Combinan 2 o más átomos
- Tienen una **función específica** pero simple
- Son **independientes del contexto de página**
- Mantienen el principio de **responsabilidad única**

### Ejemplos de Moléculas

```
├── molecules/
│   ├── SearchInput/
│   │   └── SearchInput.tsx (Input + Label + Button)
│   ├── FormField/
│   │   └── FormField.tsx (Label + Input + ErrorMessage)
│   ├── UserChip/
│   │   └── UserChip.tsx (Avatar + Name + RemoveButton)
│   ├── PriceTag/
│   │   └── PriceTag.tsx (Currency + Amount + Symbol)
│   ├── SocialButton/
│   │   └── SocialButton.tsx (Icon + BrandName + Button)
│   ├── BreadcrumbItem/
│   │   └── BreadcrumbItem.tsx (Link + Icon + Text)
│   └── Notification/
│       └── Notification.tsx (Icon + Message + CloseButton)
```

### Ejemplo de Código: SearchInput (Molécula)

```tsx
// molecules/SearchInput/SearchInput.tsx
import { ChangeEvent, FormEvent, useState } from 'react';
import { Input } from '../../atoms/Input/Input';
import { Button } from '../../atoms/Button/Button';
import { SearchIcon } from '../../atoms/Icon/icons';

export interface SearchInputProps {
  placeholder?: string;
  onSearch: (value: string) => void;
  initialValue?: string;
  className?: string;
}

export const SearchInput = ({
  placeholder = 'Search...',
  onSearch,
  initialValue = '',
  className = '',
}: SearchInputProps) => {
  const [value, setValue] = useState(initialValue);

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    setValue(e.target.value);
  };

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    onSearch(value);
  };

  return (
    <form className={`flex gap-2 ${className}`} onSubmit={handleSubmit}>
      <Input
        type="search"
        value={value}
        onChange={handleChange}
        placeholder={placeholder}
        aria-label={placeholder}
      />
      <Button type="submit" variant="primary" aria-label="Search">
        <SearchIcon />
      </Button>
    </form>
  );
};
```

### Justificación: ¿Por qué es molécula?

1. **Combina átomos:** Input + Button
2. **Función simple:** Solo busca, no filtra ni pagin
3. **Sin lógica de datos:** No sabe de dónde vienen los resultados
4. **Reutilizable:** Puede estar en el header, sidebar, o cualquier página

---

## 🧬 NIVEL 3: ORGANISMOS

Los organismos son secciones **complejas** de la interfaz. Son el primer nivel donde aparecen datos externos y lógica de negocio.

### Definición

- Compuestos de múltiples moléculas y/o átomos
- Tienen **lógica de datos** (fetch, store, state)
- Representan una **sección visual coherente**
- Son específicos de una **sección de la app**

### Ejemplos de Organismos

```
├── organisms/
│   ├── Header/
│   │   └── Header.tsx (Logo + Nav + SearchInput + UserMenu)
│   ├── ProductCard/
│   │   └── ProductCard.tsx (Image + Title + Price + AddToCart)
│   ├── CommentSection/
│   │   └── CommentSection.tsx (CommentList + CommentForm + Pagination)
│   ├── DataTable/
│   │   └── DataTable.tsx (Table + Headers + Pagination + Filters)
│   ├── AuthForm/
│   │   └── AuthForm.tsx (FormField + SocialButtons + SubmitButton)
│   ├── ArticleCard/
│   │   └── ArticleCard.tsx (Image + Title + Excerpt + AuthorMeta)
│   └── Sidebar/
│       └── Sidebar.tsx (NavItems + UserInfo + CollapseBehavior)
```

### Ejemplo de Código: ProductCard (Organismo)

```tsx
// organisms/ProductCard/ProductCard.tsx
import { useState } from 'react';
import { Product } from '../../types/product';
import { Image } from '../../atoms/Image/Image';
import { Typography } from '../../atoms/Typography/Typography';
import { Button } from '../../atoms/Button/Button';
import { Badge } from '../../atoms/Badge/Badge';
import { PriceTag } from '../../molecules/PriceTag/PriceTag';

export interface ProductCardProps {
  product: Product;
  onAddToCart?: (product: Product) => void;
  onAddToWishlist?: (product: Product) => void;
}

export const ProductCard = ({
  product,
  onAddToCart,
  onAddToWishlist,
}: ProductCardProps) => {
  const [isHovered, setIsHovered] = useState(false);

  const handleAddToCart = () => {
    onAddToCart?.(product);
  };

  return (
    <div
      className="group relative bg-white rounded-xl shadow-sm hover:shadow-md transition-shadow"
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      <div className="aspect-square overflow-hidden rounded-t-xl">
        <Image
          src={product.imageUrl}
          alt={product.name}
          className="object-cover w-full h-full group-hover:scale-105 transition-transform duration-300"
        />
        {product.isNew && (
          <Badge variant="primary" className="absolute top-3 left-3">
            New
          </Badge>
        )}
        {product.discount && (
          <Badge variant="danger" className="absolute top-3 right-3">
            -{product.discount}%
          </Badge>
        )}
      </div>

      <div className="p-4 space-y-3">
        <Typography variant="h3" className="line-clamp-2">
          {product.name}
        </Typography>

        <PriceTag
          amount={product.price}
          originalAmount={product.originalPrice}
          currency={product.currency}
        />

        <div className="flex gap-2">
          <Button
            variant="primary"
            className="flex-1"
            onClick={handleAddToCart}
          >
            Add to Cart
          </Button>
          <Button
            variant="ghost"
            onClick={() => onAddToWishlist?.(product)}
            aria-label="Add to wishlist"
          >
            ♥
          </Button>
        </div>
      </div>
    </div>
  );
};
```

### Justificación: ¿Por qué es organismo?

1. **Lógica de datos:** Receive `Product` como prop, maneja eventos
2. **Múltiples moléculas:** PriceTag, múltiples átomos
3. **Contexto de negocio:** "Producto" es un concepto de dominio
4. **Responsabilidad amplia:** Imagen, precio, acciones, estados

---

## 📄 NIVEL 4: PLANTILLAS (TEMPLATES)

Las plantillas son **esqueletos de layout**. Definen la estructura visual sin contenido específico.

### Definición

- Son el **molde** de una página
- No tienen datos reales, tienen **placeholders**
- Definen zonas: header, sidebar, content, footer
- Son el puente entre organismos y páginas

### Ejemplos de Plantillas

```
├── templates/
│   ├── DashboardTemplate/
│   │   └── DashboardTemplate.tsx
│   ├── BlogPostTemplate/
│   ├── EcommerceTemplate/
│   ├── AuthLayoutTemplate/
│   └── SettingsTemplate/
```

### Ejemplo de Código: DashboardTemplate

```tsx
// templates/DashboardTemplate/DashboardTemplate.tsx
import { ReactNode } from 'react';
import { Header } from '../../organisms/Header/Header';
import { Sidebar } from '../../organisms/Sidebar/Sidebar';

export interface DashboardTemplateProps {
  children: ReactNode;
  sidebar?: ReactNode;
  header?: ReactNode;
}

export const DashboardTemplate = ({
  children,
  sidebar = <Sidebar />,
  header = <Header />,
}: DashboardTemplateProps) => {
  return (
    <div className="min-h-screen flex flex-col">
      {header}
      <div className="flex flex-1">
        <aside className="w-64 hidden lg:block">
          {sidebar}
        </aside>
        <main className="flex-1 p-6">
          {children}
        </main>
      </div>
    </div>
  );
};
```

---

## 📑 NIVEL 5: PÁGINAS

Las páginas son las **instancias finales**. Son las plantillas con contenido real.

### Definición

- Son las **únicas que importan en el routing**
- Rellenan las plantillas con datos reales
- Son las que el usuario **realmente ve**
- Aquí termina la cadena de composición

### Ejemplos de Páginas

```
├── pages/
│   ├── DashboardPage/
│   │   └── DashboardPage.tsx
│   ├── ProductDetailPage/
│   ├── CheckoutPage/
│   ├── LoginPage/
│   └── ProfilePage/
```

### Ejemplo de Código: DashboardPage

```tsx
// pages/DashboardPage/DashboardPage.tsx
import { useEffect, useState } from 'react';
import { DashboardTemplate } from '../../templates/DashboardTemplate/DashboardTemplate';
import { StatsGrid } from '../../organisms/StatsGrid/StatsGrid';
import { RecentOrders } from '../../organisms/RecentOrders/RecentOrders';
import { useOrders } from '../../hooks/useOrders';
import { useStats } from '../../hooks/useStats';

export const DashboardPage = () => {
  const { orders, isLoading: ordersLoading } = useOrders();
  const { stats, isLoading: statsLoading } = useStats();

  return (
    <DashboardTemplate>
      <div className="space-y-6">
        <h1 className="text-3xl font-bold">Dashboard</h1>
        <StatsGrid stats={stats} isLoading={statsLoading} />
        <RecentOrders orders={orders} isLoading={ordersLoading} />
      </div>
    </DashboardTemplate>
  );
};
```

---

## 🗂️ ESTRUCTURA COMPLETA DEL PROYECTO

```
src/
├── components/
│   ├── atoms/
│   │   ├── Button/
│   │   ├── Input/
│   │   ├── Label/
│   │   ├── Typography/
│   │   ├── Icon/
│   │   ├── Image/
│   │   ├── Badge/
│   │   ├── Spinner/
│   │   ├── Avatar/
│   │   ├── Card/
│   │   ├── Divider/
│   │   └── ColorPalette/
│   │
│   ├── molecules/
│   │   ├── SearchInput/
│   │   ├── FormField/
│   │   ├── PriceTag/
│   │   ├── UserChip/
│   │   ├── Breadcrumb/
│   │   ├── Pagination/
│   │   ├── Modal/
│   │   ├── Dropdown/
│   │   └── Tooltip/
│   │
│   ├── organisms/
│   │   ├── Header/
│   │   ├── Footer/
│   │   ├── Sidebar/
│   │   ├── ProductCard/
│   │   ├── CommentSection/
│   │   ├── DataTable/
│   │   ├── AuthForm/
│   │   ├── StatsGrid/
│   │   └── RecentOrders/
│   │
│   ├── templates/
│   │   ├── DashboardTemplate/
│   │   ├── BlogPostTemplate/
│   │   ├── EcommerceTemplate/
│   │   └── AuthLayoutTemplate/
│   │
│   └── pages/
│       ├── DashboardPage/
│       ├── ProductDetailPage/
│       ├── CheckoutPage/
│       ├── LoginPage/
│       └── ProfilePage/
│
├── hooks/
├── context/
├── services/
├── types/
└── utils/
```

---

## 🎯 PRINCIPIOS CLAVE

### 1. Agnosticismo de Datos

```
❌ NO HACER:
const Button = () => {
  const { user } = useAuth();
  return <button>Logout {user.name}</button>;
}

✓ HACER:
const Button = ({ children, onClick }) => {
  return <button onClick={onClick}>{children}</button>;
}
```

### 2. Composición sobre Herencia

```
❌ NO HACER:
class ProductCard extends Card {}

✓ HACER:
const ProductCard = ({ product }) => (
  <Card>
    <Image src={product.image} />
    <Title>{product.name}</Title>
  </Card>
);
```

### 3. Responsabilidad Única por Nivel

| Nivel | Responsabilidad |
|-------|-----------------|
| Átomo | Renderizar, estados simples |
| Molécula | Comportamiento simple, interacción básica |
| Organismo | Lógica de negocio, múltiples responsabilidades |
| Plantilla | Layout, composición de organismos |
| Página | Routing, datos reales, coordinación |

### 4. naming Convensión

```
✓ atoms/Button/Button.tsx
✓ molecules/SearchInput/SearchInput.tsx
✓ organisms/ProductCard/ProductCard.tsx
✓ templates/DashboardTemplate/DashboardTemplate.tsx
✓ pages/DashboardPage/DashboardPage.tsx
```

---

## 🔄 FLUJO DE TRABAJO

```
1. IDENTIFICA los componentes que necesitas
       │
       ▼
2. CLASIFICA en el nivel apropiado
       │
       ├── Átomo: si es lo más pequeño posible
       ├── Molécula: si combina átomos
       ├── Organismo: si tiene lógica de datos
       ├── Plantilla: si es estructura de layout
       └── Página: si es instancia final
       │
       ▼
3. IMPLEMENTA desde átomos hacia arriba
       │
       ▼
4. COMPONE en la página final
```

---

## ⚠️ ANTI-PATRONES

### 1. Saltar niveles
No hacer un organismo si primero no tienes los átomos y moléculas que lo componen.

### 2. Átomos con lógica de negocio
Si tu botón sabe de usuarios, no es un átomo.

### 3. Moléculas que parecen organismos
Si tu SearchInput hace fetch de resultados, está en el nivel errado.

### 4. Páginas que contiennent todo
Si tu página tiene JSX inline de más de 20 líneas, probablemente necesitas organismos.

---

## 📚 RECURSOS

- **Libro:** "Atomic Design" por Brad Frost
- **Web:** atomicdesign.bradfrost.com
- **Herramienta:** Pattern Lab
- **Documentación:** Storybook

---

> *"We don't create pages. We create systems."*
> — Brad Frost