[Volver a la documentación C4](readC4.md)

```mermaid
graph TD
    %% --- Elementos Externos al Contenedor "Servicio de Pedidos" ---
    APIGatewayExt[("API Gateway<br/>(Llama a este servicio)")]
    ProductServiceExt[("Servicio de Productos<br/>(Externo a este contenedor)")]
    InventoryServiceExt[("Servicio de Inventario<br/>(Externo a este contenedor)")]
    OrderDBExt[("BD Pedidos (SQL Server)<br/>(Externo a este contenedor)")]
    SistemaPagosExt2[("Sistema de Pagos<br/>(Externo)")]
    MessageQueueExt[("Cola de Mensajes<br/>(Externo a este contenedor)")]

    subgraph OrderServiceContainer [Contenedor: Servicio de Pedidos (Java Spring Boot)]
        direction LR

        %% --- Componentes dentro del Servicio de Pedidos ---
        OrderController[<center><b>OrderController</b><br/>(Spring MVC RestController)<br/><i>[Componente: API Endpoint Handler]</i><br/>Maneja peticiones HTTP/JSON<br/>para crear/consultar pedidos.</center>]
        
        OrderFacade[<center><b>OrderFacade</b><br/>(Spring Service)<br/><i>[Componente: Fachada/Lógica de Orquestación]</i><br/>Orquesta la creación y gestión de pedidos,<br/>coordina con otros componentes y servicios.</center>]

        OrderBusinessLogic[<center><b>OrderBusinessLogic</b><br/>(Spring Service/Component)<br/><i>[Componente: Lógica de Negocio Principal]</i><br/>Valida datos, calcula totales,<br/>aplica reglas de negocio del pedido.</center>]
        
        ProductServiceClient[<center><b>ProductServiceClient</b><br/>(Spring Component con RestTemplate/Feign)<br/><i>[Componente: Cliente de Servicio Externo]</i><br/>Cliente para interactuar con<br/>el Servicio de Productos.</center>]

        InventoryServiceClient[<center><b>InventoryServiceClient</b><br/>(Spring Component con RestTemplate/Feign)<br/><i>[Componente: Cliente de Servicio Externo]</i><br/>Cliente para interactuar con<br/>el Servicio de Inventario.</center>]

        PaymentGatewayClient[<center><b>PaymentGatewayClient</b><br/>(Spring Component)<br/><i>[Componente: Integración de Pagos]</i><br/>Interactúa con el Sistema de Pagos externo.</center>]
        
        OrderRepository[<center><b>OrderRepository</b><br/>(Spring Data JPA Repository)<br/><i>[Componente: Acceso a Datos]</i><br/>Persiste y recupera<br/>entidades de Pedido.</center>]
        
        DomainModel[<center><b>Modelo de Dominio</b><br/>(Clases POJO/Entidades JPA)<br/><i>[Componente: Clases de Datos]</i><br/>Ej: Order, OrderItem, Address</center>]

        OrderEventPublisher[<center><b>OrderEventPublisher</b><br/>(Spring Component con RabbitTemplate/KafkaTemplate)<br/><i>[Componente: Publicador de Eventos]</i><br/>Publica eventos de pedido (ej: PedidoCreado)</center>]

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