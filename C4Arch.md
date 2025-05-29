[Volver a la documentación C4](readC4.md)

classDiagram
    direction LR
    class JpaRepository {
        <<interface>>
        +save(S entity) S
        +findById(ID id) Optional<T>
        +findAll() List<T>
        +deleteById(ID id) void
        %% ... otros métodos CRUD
    }

    class OrderRepository {
        <<interface>>
        %% Hereda métodos de JpaRepository<Order, Long>
        +findByCustomerId(Long customerId) List<Order>
    }
    JpaRepository <|-- OrderRepository

    class Order {
        <<@Entity>>
        +Long id
        +Long customerId
        +Date orderDate
        +OrderStatus status
        +Address shippingAddress
        +Address billingAddress
        +List<OrderItem> items
        +BigDecimal totalAmount
        +createOrder()
        +addItem(Product product, int quantity)
        +calculateTotal()
    }
    OrderRepository ..> Order : "manages"

    class OrderItem {
        <<@Entity>>
        +Long id
        +String productId
        +String productName
        +int quantity
        +BigDecimal unitPrice
        +BigDecimal subTotal
    }
    Order "1" *-- "0..*" OrderItem : "contains"

    class Address {
        <<@Embeddable / @Entity>>
        +String street
        +String city
        +String postalCode
        +String country
    }
    Order -- "1" Address : "shipping"
    Order -- "1" Address : "billing"

    class OrderStatus {
        <<enum>>
        PENDING
        PAID
        SHIPPED
        DELIVERED
        CANCELLED
    }
    Order -- OrderStatus

    %% Nota: Product no está detallado aquí pero OrderItem lo referencia
    class Product {
        +String id
        +String name
        +BigDecimal price
    }
    OrderItem ..> Product : "references"