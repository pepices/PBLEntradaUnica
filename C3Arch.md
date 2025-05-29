[Volver a la documentación C4](readC4.md)

```mermaid
graph TD
    %% --- Elementos Externos al Contenedor "Servicio de Pedidos" ---
    APIGatewayExt(("API Gateway\n(Llama a este servicio)"))
    ProductServiceExt(("Servicio de Productos\n(Externo a este contenedor)"))
    InventoryServiceExt(("Servicio de Inventario\n(Externo a este contenedor)"))
    OrderDBExt(("BD Pedidos (SQL Server)\n(Externo a este contenedor)"))
    SistemaPagosExt2(("Sistema de Pagos\n(Externo)"))
    MessageQueueExt(("Cola de Mensajes\n(Externo a este contenedor)"))

    subgraph OrderServiceContainer ["Contenedor: Servicio de Pedidos (Java Spring Boot)"]
        direction LR

        %% --- Componentes dentro del Servicio de Pedidos ---
        OrderController["OrderController\n(Spring MVC RestController)\n[Componente: API Endpoint Handler]\nManeja peticiones HTTP/JSON\npara crear/consultar pedidos."]
        
        OrderFacade["OrderFacade\n(Spring Service)\n[Componente: Fachada/Lógica de Orquestación]\nOrquesta la creación y gestión de pedidos,\ncoordina con otros componentes y servicios."]

        OrderBusinessLogic["OrderBusinessLogic\n(Spring Service/Component)\n[Componente: Lógica de Negocio Principal]\nValida datos, calcula totales,\naplica reglas de negocio del pedido."]
        
        ProductServiceClient["ProductServiceClient\n(Spring Component con RestTemplate/Feign)\n[Componente: Cliente de Servicio Externo]\nCliente para interactuar con\nel Servicio de Productos."]

        InventoryServiceClient["InventoryServiceClient\n(Spring Component con RestTemplate/Feign)\n[Componente: Cliente de Servicio Externo]\nCliente para interactuar con\nel Servicio de Inventario."]

        PaymentGatewayClient["PaymentGatewayClient\n(Spring Component)\n[Componente: Integración de Pagos]\nInteractúa con el Sistema de Pagos externo."]
        
        OrderRepository["OrderRepository\n(Spring Data JPA Repository)\n[Componente: Acceso a Datos]\nPersiste y recupera\nentidades de Pedido."]
        
        DomainModel["Modelo de Dominio\n(Clases POJO/Entidades JPA)\n[Componente: Clases de Datos]\nEj: Order, OrderItem, Address"]

        OrderEventPublisher["OrderEventPublisher\n(Spring Component con RabbitTemplate/KafkaTemplate)\n[Componente: Publicador de Eventos]\nPublica eventos de pedido (ej: PedidoCreado)"]

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