[Volver a la documentación C4](readC4.md)

graph TD
    %% --- Personas ---
    ClienteWeb[<center><b>Cliente Web</b><br/>(Usuario)</center>]
    ClienteMovil[<center><b>Cliente Móvil</b><br/>(Usuario)</center>]
    Admin[<center><b>Administrador<br/>del Sistema</b><br/>(Usuario)</center>]

    %% --- Sistema de Software (El nuestro) ---
    SistemaPedidos[<center><b>Sistema de Gestión<br/>de Pedidos Online</b><br/><i>[Sistema de Software]</i></center>]

    %% --- Sistemas Externos ---
    SistemaPagos[<center><b>Sistema de Pagos</b><br/>(Ej: Stripe)<br/><i>[Sistema de Software Externo]</i></center>]
    SistemaEnvios[<center><b>Sistema de Envíos</b><br/>(Ej: FedEx API)<br/><i>[Sistema de Software Externo]</i></center>]
    ProveedorIdentidad[<center><b>Proveedor de Identidad</b><br/>(Ej: Auth0, EntraID)<br/><i>[Sistema de Software Externo]</i></center>]

    %% --- Relaciones ---
    ClienteWeb -- "Realiza Pedidos,<br/>Consulta Estado" --> SistemaPedidos
    ClienteMovil -- "Realiza Pedidos,<br/>Consulta Estado" --> SistemaPedidos
    Admin -- "Gestiona Productos,<br/>Revisa Pedidos,<br/>Configura Sistema" --> SistemaPedidos

    SistemaPedidos -- "Procesa Pagos" --> SistemaPagos
    SistemaPedidos -- "Gestiona Envíos" --> SistemaEnvios
    SistemaPedidos -- "Autentica Usuarios" --> ProveedorIdentidad

    %% Estilos
    style SistemaPedidos fill:#1168bd,stroke:#000,stroke-width:2px,color:#fff
    classDef persona fill:#08427b,stroke:#000,stroke-width:2px,color:#fff
    class ClienteWeb,ClienteMovil,Admin persona
    classDef sistemaExterno fill:#999,stroke:#000,stroke-width:2px,color:#fff
    class SistemaPagos,SistemaEnvios,ProveedorIdentidad sistemaExterno