[Volver a la documentación C4](readC4.md)

```mermaid
graph TD
    %% --- Actores (definidos en C1, relevantes aquí para mostrar conexiones) ---
    UsuarioWebExt(("Cliente Web\n(Usuario)"))
    UsuarioMovilExt(("Cliente Móvil\n(Usuario)"))
    AdminExt(("Admin del Sistema\n(Usuario)"))

    %% --- Sistemas Externos (definidos en C1) ---
    SistemaPagosExt(("Sistema de Pagos\n(Externo)"))
    SistemaEnviosExt(("Sistema de Envíos\n(Externo)"))
    ProveedorIdentidadExt(("Proveedor de Identidad\n(Externo)"))

    subgraph SistemaOnline ["Sistema de Gestión de Pedidos Online"]
        direction LR

        %% --- Contenedores de Frontend ---
        subgraph FrontendApps ["Aplicaciones Cliente"]
            direction TB
            WebApp["Aplicación Web\n(React)\n[Contenedor: SPA]"]
            MobileApp["Aplicación Móvil\n(Kotlin/Android)\n[Contenedor: App Nativa]"]
        end

        %% --- Contenedor API Gateway ---
        APIGateway["API Gateway\n(Node.js + Express)\n[Contenedor: Aplicación Web]"]

        %% --- Contenedores de Backend (Microservicios) ---
        subgraph BackendServices ["Microservicios"]
            direction TB
            OrderService["Servicio de Pedidos\n(Java Spring Boot)\n[Contenedor: Aplicación Web API]"]
            ProductService["Servicio de Productos\n(Python Flask)\n[Contenedor: Aplicación Web API]"]
            InventoryService["Servicio de Inventario\n(Go)\n[Contenedor: Aplicación Web API]"]
            NotificationService["Servicio de Notificaciones\n(Node.js)\n[Contenedor: Aplicación]"]
        end

        %% --- Contenedores de Almacenamiento de Datos ---
        subgraph DataStores ["Almacenamiento de Datos"]
            direction TB
            OrderDB["BD Pedidos\n(SQL Server)\n[Contenedor: Base de Datos]"]
            ProductDB["BD Productos\n(PostgreSQL)\n[Contenedor: Base de Datos]"]
            InventoryDB["BD Inventario\n(MongoDB)\n[Contenedor: Base de Datos]"]
            MessageQueue["Cola de Mensajes\n(RabbitMQ / Kafka)\n[Contenedor: Sistema de Mensajería]"]
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