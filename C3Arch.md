[Volver a la documentación C4](readC4.md)

```mermaid
graph TD
    %% --- Elementos Externos al Contenedor "Servicio de Pedidos" ---
    APIGatewayExt(("API Gateway (Llama a este servicio)"))
    ProductServiceExt(("Servicio de Productos (Externo a este contenedor)"))
    InventoryServiceExt(("Servicio de Inventario (Externo a este contenedor)"))
    OrderDBExt(("BD Pedidos (SQL Server) (Externo a este contenedor)"))
    SistemaPagosExt2(("Sistema de Pagos (Externo)"))
    MessageQueueExt(("Cola de Mensajes (Externo a este contenedor)"))

    subgraph OrderServiceContainer ["Contenedor: Servicio de Pedidos (Java Spring Boot)"]
        direction LR

        %% --- Componentes dentro del Servicio de Pedidos ---
        OrderController["OrderController (Spring MVC RestController) - [Componente: API Endpoint Handler] - Maneja peticiones HTTP/JSON para crear/consultar pedidos."]
        
        OrderFacade["OrderFacade (Spring Service) - [Componente: Fachada/Lógica de Orquestación] - Orquesta la creación y gestión de pedidos, coordina con otros componentes y servicios."]

        OrderBusinessLogic["OrderBusinessLogic (Spring Service/Component) - [Componente: Lógica de Negocio Principal] - Valida datos, calcula totales, aplica reglas de negocio del pedido."]
        
        ProductServiceClient["ProductServiceClient (Spring Component con RestTemplate/Feign) - [Componente: Cliente de Servicio Externo] - Cliente para interactuar con el Servicio de Productos."]

        InventoryServiceClient["InventoryServiceClient (Spring Component con RestTemplate/Feign) - [Componente: Cliente de Servicio Externo] - Cliente para interactuar con el Servicio de Inventario."]

        PaymentGatewayClient["PaymentGatewayClient (Spring Component) - [Componente: Integración de Pagos] - Interactúa con el Sistema de Pagos externo."]
        
        OrderRepository["OrderRepository (Spring Data JPA Repository) - [Componente: Acceso a Datos] - Persiste y recupera entidades de Pedido."]
        
        DomainModel["Modelo de Dominio (Clases POJO/Entidades JPA) - [Componente: Clases de Datos] - Ej: Order, OrderItem, Address"]

        OrderEventPublisher["OrderEventPublisher (Spring Component con RabbitTemplate/KafkaTemplate) - [Componente: Publicador de Eventos] - Publica eventos de pedido (ej: PedidoCreado)"]

    end

    %% --- Interacciones ---
    APIGatewayExt -- "1. Petición HTTP (ej: POST /orders)" --> OrderController
    OrderController -- "2. Delega a" --> OrderFacade
    OrderFacade -- "3. Usa" --> OrderBusinessLogic
    OrderFacade -- "4. Llama para info de producto" --> ProductServiceClient
    ProductServiceClient -- "5. Llama (HTTP/gRPC)" --> ProductServiceExt
    OrderFacade -- "6. Llama para verificar stock" --> InventoryServiceClient
    InventoryServiceClient -- "7. Llama (HTTP/gRPC)" --> InventoryServiceExt
    OrderFacade -- "8. Usa para procesar pago" --> PaymentGatewayClient
    PaymentGatewayClient -- "9. Llama (API Externa)" --> SistemaPagosExt2
    OrderFacade -- "10. Persiste/Recupera usando" --> OrderRepository
    OrderRepository -- "11. Accede (JDBC)" --> OrderDBExt
    OrderBusinessLogic -- "Usa" --> DomainModel
    OrderRepository -- "Usa/Mapea" --> DomainModel
    OrderFacade -- "12. Publica evento usando" --> OrderEventPublisher
    OrderEventPublisher -- "13. Envía mensaje a" --> MessageQueueExt

    %% Estilos
    classDef component fill:#d5e8d4,stroke:#82b366,stroke-width:2px,rx:5px
    class OrderController,OrderFacade,OrderBusinessLogic,ProductServiceClient,InventoryServiceClient,PaymentGatewayClient,OrderRepository,DomainModel,OrderEventPublisher component