[Volver a la documentación C4](readC4.md)

```mermaid
graph TD
    %% --- Actores (definidos en C1, relevantes aquí para mostrar conexiones) ---
    UsuarioWebExt(("Cliente Web (Usuario)"))
    UsuarioMovilExt(("Cliente Móvil (Usuario)"))
    AdminExt(("Admin del Sistema (Usuario)"))

    %% --- Sistemas Externos (definidos en C1) ---
    SistemaPagosExt(("Sistema de Pagos (Externo)"))
    SistemaEnviosExt(("Sistema de Envíos (Externo)"))
    ProveedorIdentidadExt(("Proveedor de Identidad (Externo)"))

    subgraph SistemaOnline ["Sistema de Gestión de Pedidos Online"]
        direction LR

        %% --- Contenedores de Frontend ---
        subgraph FrontendApps ["Aplicaciones Cliente"]
            direction TB
            WebApp["Aplicación Web (React) - [Contenedor: SPA]"]
            MobileApp["Aplicación Móvil (Kotlin/Android) - [Contenedor: App Nativa]"]
        end

        %% --- Contenedor API Gateway ---
        APIGateway["API Gateway (Node.js + Express) - [Contenedor: Aplicación Web]"]

        %% --- Contenedores de Backend (Microservicios) ---
        subgraph BackendServices ["Microservicios"]
            direction TB
            OrderService["Servicio de Pedidos (Java Spring Boot) - [Contenedor: Aplicación Web API]"]
            ProductService["Servicio de Productos (Python Flask) - [Contenedor: Aplicación Web API]"]
            InventoryService["Servicio de Inventario (Go) - [Contenedor: Aplicación Web API]"]
            NotificationService["Servicio de Notificaciones (Node.js) - [Contenedor: Aplicación]"]
        end

        %% --- Contenedores de Almacenamiento de Datos ---
        subgraph DataStores ["Almacenamiento de Datos"]
            direction TB
            OrderDB["BD Pedidos (SQL Server) - [Contenedor: Base de Datos]"]
            ProductDB["BD Productos (PostgreSQL) - [Contenedor: Base de Datos]"]
            InventoryDB["BD Inventario (MongoDB) - [Contenedor: Base de Datos]"]
            MessageQueue["Cola de Mensajes (RabbitMQ / Kafka) - [Contenedor: Sistema de Mensajería]"]
        end
    end

    %% --- Interacciones ---
    UsuarioWebExt -- "HTTPS" --> WebApp
    UsuarioMovilExt -- "HTTPS" --> MobileApp
    AdminExt -- "HTTPS" --> WebApp

    WebApp -- "HTTPS/JSON (API Externa)" --> APIGateway
    MobileApp -- "HTTPS/JSON (API Externa)" --> APIGateway

    APIGateway -- "Autenticación (OAuth2/OIDC)" --> ProveedorIdentidadExt
    APIGateway -- "HTTP/gRPC (API Interna)" --> OrderService
    APIGateway -- "HTTP/gRPC (API Interna)" --> ProductService
    APIGateway -- "HTTP/gRPC (API Interna)" --> InventoryService

    OrderService -- "JDBC" --> OrderDB
    OrderService -- "HTTP/gRPC" --> ProductService
    OrderService -- "HTTP/gRPC" --> InventoryService
    OrderService -- "Procesar Pago (API Externa)" --> SistemaPagosExt
    OrderService -- "Publica Evento (AMQP/Kafka)" --> MessageQueue

    ProductService -- "JDBC" --> ProductDB
    InventoryService -- "Driver NoSQL" --> InventoryDB
    
    NotificationService -- "Consume Evento (AMQP/Kafka)" --> MessageQueue
    NotificationService -- "Solicita Envío (API Externa)" --> SistemaEnviosExt

    %% Estilos
    classDef container fill:#e6ffe6,stroke:#4A4,stroke-width:2px,rx:5px
    classDef datastore fill:#ffe6cc,stroke:#D35400,stroke-width:2px,rx:5px
    classDef apigw fill:#fff0b3,stroke:#D35400,stroke-width:2px,rx:5px
    class WebApp,MobileApp,OrderService,ProductService,InventoryService,NotificationService container
    class APIGateway apigw
    class OrderDB,ProductDB,InventoryDB,MessageQueue datastore