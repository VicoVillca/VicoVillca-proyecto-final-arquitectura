## Descripción
Este PR implementa el esquema GraphQL para el catálogo de productos, permitiendo consultas eficientes y flexibles desde el frontend sin sobrescribir datos innecesarios.

## Cambios realizados
- ✅ Definición de tipos principales (Product, Category, Review)
- ✅ Creación de queries (products, product, categories)
- ✅ Creación de mutations (addProduct, updateProduct, deleteProduct)
- ✅ Implementación de resolvers básicos (descripción textual)
- ✅ Documentación de subscriptions para actualizaciones en tiempo real

## Archivos modificados
- `docs/graphql/schema.md` - Esquema completo de GraphQL

## Esquema GraphQL

### Tipos definidos
```graphql
type Product {
  id: ID!
  name: String!
  description: String
  price: Float!
  category: Category!
  reviews: [Review!]!
  stock: Int!
  images: [String!]!
  createdAt: String!
  updatedAt: String!
}

type Category {
  id: ID!
  name: String!
  description: String
  products: [Product!]!
  parentCategory: Category
  subcategories: [Category!]!
}

type Review {
  id: ID!
  product: Product!
  user: User!
  rating: Int!
  comment: String
  createdAt: String!
}
```

### Queries implementadas
- `products(filters: ProductFilters, pagination: PaginationInput): [Product!]!` - Listar productos con filtros
- `product(id: ID!): Product` - Obtener producto por ID
- `categories: [Category!]!` - Listar todas las categorías

### Mutations implementadas
- `addProduct(input: ProductInput!): Product!` - Crear nuevo producto
- `updateProduct(id: ID!, input: ProductUpdateInput!): Product!` - Actualizar producto
- `deleteProduct(id: ID!): Boolean!` - Eliminar producto

### Subscriptions
- `productAdded: Product!` - Notificar cuando se agrega un producto
- `productUpdated: Product!` - Notificar cuando se actualiza un producto
- `productDeleted: ID!` - Notificar cuando se elimina un producto

## Ejemplos de consultas incluidos

### Consulta básica de productos
```graphql
query GetProducts {
  products {
    id
    name
    price
    category {
      name
    }
  }
}
```

### Consulta con filtros
```graphql
query GetFilteredProducts {
  products(
    filters: {
      categoryId: "cat_123"
      minPrice: 100
      maxPrice: 500
      inStock: true
    }
    pagination: {
      page: 1
      limit: 10
    }
  ) {
    id
    name
    price
    stock
    reviews {
      rating
    }
  }
}
```

### Consulta con relaciones anidadas
```graphql
query GetProductWithDetails {
  product(id: "prod_123") {
    name
    description
    price
    category {
      name
      parentCategory {
        name
      }
    }
    reviews {
      rating
      comment
      user {
        name
      }
    }
  }
}
```

### Mutación para crear producto
```graphql
mutation CreateProduct {
  addProduct(
    input: {
      name: "Smartphone XYZ"
      description: "Último modelo con 5G"
      price: 599.99
      categoryId: "cat_456"
      stock: 50
      images: ["https://ejemplo.com/img1.jpg"]
    }
  ) {
    id
    name
    price
    category {
      name
    }
    createdAt
  }
}
```

### Mutación para actualizar producto
```graphql
mutation UpdateProductPrice {
  updateProduct(
    id: "prod_123"
    input: {
      price: 549.99
      stock: 45
    }
  ) {
    id
    name
    price
    stock
    updatedAt
  }
}
```

### Mutación para eliminar producto
```graphql
mutation DeleteProduct {
  deleteProduct(id: "prod_123")
}
```

### Subscription para nuevos productos
```graphql
subscription OnProductAdded {
  productAdded {
    id
    name
    price
    category {
      name
    }
    createdAt
  }
}
```

## Descripción de Resolvers

### Query Resolvers
- **products**: Obtiene lista de productos de la base de datos aplicando filtros y paginación
- **product**: Busca un producto por su ID en la base de datos
- **categories**: Obtiene todas las categorías con sus relaciones jerárquicas

### Mutation Resolvers
- **addProduct**: Valida datos de entrada, crea nuevo producto en la base de datos y publica evento `productAdded`
- **updateProduct**: Valida permisos, actualiza producto existente y publica evento `productUpdated`
- **deleteProduct**: Verifica existencia, elimina producto y publica evento `productDeleted`

### Field Resolvers
- **Product.category**: Resuelve la relación obteniendo la categoría del producto
- **Product.reviews**: Obtiene todas las reseñas asociadas al producto
- **Category.products**: Resuelve relación inversa obteniendo productos de la categoría
- **Category.subcategories**: Obtiene categorías hijas de manera recursiva

## Cómo probar
1. Revisar el archivo `docs/graphql/schema.md`
2. Verificar que el esquema es válido y completo
3. Probar los ejemplos de consultas en un playground de GraphQL
4. Validar que las mutations cubren los casos de uso necesarios

## Checklist
- [x] Tipos principales definidos (Product, Category, Review)
- [x] Queries implementadas (mínimo 3)
- [x] Mutations implementadas (mínimo 3)
- [x] Resolvers documentados
- [x] Subscriptions incluidas
- [x] Ejemplos de consultas proporcionados

## Issue relacionado
Closes #2 - Definir esquema GraphQL para consultas de catálogo