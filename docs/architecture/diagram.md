# Arquitectura del Sistema - Catálogo de Productos

## Visión General
Este documento describe la arquitectura del sistema de catálogo de productos, mostrando la interacción entre servicios, bases de datos y clientes. Se utiliza una arquitectura basada en microservicios con API Gateway para manejar las peticiones de los clientes.

## Patrones Arquitectónicos Utilizados
- **Microservicios**: Separación por dominios de negocio
- **API Gateway**: Punto único de entrada para clientes
- **CQRS**: Separación de lecturas y escrituras (donde aplica)
- **Event-Driven**: Comunicación asíncrona entre servicios
- **Database per Service**: Base de datos independiente por servicio

## Diagrama de Contexto del Sistema

```mermaid
graph TB
    subgraph "Usuarios"
        A[Usuario Frontend<br/>Web/Mobile]
        B[Administrador<br/>Backoffice]
    end
    
    subgraph "Sistema de Catálogo"
        C[API Gateway]
    end
    
    subgraph "Servicios Externos"
        D[Proveedor de Pago]
        E[Servicio de Email]
        F[CDN de Imágenes]
    end
    
    A --> C
    B --> C
    C --> D
    C --> E
    C --> F
    
    classDef user fill:#e1f5fe,stroke:#01579b
    classDef system fill:#fff3e0,stroke:#e65100
    classDef external fill:#f3e5f5,stroke:#4a148c
    
    class A,B user
    class C system
    class D,E,F external
```

### Leyenda - Diagrama de Contexto
| Color | Tipo | Descripción |
|-------|------|-------------|
| 🔵 Azul | Usuarios | Actores que interactúan con el sistema |
| 🟠 Naranja | Sistema | Nuestro sistema principal |
| 🟣 Púrpura | Externos | Servicios de terceros |

## Diagrama de Contenedores

```mermaid
graph TB
    subgraph "Clientes"
        A[Aplicación Web<br/>React SPA]
        B[Aplicación Móvil<br/>React Native]
        C[Backoffice Admin<br/>React Admin]
    end
    
    subgraph "Edge"
        D[CDN<br/>CloudFront]
        E[Load Balancer<br/>Nginx]
    end
    
    subgraph "API Layer"
        F[API Gateway<br/>Kong/KrakenD]
    end
    
    subgraph "Microservicios"
        G[Servicio de Productos<br/>Node.js/Express]
        H[Servicio de Usuarios<br/>Python/FastAPI]
        I[Servicio de Pedidos<br/>Java/Spring]
        J[Servicio de Búsqueda<br/>Elasticsearch]
        K[Servicio de Reviews<br/>Go]
    end
    
    subgraph "Bases de Datos"
        L[(PostgreSQL<br/>Productos)]
        M[(MongoDB<br/>Usuarios)]
        N[(PostgreSQL<br/>Pedidos)]
        O[(Elasticsearch<br/>Índices)]
        P[(Redis<br/>Caché)]
    end
    
    subgraph "Message Queue"
        Q[RabbitMQ/Kafka]
    end
    
    A --> D
    B --> D
    C --> E
    D --> F
    E --> F
    F --> G
    F --> H
    F --> I
    F --> J
    F --> K
    
    G --> L
    G --> P
    H --> M
    I --> N
    J --> O
    K --> Q
    
    G <--> Q
    I <--> Q
    K <--> Q
    
    classDef client fill:#e1f5fe,stroke:#01579b
    classDef edge fill:#b2ebf2,stroke:#006064
    classDef api fill:#fff3e0,stroke:#e65100
    classDef service fill:#c8e6c9,stroke:#1b5e20
    classDef db fill:#ffccbc,stroke:#bf360c
    classDef queue fill:#ffe0b2,stroke:#e65100
    
    class A,B,C client
    class D,E edge
    class F api
    class G,H,I,J,K service
    class L,M,N,O,P db
    class Q queue
```

### Leyenda - Diagrama de Contenedores
| Color | Tipo | Descripción |
|-------|------|-------------|
| 🔵 Azul | Clientes | Aplicaciones que consumen la API |
| 🔷 Celeste | Edge | Capa de borde (CDN, Load Balancer) |
| 🟠 Naranja | API Layer | Punto de entrada único |
| 🟢 Verde | Microservicios | Servicios core del negocio |
| 🟤 Café | Bases de Datos | Almacenamiento persistente |
| 🟡 Amarillo | Message Queue | Comunicación asíncrona |

## Diagrama de Componentes Internos (Servicio de Productos)

```mermaid
graph TB
    subgraph "API Layer"
        A[REST Endpoints<br/>/api/products]
        B[GraphQL Endpoint<br/>/graphql]
        C[Middleware<br/>Auth/Rate Limit]
    end
    
    subgraph "Capa de Aplicación"
        D[Product Controller]
        E[Category Controller]
        F[Review Controller]
        G[Validation Service]
    end
    
    subgraph "Capa de Negocio"
        H[Product Service]
        I[Category Service]
        J[Review Service]
        K[Search Service]
        L[Cache Service]
    end
    
    subgraph "Capa de Acceso a Datos"
        M[Product Repository]
        N[Category Repository]
        O[Review Repository]
        P[Search Repository]
    end
    
    subgraph "Infraestructura"
        Q[(PostgreSQL)]
        R[(Elasticsearch)]
        S[(Redis)]
        T[RabbitMQ]
    end
    
    A --> C
    B --> C
    C --> D
    C --> E
    C --> F
    
    D --> G
    E --> G
    F --> G
    
    D --> H
    E --> I
    F --> J
    
    H --> K
    H --> L
    I --> L
    J --> K
    
    H --> M
    I --> N
    J --> O
    K --> P
    
    M --> Q
    N --> Q
    O --> Q
    P --> R
    L --> S
    H --> T
    J --> T
    
    classDef api fill:#fff3e0,stroke:#e65100
    classDef app fill:#c8e6c9,stroke:#1b5e20
    classDef business fill:#bbdefb,stroke:#0d47a1
    classDef data fill:#ffccbc,stroke:#bf360c
    classDef infra fill:#ffe0b2,stroke:#e65100
    
    class A,B,C api
    class D,E,F,G app
    class H,I,J,K,L business
    class M,N,O,P data
    class Q,R,S,T infra
```

## Especificación de Tecnologías Propuestas

### Frontend
| Componente | Tecnología | Versión | Justificación |
|------------|------------|---------|---------------|
| Web App | React | 18.x | Amplia comunidad, componentes reutilizables |
| Mobile App | React Native | 0.72 | Código compartido con web |
| Backoffice | React Admin | 4.x | Rápido desarrollo de paneles administrativos |
| State Management | Redux Toolkit | 2.x | Manejo predecible del estado |
| UI Framework | Material-UI | 5.x | Componentes accesibles y personalizables |

### Backend
| Componente | Tecnología | Versión | Justificación |
|------------|------------|---------|---------------|
| API Gateway | KrakenD/Kong | 2.x | Alto rendimiento, fácil configuración |
| Servicio Productos | Node.js/Express | 20.x | Rapidez de desarrollo, async/await |
| Servicio Usuarios | Python/FastAPI | 0.100 | Tipado fuerte, documentación automática |
| Servicio Pedidos | Java/Spring Boot | 3.x | Robustez para transacciones complejas |
| Servicio Búsqueda | Elasticsearch | 8.x | Búsquedas avanzadas y rápidas |
| Servicio Reviews | Go | 1.21 | Alto rendimiento para alta concurrencia |

### Bases de Datos
| Componente | Tecnología | Versión | Justificación |
|------------|------------|---------|---------------|
| Productos | PostgreSQL | 15.x | Integridad referencial, JSONB support |
| Usuarios | MongoDB | 7.x | Flexibilidad de esquemas |
| Pedidos | PostgreSQL | 15.x | ACID compliance |
| Búsqueda | Elasticsearch | 8.x | Búsqueda de texto completo |
| Caché | Redis | 7.x | Alta velocidad, soporte de estructuras |

### Infraestructura
| Componente | Tecnología | Versión | Justificación |
|------------|------------|---------|---------------|
| Contenedores | Docker | 24.x | Portabilidad, consistencia |
| Orquestación | Kubernetes | 1.28 | Auto-escalado, auto-curación |
| Message Queue | RabbitMQ | 3.12 | Comunicación asíncrona confiable |
| CDN | CloudFront | - | Distribución global de contenido |
| Load Balancer | Nginx | 1.24 | Balanceo de carga, SSL termination |

## Flujo de Datos entre Componentes

### Flujo de Consulta de Productos
```mermaid
sequenceDiagram
    participant C as Cliente Web
    participant G as API Gateway
    participant P as Servicio Productos
    participant R as Redis Cache
    participant D as PostgreSQL
    participant E as Elasticsearch
    
    C->>G: GET /api/products?search=laptop
    G->>P: Reenviar petición
    P->>R: Buscar en caché
    alt Cache Hit
        R-->>P: Datos cacheados
    else Cache Miss
        P->>E: Buscar en Elasticsearch
        E-->>P: IDs de productos
        P->>D: Obtener detalles por IDs
        D-->>P: Detalles completos
        P->>R: Guardar en caché
    end
    P-->>G: Respuesta JSON
    G-->>C: Productos encontrados
```

### Flujo de Creación de Pedido
```mermaid
sequenceDiagram
    participant C as Cliente
    participant G as API Gateway
    participant S as Servicio Pedidos
    participant P as Servicio Productos
    participant U as Servicio Usuarios
    participant Q as RabbitMQ
    participant E as Servicio Email
    
    C->>G: POST /api/orders
    G->>S: Crear pedido
    S->>U: Validar usuario
    U-->>S: Usuario válido
    S->>P: Verificar stock
    P-->>S: Stock disponible
    S->>S: Crear pedido
    S->>P: Actualizar stock
    S->>Q: Publicar evento "order.created"
    S-->>G: Pedido creado
    G-->>C: Confirmación
    
    Q->>E: Consumir evento
    E->>E: Enviar email
    E-->>Q: Email enviado
```

### Flujo de Actualización en Tiempo Real
```mermaid
sequenceDiagram
    participant A as Admin Backoffice
    participant G as API Gateway
    participant P as Servicio Productos
    participant D as PostgreSQL
    participant Q as RabbitMQ
    participant W as WebSocket Server
    participant C as Clientes
    
    A->>G: PUT /api/products/123
    G->>P: Actualizar producto
    P->>D: Guardar cambios
    D-->>P: Confirmación
    P->>Q: Publicar "product.updated"
    P-->>G: Éxito
    G-->>A: 200 OK
    
    Q->>W: Enviar actualización
    W->>C: WebSocket "productUpdated"
    C->>C: Actualizar UI
```

## Consideraciones de Arquitectura

### Escalabilidad
- **Horizontal**: Cada microservicio puede escalar independientemente
- **Vertical**: Optimización de recursos por servicio
- **Auto-scaling**: Basado en métricas de CPU/memoria y colas

### Seguridad
- **API Gateway**: Rate limiting, IP whitelisting
- **Autenticación**: JWT con OAuth2
- **Autorización**: RBAC por servicio
- **HTTPS**: TLS 1.3 en todas las comunicaciones
- **Secretos**: HashiCorp Vault o Kubernetes Secrets

### Disponibilidad
- **Alta disponibilidad**: Mínimo 3 réplicas por servicio crítico
- **Multi-AZ**: Distribución en múltiples zonas de disponibilidad
- **Circuit Breaker**: Prevención de fallos en cascada
- **Retry Policies**: Reintentos con backoff exponencial

### Observabilidad
- **Logs**: ELK Stack o Loki
- **Métricas**: Prometheus + Grafana
- **Tracing**: Jaeger o Zipkin
- **Alertas**: AlertManager + PagerDuty

## Estructura de Archivos Propuesta

```
docs/
└── architecture/
    └── diagram.md           # Este archivo

infrastructure/
├── kubernetes/
│   ├── namespaces.yaml
│   ├── deployments/
│   └── services/
├── docker/
│   ├── product-service/
│   ├── user-service/
│   └── order-service/
└── terraform/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

## Próximos Pasos
1. ✅ Definir arquitectura de componentes
2. ⬜ Configurar repositorios de microservicios
3. ⬜ Implementar API Gateway
4. ⬜ Configurar bases de datos
5. ⬜ Establecer pipelines CI/CD
6. ⬜ Implementar monitoreo y alertas

---
**Última actualización:** Febrero 2026  
**Versión:** 1.0.0  
**Autores:** Equipo de Arquitectura