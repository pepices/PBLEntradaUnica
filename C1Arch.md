[Volver a la documentación C4](readC4.md)

```mermaid
graph TD
    %% --- Personas ---
    ClienteWeb["Cliente Web\n(Usuario)"]
    ClienteMovil["Cliente Móvil\n(Usuario)"]
    Admin["Administrador del Sistema\n(Usuario)"]

    %% --- Sistema de Software (El nuestro) ---
    SistemaPedidos["Sistema de Gestión de Pedidos Online\n[Sistema de Software]"]

    %% --- Sistemas Externos ---
    SistemaPagos["Sistema de Pagos\n(Ej: Stripe)\n[Sistema de Software Externo]"]
    SistemaEnvios["Sistema de Envíos\n(Ej: FedEx API)\n[Sistema de Software Externo]"]
    ProveedorIdentidad["Proveedor de Identidad\n(Ej: Auth0, EntraID)\n[Sistema de Software Externo]"]

    %% --- Relaciones ---
    ClienteWeb -- "Realiza Pedidos,\nConsulta Estado" --> SistemaPedidos
    ClienteMovil -- "Realiza Pedidos,\nConsulta Estado" --> SistemaPedidos
    Admin -- "Gestiona Productos,\nRevisa Pedidos,\nConfigura Sistema" --> SistemaPedidos

    SistemaPedidos -- "Procesa Pagos" --> SistemaPagos
    SistemaPedidos -- "Gestiona Envíos" --> SistemaEnvios
    SistemaPedidos -- "Autentica Usuarios" --> ProveedorIdentidad

    %% Estilos
    style SistemaPedidos fill:#1168bd,stroke:#000,stroke-width:2px,color:#fff
    classDef persona fill:#08427b,stroke:#000,stroke-width:2px,color:#fff
    class ClienteWeb,ClienteMovil,Admin persona
    classDef sistemaExterno fill:#999,stroke:#000,stroke-width:2px,color:#fff
    class SistemaPagos,SistemaEnvios,ProveedorIdentidad sistemaExterno