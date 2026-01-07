# Modern PWA Architecture - Vendor Agnostic
## Universal Frontend Application Architecture

This document provides a comprehensive overview of a modern, vendor-agnostic architecture for building scalable Progressive Web Applications (PWA) using Next.js. While examples reference e-commerce use cases, this architecture is applicable to any type of application including SaaS platforms, content management systems, dashboards, and more.

---

## Table of Contents

1. [Technology Stack](#technology-stack)
2. [Application Architecture Overview](#application-architecture-overview)
3. [Layered Architecture](#layered-architecture)
4. [Component Architecture](#component-architecture)
5. [Data Fetching & State Management](#data-fetching--state-management)
6. [Routing & Templates](#routing--templates)
7. [Styling Architecture](#styling-architecture)
8. [Server-Side Rendering (SSR)](#server-side-rendering-ssr)
9. [API Integration Architecture](#api-integration-architecture)
10. [Configuration Management](#configuration-management)
11. [Testing Architecture](#testing-architecture)
12. [Build & Deployment](#build--deployment)
13. [Monitoring & Observability](#monitoring--observability)

---

## Technology Stack

### Core Framework
- **React**: 18.2.0+
- **TypeScript**: 5.0.0+
- **Node.js**: >=18.0.0
- **Next.js**: 14.0.0+ (App Router)
  - Built-in SSR/SSG support
  - API routes
  - Image optimization
  - Font optimization
- **Express**: 4.21.2+ (Optional custom server)

### Build & Bundling
- **Turbopack**: Next.js default bundler (or Webpack 5)
- **SWC**: Fast compilation (replaces Babel)
- **PostCSS**: CSS processing
- **Next.js Code Splitting**: Automatic route-based splitting

### Styling
- **Tailwind CSS**: 3.4.0+ (Utility-first CSS)
- **CSS Modules**: Component-scoped styles (optional)
- **Design Tokens**: Style Dictionary or CSS Variables
- **PostCSS**: CSS processing pipeline

### State Management & Data Fetching
- **TanStack React Query**: 5.0.0+ (data fetching, caching, synchronization)
- **Zustand**: 4.4.0+ (lightweight client state management)
- **React Hook Form**: 7.48.0+ (form state management)
- **Zod**: 3.22.0+ (Schema validation)

### Routing
- **Next.js App Router**: File-based routing (replaces React Router)
- **Next.js Middleware**: Route interception and rewriting

### Internationalization
- **next-intl**: 3.0.0+ (Next.js i18n solution)
- **React Intl**: 6.3.0+ (Alternative)

### Testing
- **Jest**: 29.7.0+ (Unit testing)
- **React Testing Library**: 14.0.0+ (Component testing)
- **Playwright**: 1.40.0+ (E2E testing)
- **Storybook**: 8.0.0+ (Component development & documentation)
- **MSW (Mock Service Worker)**: 2.0.0+ (API mocking)

### Code Quality
- **ESLint**: 8.54.0+ (Linting)
- **Prettier**: 3.1.0+ (Code formatting)
- **Husky**: 8.0.0+ (Git hooks)
- **lint-staged**: 15.0.0+ (Pre-commit checks)
- **Commitlint**: 18.4.0+ (Conventional commits)
- **TypeScript**: Type checking

### Monitoring & Observability
- **OpenTelemetry**: 1.21.0+ (Distributed tracing & metrics)
- **Sentry**: 7.80.0+ (Error tracking) or **LogRocket** (Session replay)
- **Vercel Analytics**: (Web vitals) or **Google Analytics 4**
- **next-seo**: 6.0.0+ (SEO & meta tag management)

---

## Architecture Applicability

While this architecture document uses **e-commerce examples** (products, cart, checkout) for illustration, the architecture itself is **universal** and can be applied to:

- **E-Commerce Platforms**: Online stores, marketplaces, retail applications
- **SaaS Applications**: B2B software, productivity tools, business platforms
- **Content Management Systems**: Blogs, news sites, documentation platforms
- **Dashboards & Analytics**: Data visualization, reporting, admin panels
- **Social Platforms**: Community sites, forums, social networks
- **Portals**: Customer portals, partner portals, intranets
- **Any Web Application**: The patterns and structures are domain-agnostic

The core architectural principles (layered architecture, component organization, data fetching, routing, etc.) remain the same regardless of the application domain. Only the **domain-specific components and business logic** change.

---

## Application Architecture Overview

### High-Level Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    User Request                             │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Next.js Server (SSR/SSG)                        │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Next.js Middleware                      │    │
│  │  (Route interception, rewrites, redirects)           │    │
│  └─────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Security Headers (next-seo)            │    │
│  └─────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Next.js App Router                      │    │
│  │  (Server Components, Route Handlers)                │    │
│  └─────────────────────────────────────────────────────┘    │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              React Application (SSR/Hydration)               │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                    Providers                        │    │
│  │  - QueryClientProvider (React Query)               │    │
│  │  - ThemeProvider (Design tokens)                   │    │
│  │  - TranslationProvider (next-intl)                  │    │
│  │  - AnalyticsProvider                                │    │
│  └─────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                  Root Layout                        │    │
│  │  (Persistent template wrapper)                      │    │
│  └─────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                    Routes                            │    │
│  │  (App Router file-based routing)                    │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

---

## Layered Architecture

The application follows a strict layered architecture pattern:

```
┌─────────────────────────────────────────────────────────────┐
│                        Routes Layer                         │
│  - Next.js App Router (file-based)                         │
│  - Route groups and layouts                                │
│  - Route configuration                                     │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      Templates Layer                        │
│  - MainLayout (header, footer, content area)               │
│  - CheckoutLayout (simplified layout)                      │
│  - AccountLayout (account pages)                           │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                        Pages Layer                          │
│  - Page components (app/[locale]/brands/page.tsx)         │
│  - Page-specific data fetching (Server Components)         │
│  - Business logic                                           │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                     Components Layer                        │
│  - Organisms (Header, Footer, MegaMenu, etc.)               │
│  - Molecules (ProductCard, Breadcrumb, etc.)                │
│  - Atoms (Button, Input, Icon, etc.)                       │
└─────────────────────────────────────────────────────────────┘
```

### Layer Responsibilities

1. **Routes Layer** (`app/[locale]/`)
   - Next.js App Router file-based routing
   - Route groups for organization
   - Layout composition
   - Middleware for route interception

2. **Templates Layer** (`app/[locale]/_layouts/`)
   - Provides consistent layout structure
   - Wraps page content with header/footer
   - Manages template-specific components
   - Handles template persistence across route changes

3. **Pages Layer** (`app/[locale]/*/page.tsx`)
   - Page-specific Server Components
   - Page-level data fetching
   - Business logic for specific pages
   - Route parameter handling

4. **Components Layer** (`packages/components/`)
   - Reusable UI components
   - Organized by Atomic Design methodology
   - Client Components (when interactivity needed)
   - Server Components (default)

---

## Component Architecture

### Complete Component Package Structure

```
packages/components/
├── src/
│   ├── atoms/                  # Basic building blocks
│   │   ├── button/
│   │   │   ├── index.ts
│   │   │   ├── Button.tsx
│   │   │   ├── Button.spec.tsx
│   │   │   ├── Button.stories.tsx
│   │   │   ├── Button.test.tsx
│   │   │   ├── types.ts
│   │   │   └── styles.module.css (optional)
│   │   ├── input/
│   │   ├── icon/
│   │   ├── badge/
│   │   ├── price/
│   │   ├── label/
│   │   ├── spinner/
│   │   └── index.ts            # Barrel export for atoms
│   │
│   ├── molecules/              # Simple combinations
│   │   ├── product-card/
│   │   │   ├── index.ts
│   │   │   ├── ProductCard.tsx
│   │   │   ├── ProductCard.spec.tsx
│   │   │   ├── ProductCard.stories.tsx
│   │   │   ├── ProductCard.test.tsx
│   │   │   ├── types.ts
│   │   │   └── components/     # Sub-components if needed
│   │   │       └── ProductImage.tsx
│   │   ├── breadcrumb/
│   │   ├── search-suggestions-panel/
│   │   ├── form-field/         # Input + Label combination
│   │   ├── price-display/      # Price + Currency
│   │   └── index.ts            # Barrel export for molecules
│   │
│   ├── organisms/              # Complex UI sections
│   │   ├── header/
│   │   │   ├── index.ts
│   │   │   ├── Header.tsx
│   │   │   ├── Header.spec.tsx
│   │   │   ├── Header.stories.tsx
│   │   │   ├── types.ts
│   │   │   └── components/     # Sub-components
│   │   │       ├── HeaderTopBar.tsx
│   │   │       ├── HeaderNavigation.tsx
│   │   │       └── HeaderSearch.tsx
│   │   ├── footer/
│   │   ├── mega-menu/
│   │   ├── search-suggestions/
│   │   ├── product-grid/
│   │   └── index.ts            # Barrel export for organisms
│   │
│   ├── templates/              # Page-level layouts
│   │   ├── main-layout/
│   │   │   ├── index.ts
│   │   │   ├── MainLayout.tsx
│   │   │   ├── MainLayout.spec.tsx
│   │   │   └── types.ts
│   │   ├── checkout-layout/
│   │   ├── account-layout/
│   │   └── index.ts
│   │
│   ├── providers/              # Context providers
│   │   ├── ThemeProvider.tsx
│   │   ├── QueryProvider.tsx
│   │   └── index.ts
│   │
│   └── index.ts                # Main package export
│
├── dist/                       # Build output
├── package.json
├── tsconfig.json
└── README.md
```

### Atomic Design Methodology

The component architecture follows Atomic Design principles:

```
packages/components/
├── atoms/          # Basic building blocks (Button, Input, Icon)
├── molecules/      # Simple combinations (ProductCard, Breadcrumb)
├── organisms/      # Complex components (Header, Footer, MegaMenu)
└── templates/      # Page-level layouts (MainLayout, CheckoutLayout)
```

### Component Organization

#### Atoms
- **Purpose**: Smallest, indivisible components
- **Characteristics**: 
  - Single responsibility
  - No business logic
  - Highly reusable
  - Can be styled but not composed
- **Examples**: `Button`, `Input`, `Icon`, `Badge`, `Price`, `Label`, `Spinner`
- **Location**: `packages/components/src/atoms/`
- **Server/Client**: Usually Client Components (interactive)

#### Molecules
- **Purpose**: Simple combinations of atoms
- **Characteristics**:
  - Composed of 2-5 atoms
  - Simple functionality
  - Reusable across pages
  - May have internal state
- **Examples**: `ProductCard`, `Breadcrumb`, `SearchSuggestionsPanel`, `FormField`, `PriceDisplay`
- **Location**: `packages/components/src/molecules/`
- **Server/Client**: Can be either, depending on interactivity

#### Organisms
- **Purpose**: Complex UI sections
- **Characteristics**:
  - Composed of molecules and atoms
  - Complex functionality
  - May fetch data (Server Components)
  - Page-specific but reusable
- **Examples**: `Header`, `Footer`, `MegaMenu`, `SearchSuggestions`, `ProductGrid`
- **Location**: `packages/components/src/organisms/`
- **Server/Client**: Often Server Components (data fetching)

#### Templates
- **Purpose**: Page-level layouts
- **Characteristics**:
  - Define page structure
  - Composed of organisms
  - Layout-specific logic
  - Used in Next.js layouts
- **Examples**: `MainLayout`, `CheckoutLayout`, `AccountLayout`
- **Location**: `packages/components/src/templates/`
- **Server/Client**: Usually Server Components

### Component Structure

Each component follows a consistent structure:

```
ComponentName/
├── index.ts                    # Public exports
│                               # export { ComponentName } from './ComponentName'
│                               # export type { ComponentNameProps } from './types'
│
├── ComponentName.tsx          # Component implementation
│                               # Main component file
│
├── ComponentName.spec.tsx     # Unit tests (Jest + RTL)
│                               # Component behavior tests
│
├── ComponentName.stories.tsx  # Storybook stories
│                               # Visual documentation
│
├── ComponentName.test.tsx     # Integration tests (optional)
│                               # Component integration tests
│
├── types.ts                   # TypeScript types
│                               # Props, state, and related types
│
├── styles/
│   └── ComponentName.module.css  # CSS Modules (optional)
│                               # Component-specific styles
│
├── components/                # Sub-components (optional)
│   └── SubComponent.tsx       # For complex components
│
├── hooks/                     # Component-specific hooks (optional)
│   └── useComponentLogic.ts   # Custom hooks for component
│
└── utils/                     # Component utilities (optional)
    └── helpers.ts             # Helper functions
```

### Component File Examples

#### Atom Example: Button

```typescript
// packages/components/src/atoms/button/types.ts
export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'outline' | 'ghost'
  size?: 'sm' | 'md' | 'lg'
  isLoading?: boolean
  leftIcon?: React.ReactNode
  rightIcon?: React.ReactNode
}

// packages/components/src/atoms/button/Button.tsx
'use client'

import { cn } from '@company/utils'
import { ButtonProps } from './types'
import styles from './styles.module.css'

export const Button = ({
  variant = 'primary',
  size = 'md',
  isLoading = false,
  leftIcon,
  rightIcon,
  children,
  className,
  disabled,
  ...props
}: ButtonProps) => {
  return (
    <button
      className={cn(
        styles.button,
        styles[variant],
        styles[size],
        isLoading && styles.loading,
        className
      )}
      disabled={disabled || isLoading}
      {...props}
    >
      {leftIcon && <span className={styles.leftIcon}>{leftIcon}</span>}
      {children}
      {rightIcon && <span className={styles.rightIcon}>{rightIcon}</span>}
      {isLoading && <Spinner className={styles.spinner} />}
    </button>
  )
}

// packages/components/src/atoms/button/index.ts
export { Button } from './Button'
export type { ButtonProps } from './types'
```

#### Molecule Example: ProductCard

```typescript
// packages/components/src/molecules/product-card/types.ts
export interface ProductCardProps {
  product: {
    id: string
    name: string
    price: number
    imageUrl: string
    rating?: number
    badge?: string
  }
  onAddToCart?: (productId: string) => void
  className?: string
}

// packages/components/src/molecules/product-card/ProductCard.tsx
import { Button } from '@company/components/atoms/button'
import { Price } from '@company/components/atoms/price'
import { Badge } from '@company/components/atoms/badge'
import { ProductCardProps } from './types'
import styles from './styles.module.css'

export const ProductCard = ({ product, onAddToCart, className }: ProductCardProps) => {
  return (
    <article className={cn(styles.card, className)}>
      {product.badge && <Badge>{product.badge}</Badge>}
      <img src={product.imageUrl} alt={product.name} className={styles.image} />
      <div className={styles.content}>
        <h3 className={styles.title}>{product.name}</h3>
        <Price value={product.price} className={styles.price} />
        {onAddToCart && (
          <Button onClick={() => onAddToCart(product.id)}>
            Add to Cart
          </Button>
        )}
      </div>
    </article>
  )
}

// packages/components/src/molecules/product-card/index.ts
export { ProductCard } from './ProductCard'
export type { ProductCardProps } from './types'
```

#### Organism Example: Header

```typescript
// packages/components/src/organisms/header/types.ts
export interface HeaderProps {
  locale: string
  className?: string
}

// packages/components/src/organisms/header/Header.tsx
import { HeaderTopBar } from './components/HeaderTopBar'
import { HeaderNavigation } from './components/HeaderNavigation'
import { HeaderSearch } from './components/HeaderSearch'
import { HeaderProps } from './types'
import styles from './styles.module.css'

export async function Header({ locale, className }: HeaderProps) {
  // Server Component - can fetch data directly
  const navigationData = await fetchNavigation(locale)
  
  return (
    <header className={cn(styles.header, className)}>
      <HeaderTopBar locale={locale} />
      <HeaderNavigation data={navigationData} />
      <HeaderSearch />
    </header>
  )
}

// packages/components/src/organisms/header/components/HeaderNavigation.tsx
'use client'

import { MegaMenu } from '@company/components/organisms/mega-menu'
import { NavigationData } from '../types'

export function HeaderNavigation({ data }: { data: NavigationData }) {
  return (
    <nav>
      <MegaMenu items={data.items} />
    </nav>
  )
}
```

### Server vs Client Components

#### Server Components (Default)

**When to use**:
- Data fetching
- Accessing backend resources
- Keeping sensitive information on server
- Large dependencies that should not be in client bundle

**Characteristics**:
- Fetch data directly
- No client-side JavaScript
- Better performance
- SEO-friendly
- Cannot use hooks, event handlers, or browser APIs

**Example**:
```typescript
// Server Component
export async function ProductList() {
  const products = await fetchProducts() // Direct fetch
  
  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  )
}
```

#### Client Components (`'use client'`)

**When to use**:
- Interactive components (onClick, onChange, etc.)
- State management (useState, useReducer)
- Browser APIs (localStorage, window, etc.)
- Hooks (useEffect, useContext, etc.)
- Event listeners

**Characteristics**:
- Interactive components
- State management
- Browser APIs
- Event handlers
- Adds to client bundle

**Example**:
```typescript
'use client'

import { useState } from 'react'
import { Button } from '@company/components/atoms/button'

export function Counter() {
  const [count, setCount] = useState(0)
  
  return (
    <div>
      <p>Count: {count}</p>
      <Button onClick={() => setCount(count + 1)}>
        Increment
      </Button>
    </div>
  )
}
```

### Component Composition Patterns

#### Pattern 1: Container/Presentational

```typescript
// Container (Server Component) - fetches data
export async function ProductListContainer() {
  const products = await fetchProducts()
  return <ProductList products={products} />
}

// Presentational (Client Component) - displays data
'use client'
export function ProductList({ products }: { products: Product[] }) {
  return (
    <ul>
      {products.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </ul>
  )
}
```

#### Pattern 2: Compound Components

```typescript
// Compound component pattern
export const Card = {
  Root: CardRoot,
  Header: CardHeader,
  Body: CardBody,
  Footer: CardFooter,
}

// Usage
<Card.Root>
  <Card.Header>Title</Card.Header>
  <Card.Body>Content</Card.Body>
  <Card.Footer>Footer</Card.Footer>
</Card.Root>
```

#### Pattern 3: Render Props

```typescript
export function DataFetcher<T>({
  fetchFn,
  children,
}: {
  fetchFn: () => Promise<T>
  children: (data: T) => React.ReactNode
}) {
  const data = await fetchFn()
  return <>{children(data)}</>
}

// Usage
<DataFetcher fetchFn={fetchProducts}>
  {(products) => <ProductList products={products} />}
</DataFetcher>
```

### Component Export Patterns

#### Barrel Exports

```typescript
// packages/components/src/atoms/index.ts
export { Button } from './button'
export { Input } from './input'
export { Icon } from './icon'
export { Badge } from './badge'
export { Price } from './price'

// packages/components/src/index.ts
export * from './atoms'
export * from './molecules'
export * from './organisms'
export * from './templates'
```

#### Named Exports

```typescript
// packages/components/src/index.ts
export { Button, Input, Icon } from './atoms'
export { ProductCard, Breadcrumb } from './molecules'
export { Header, Footer } from './organisms'
```

### Component Naming Conventions

1. **Component Files**: PascalCase (`Button.tsx`, `ProductCard.tsx`)
2. **Component Folders**: kebab-case (`button/`, `product-card/`)
3. **Types**: PascalCase with suffix (`ButtonProps`, `ProductCardProps`)
4. **Hooks**: camelCase with `use` prefix (`useProduct`, `useCart`)
5. **Utils**: camelCase (`formatPrice`, `validateEmail`)
6. **Constants**: UPPER_SNAKE_CASE (`MAX_ITEMS`, `DEFAULT_TIMEOUT`)

### Component Testing Structure

```typescript
// ComponentName.spec.tsx
import { render, screen } from '@testing-library/react'
import { ComponentName } from './ComponentName'

describe('ComponentName', () => {
  it('renders correctly', () => {
    render(<ComponentName />)
    expect(screen.getByRole('button')).toBeInTheDocument()
  })

  it('handles user interaction', async () => {
    const { user } = render(<ComponentName />)
    await user.click(screen.getByRole('button'))
    // Assertions
  })
})
```

### Storybook Structure

```typescript
// ComponentName.stories.tsx
import type { Meta, StoryObj } from '@storybook/react'
import { ComponentName } from './ComponentName'

const meta: Meta<typeof ComponentName> = {
  title: 'Atoms/ComponentName',
  component: ComponentName,
  tags: ['autodocs'],
}

export default meta
type Story = StoryObj<typeof ComponentName>

export const Default: Story = {
  args: {
    // Default props
  },
}

export const Variant: Story = {
  args: {
    variant: 'secondary',
  },
}
```

### Component Organization in Next.js App Directory

Components used within the Next.js app are organized separately from the shared component library:

```
apps/web/
├── app/
│   ├── [locale]/
│   │   ├── _components/        # App-specific components
│   │   │   ├── header/        # App-specific Header wrapper
│   │   │   ├── footer/         # App-specific Footer wrapper
│   │   │   └── page-specific/ # Components only used in specific pages
│   │   ├── _layouts/           # Layout components
│   │   │   ├── MainLayout.tsx
│   │   │   └── CheckoutLayout.tsx
│   │   ├── _providers/         # App-level providers
│   │   │   ├── QueryProvider.tsx
│   │   │   └── ThemeProvider.tsx
│   │   ├── _hooks/             # App-specific hooks
│   │   │   └── use-app-data.ts
│   │   └── (routes)/           # Route pages
│   │
│   └── api/                    # API route handlers
│
├── components/                 # App-specific components (alternative)
│   ├── ui/                     # App-specific UI components
│   └── features/               # Feature-specific components
│
└── lib/                        # Utility functions
    ├── api/                    # API client
    └── utils/                  # Helper functions
```

#### Component Import Strategy

```typescript
// ✅ Import from shared component library
import { Button, Input } from '@company/components'

// ✅ Import app-specific components
import { AppHeader } from '@/app/[locale]/_components/header/AppHeader'

// ✅ Import from relative path for page-specific components
import { ProductFilters } from './components/ProductFilters'
```

### Component Usage Patterns

#### Pattern 1: Shared Components (Design System)

```typescript
// Use shared components from packages/components
import { Button, ProductCard } from '@company/components'

export function ProductsPage() {
  return (
    <div>
      <Button variant="primary">Click me</Button>
      <ProductCard product={product} />
    </div>
  )
}
```

#### Pattern 2: App-Specific Components

```typescript
// App-specific components in app/_components
// app/[locale]/_components/header/AppHeader.tsx
import { Header } from '@company/components/organisms/header'
import { AppNavigation } from './AppNavigation'

export function AppHeader() {
  return (
    <Header>
      <AppNavigation /> {/* App-specific navigation */}
    </Header>
  )
}
```

#### Pattern 3: Page-Specific Components

```typescript
// Components only used in one page
// app/[locale]/products/components/ProductFilters.tsx
export function ProductFilters() {
  // Component only used in products page
}

// app/[locale]/products/page.tsx
import { ProductFilters } from './components/ProductFilters'

export default function ProductsPage() {
  return (
    <div>
      <ProductFilters />
      {/* Rest of page */}
    </div>
  )
}
```

### Component Dependency Rules

```
┌─────────────────────────────────────────┐
│         packages/components/            │
│  (Shared Design System Components)      │
│                                         │
│  ✅ Can import:                         │
│     - @company/utils                    │
│     - @company/tokens                   │
│     - @company/icons                    │
│     - React, Next.js core               │
│                                         │
│  ❌ Cannot import:                     │
│     - App-specific code                 │
│     - Next.js app directory             │
│     - API routes                        │
└─────────────────────────────────────────┘
                    │
                    │ imports
                    ▼
┌─────────────────────────────────────────┐
│            apps/web/app/                │
│      (Application Components)           │
│                                         │
│  ✅ Can import:                         │
│     - @company/components               │
│     - @company/utils                    │
│     - App-specific components           │
│     - Next.js app directory             │
│                                         │
│  ❌ Cannot import:                     │
│     - Other apps                        │
│     - Internal package files            │
└─────────────────────────────────────────┘
```

### Component Best Practices

1. **Start with Server Components**: Default to Server Components, add `'use client'` only when needed

2. **Composition over Configuration**: Build complex components from simple ones

3. **Single Responsibility**: Each component should do one thing well

4. **Props Interface**: Use TypeScript interfaces for all props

5. **Accessibility**: Include ARIA attributes and semantic HTML

6. **Performance**: Use React.memo for expensive components, useMemo for expensive calculations

7. **Testing**: Write tests for component behavior, not implementation

8. **Documentation**: Include JSDoc comments for complex components

9. **Error Boundaries**: Wrap components in error boundaries where appropriate

10. **Code Splitting**: Use dynamic imports for large components

### Component Checklist

When creating a new component, ensure:

- [ ] Component follows Atomic Design level (atom/molecule/organism/template)
- [ ] Component has proper TypeScript types
- [ ] Component is properly exported (barrel export)
- [ ] Component has unit tests
- [ ] Component has Storybook stories
- [ ] Component follows naming conventions
- [ ] Component is accessible (ARIA, semantic HTML)
- [ ] Component handles loading and error states
- [ ] Component is responsive (mobile-first)
- [ ] Component documentation is updated

---

## Data Fetching & State Management

### React Query (TanStack Query)

**Primary data fetching solution** for all API calls.

#### Configuration
- **Location**: `app/providers/QueryProvider.tsx`
- **Global Settings**:
  - Default retry: 2 attempts
  - Exponential backoff retry delay
  - Stale time: 5 minutes
  - Cache time: 10 minutes

#### Custom Hooks Pattern

All API calls are wrapped in custom hooks:

```typescript
// app/hooks/api/use-meganav.ts
export const useMeganav = (locale: string, options?: QueryOptions) => {
  return useQuery({
    queryKey: ['meganav', locale],
    queryFn: async () => {
      const response = await fetch(`/api/meganav?locale=${locale}`)
      if (!response.ok) throw new Error('Failed to fetch')
      return response.json()
    },
    staleTime: Infinity, // For static data
    gcTime: 1000 * 60 * 60 * 24, // 24 hours
  })
}
```

#### Data Fetching Strategies

1. **Server Components** (Recommended for initial data)
   - Fetch data directly in Server Components
   - Pass data to Client Components as props
   - No client-side JavaScript for initial render

2. **React Query** (For client-side data)
   - Client Components use React Query hooks
   - Automatic caching and synchronization
   - Background refetching

3. **Route Handlers** (`app/api/`)
   - Next.js API routes as backend endpoints
   - Proxy to external APIs
   - Server-side data processing

### State Management

- **React Query**: Server state management
- **Zustand**: Global client state (cart, user preferences)
- **React Context**: Theme, translations (via next-intl)
- **React Hook Form**: Form state management
- **Local State**: Component-level state with `useState`

### API Integration

#### REST API Client

```typescript
// app/lib/api/client.ts
class ApiClient {
  private baseURL: string
  private headers: HeadersInit

  async get<T>(endpoint: string): Promise<T> {
    const response = await fetch(`${this.baseURL}${endpoint}`, {
      headers: this.headers,
    })
    if (!response.ok) throw new Error('API Error')
    return response.json()
  }

  async post<T>(endpoint: string, data: unknown): Promise<T> {
    // Implementation
  }
}

export const apiClient = new ApiClient()
```

#### GraphQL Support (Optional)

- **Apollo Client**: For GraphQL APIs
- **urql**: Lightweight alternative
- **React Query + GraphQL**: Using `graphql-request`

---

## Routing & Templates

### Next.js App Router

**Location**: `app/[locale]/`

#### Route Structure

```
app/
├── [locale]/
│   ├── layout.tsx              # Root layout
│   ├── page.tsx                # Home page
│   ├── brands/
│   │   └── page.tsx            # Brands page
│   ├── checkout/
│   │   ├── layout.tsx         # Checkout layout
│   │   └── [step]/
│   │       └── page.tsx       # Checkout steps
│   └── (account)/
│       ├── layout.tsx         # Account layout (route group)
│       └── profile/
│           └── page.tsx      # Profile page
```

#### Layout System

```typescript
// app/[locale]/layout.tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>
        <Providers>
          <MainLayout>{children}</MainLayout>
        </Providers>
      </body>
    </html>
  )
}
```

#### Template Types

1. **MainLayout**
   - Standard layout with header and footer
   - Uses Server Components for header/footer data
   - Location: `app/[locale]/_layouts/MainLayout.tsx`

2. **CheckoutLayout**
   - Simplified layout for checkout flow
   - No footer, minimal header
   - Location: `app/[locale]/checkout/layout.tsx`

3. **AccountLayout**
   - Account section layout
   - Navigation sidebar
   - Location: `app/[locale]/(account)/layout.tsx`

### Middleware

**Location**: `middleware.ts`

```typescript
// middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  // Locale detection
  // Authentication checks
  // Route protection
  // Redirects
  return NextResponse.next()
}

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)'],
}
```

---

## Styling Architecture

### Tailwind CSS

**Primary styling solution** using utility classes.

#### Configuration

**Location**: `tailwind.config.ts`

```typescript
import type { Config } from 'tailwindcss'

const config: Config = {
  content: [
    './app/**/*.{js,ts,jsx,tsx}',
    './packages/components/**/*.{js,ts,jsx,tsx}',
  ],
  theme: {
    extend: {
      colors: {
        // Design tokens
      },
    },
  },
  plugins: [],
}

export default config
```

#### Design Tokens

**Location**: `packages/tokens/`

- CSS Variables approach
- Style Dictionary (optional)
- Tailwind theme integration

#### Styling Patterns

1. **Utility Classes** (Primary)
   ```tsx
   <div className="flex items-center gap-4 p-6">
   ```

2. **CSS Modules** (For complex components)
   ```tsx
   import styles from './Component.module.css'
   <div className={styles.container}>
   ```

3. **Tailwind with clsx/cn** (Conditional classes)
   ```tsx
   import { cn } from '@company/utils'
   <div className={cn('base-classes', condition && 'conditional-classes')}>
   ```

---

## Server-Side Rendering (SSR)

### Next.js SSR/SSG

**Built-in SSR support** via Next.js App Router.

#### Server Components (Default)

```typescript
// app/[locale]/products/page.tsx
export default async function ProductsPage() {
  const products = await fetchProducts() // Server-side fetch
  
  return (
    <div>
      {products.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  )
}
```

#### Static Site Generation (SSG)

```typescript
// app/[locale]/products/[id]/page.tsx
export async function generateStaticParams() {
  const products = await fetchAllProducts()
  return products.map(product => ({ id: product.id }))
}

export default async function ProductPage({ params }: { params: { id: string } }) {
  const product = await fetchProduct(params.id)
  return <ProductDetails product={product} />
}
```

#### Incremental Static Regeneration (ISR)

```typescript
export const revalidate = 3600 // Revalidate every hour

export default async function Page() {
  const data = await fetchData()
  return <div>{data}</div>
}
```

### SSR Features

- **Automatic Code Splitting**: Route-based
- **Streaming SSR**: React Server Components streaming
- **Partial Prerendering**: Hybrid static/dynamic rendering
- **Image Optimization**: Next.js Image component
- **Font Optimization**: next/font

---

## API Integration Architecture

### API Layer Structure

```
app/
├── api/                          # Next.js API Routes
│   ├── products/
│   │   └── route.ts             # GET /api/products
│   ├── cart/
│   │   └── route.ts             # POST /api/cart
│   └── auth/
│       └── route.ts             # POST /api/auth
│
lib/
├── api/                          # API client utilities
│   ├── client.ts                # Base API client
│   ├── products.ts              # Product API functions
│   └── cart.ts                  # Cart API functions
```

### API Client Pattern

```typescript
// lib/api/client.ts
export class ApiClient {
  constructor(private baseURL: string) {}

  async request<T>(
    endpoint: string,
    options?: RequestInit
  ): Promise<T> {
    const response = await fetch(`${this.baseURL}${endpoint}`, {
      ...options,
      headers: {
        'Content-Type': 'application/json',
        ...options?.headers,
      },
    })

    if (!response.ok) {
      throw new ApiError(response.status, await response.text())
    }

    return response.json()
  }
}
```

### Route Handlers

```typescript
// app/api/products/route.ts
import { NextRequest, NextResponse } from 'next/server'

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams
  const category = searchParams.get('category')

  // Fetch from external API
  const products = await fetchProductsFromExternalAPI(category)

  return NextResponse.json(products)
}
```

### Authentication

#### Options

1. **NextAuth.js** (Recommended)
   - OAuth providers
   - JWT sessions
   - Database sessions

2. **Clerk**
   - Managed authentication
   - Pre-built components

3. **Custom JWT**
   - Full control
   - Custom implementation

---

## Configuration Management

### Configuration Structure

```
config/
├── default.ts              # Base configuration
├── environments/           # Environment-specific configs
│   ├── development.ts
│   ├── staging.ts
│   └── production.ts
└── sites.ts                # Site definitions
```

### Environment Variables

**Location**: `.env.local`, `.env.development`, etc.

```bash
# .env.local
NEXT_PUBLIC_API_URL=https://api.example.com
NEXT_PUBLIC_SITE_ID=uk
DATABASE_URL=postgresql://...
```

### Configuration Pattern

```typescript
// config/default.ts
export const config = {
  api: {
    baseURL: process.env.NEXT_PUBLIC_API_URL || 'http://localhost:3000',
    timeout: 30000,
  },
  site: {
    id: process.env.NEXT_PUBLIC_SITE_ID || 'uk',
    locale: process.env.NEXT_PUBLIC_LOCALE || 'en-GB',
  },
  features: {
    enableAnalytics: process.env.NEXT_PUBLIC_ENABLE_ANALYTICS === 'true',
  },
}
```

---

## Testing Architecture

### Testing Stack

1. **Unit Testing**: Jest + React Testing Library
2. **Component Testing**: React Testing Library
3. **E2E Testing**: Playwright
4. **Visual Regression**: Chromatic or Percy
5. **Component Development**: Storybook

### Test Organization

```
ComponentName/
├── ComponentName.spec.tsx     # Unit tests
├── ComponentName.stories.tsx  # Storybook stories
└── ComponentName.test.tsx     # Integration tests
```

### Testing Patterns

#### Unit Tests

```typescript
// ComponentName.spec.tsx
import { render, screen } from '@testing-library/react'
import { ComponentName } from './ComponentName'

describe('ComponentName', () => {
  it('renders correctly', () => {
    render(<ComponentName />)
    expect(screen.getByText('Hello')).toBeInTheDocument()
  })
})
```

#### E2E Tests

```typescript
// e2e/products.spec.ts
import { test, expect } from '@playwright/test'

test('displays products', async ({ page }) => {
  await page.goto('/products')
  await expect(page.locator('[data-testid="product-card"]')).toBeVisible()
})
```

---

## Build & Deployment

### Build Process

1. **Type Checking**: `tsc --noEmit`
2. **Linting**: `eslint .`
3. **Testing**: `jest`
4. **Build**: `next build`
5. **Export** (if static): `next export`

### Build Output

```
.next/
├── static/                  # Static assets
├── server/                  # Server-side code
└── cache/                   # Build cache
```

### Deployment Options

1. **Vercel** (Recommended for Next.js)
   - Zero-config deployment
   - Automatic optimizations
   - Edge functions

2. **Self-Hosted**
   - Docker container
   - Node.js server
   - Reverse proxy (Nginx)

3. **Cloud Platforms**
   - AWS (Amplify, ECS, Lambda)
   - Google Cloud (Cloud Run)
   - Azure (App Service)

### CI/CD

```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: yarn install
      - run: yarn build
      - run: yarn test
      - run: yarn deploy
```

---

## Monitoring & Observability

### OpenTelemetry

**Purpose**: Distributed tracing and metrics

**Configuration**:
- **Location**: `lib/telemetry.ts`
- **Exporter**: OTLP endpoint
- **Sampling**: Configurable per environment

### Error Tracking

**Sentry Integration**:

```typescript
// lib/sentry.ts
import * as Sentry from '@sentry/nextjs'

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  environment: process.env.NODE_ENV,
})
```

### Analytics

- **Vercel Analytics**: Web vitals
- **Google Analytics 4**: User analytics
- **Custom Analytics**: Event tracking

### Logging

```typescript
// lib/logger.ts
export const logger = {
  info: (message: string, meta?: object) => {
    console.log(JSON.stringify({ level: 'info', message, ...meta }))
  },
  error: (message: string, error?: Error) => {
    console.error(JSON.stringify({ level: 'error', message, error }))
  },
}
```

---

## Key Architectural Patterns

### 1. Server Components First

**Purpose**: Maximize performance with server-side rendering

**Implementation**:
- Default to Server Components
- Use Client Components only when needed (`'use client'`)
- Fetch data in Server Components
- Pass data to Client Components as props

### 2. API Components Pattern

**Purpose**: Separate data fetching from presentation

**Implementation**:
- Server Components fetch data
- Client Components receive data as props
- No prop drilling required

### 3. Layout Composition

**Purpose**: Reusable layout patterns

**Implementation**:
- Nested layouts in App Router
- Route groups for layout organization
- Persistent layouts across routes

### 4. Custom Hooks Pattern

**Purpose**: Reusable data fetching logic

**Implementation**:
- All API calls wrapped in custom hooks
- Hooks use React Query
- Consistent error handling

### 5. Provider Composition

**Purpose**: Centralized provider setup

**Implementation**:
- Providers component wraps all providers
- Consistent provider order
- Easy to add new providers

---

## Performance Optimizations

### Code Splitting

- **Automatic Route Splitting**: Next.js App Router
- **Dynamic Imports**: `next/dynamic`
- **Component Lazy Loading**: Heavy components

### Caching

- **React Query**: Automatic API response caching
- **Next.js Cache**: Built-in fetch caching
- **Static Generation**: Pre-rendered pages
- **CDN**: Static asset caching

### Rendering Optimizations

- **Server Components**: Reduce client bundle
- **Streaming SSR**: Progressive page loading
- **Image Optimization**: Next.js Image component
- **Font Optimization**: next/font

### Bundle Optimization

- **Tree Shaking**: Automatic dead code elimination
- **Minification**: Production builds
- **Compression**: Gzip/Brotli

---

## Security

### Content Security Policy (CSP)

```typescript
// next.config.ts
const securityHeaders = [
  {
    key: 'Content-Security-Policy',
    value: "default-src 'self'; script-src 'self' 'unsafe-eval' 'unsafe-inline';",
  },
]
```

### Security Headers

- **Helmet**: Security headers middleware
- **HTTPS**: Enforced in production
- **Cookie Security**: HttpOnly, Secure flags

### Authentication

- **JWT Tokens**: Secure token storage
- **Session Management**: Secure session handling
- **CSRF Protection**: Token-based protection

---

## Internationalization

### next-intl Setup

**Location**: `app/[locale]/`

```typescript
// middleware.ts
import createMiddleware from 'next-intl/middleware'

export default createMiddleware({
  locales: ['en', 'fr', 'de'],
  defaultLocale: 'en',
})

export const config = {
  matcher: ['/((?!api|_next|.*\\..*).*)'],
}
```

### Multi-Site Support

- **Site Resolution**: URL-based site detection
- **Locale Management**: Per-site locale configuration
- **Translation Files**: `messages/[locale].json`

---

## Summary

This modern architecture provides:

- **Vendor Agnostic**: No Salesforce dependencies
- **Modern Stack**: Next.js 14+ with App Router
- **Performance**: Server Components, streaming SSR
- **Developer Experience**: TypeScript, Tailwind, React Query
- **Scalability**: Monorepo structure, modular architecture
- **Flexibility**: Custom API integration, multiple auth options
- **Observability**: OpenTelemetry, error tracking, analytics

This architecture provides a solid foundation for building and maintaining large-scale Progressive Web Applications of any type (e-commerce, SaaS, content platforms, dashboards, etc.) without vendor lock-in. The patterns and structures described here are universal and can be adapted to any domain-specific requirements.

