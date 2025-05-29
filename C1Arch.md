# C1: Diagrama de Contexto del Sistema

[Volver a la documentación C4](readC4.md) | [Siguiente: C2](C2Arch.md)

## Descripción
Este diagrama muestra el contexto del sistema, incluyendo los usuarios y sistemas externos que interactúan con nuestro sistema de gestión de pedidos.

## Diagrama

```mermaid
graph TD
    %% --- Personas ---
    ClienteWeb["Cliente Web (Usuario)"]
    ClienteMovil["Cliente Móvil (Usuario)"]
    Admin["Administrador del Sistema (Usuario)"]

    %% --- Sistema de Software (El nuestro) ---
    SistemaPedidos["Sistema de Gestión de Pedidos Online - [Sistema de Software]"]

    %% --- Sistemas Externos ---
    SistemaPagos["Sistema de Pagos (Ej: Stripe) - [Sistema de Software Externo]"]
    SistemaEnvios["Sistema de Envíos (Ej: FedEx API) - [Sistema de Software Externo]"]
    ProveedorIdentidad["Proveedor de Identidad (Ej: Auth0, EntraID) - [Sistema de Software Externo]"]

    %% --- Relaciones ---
    ClienteWeb -- "Realiza Pedidos, Consulta Estado" --> SistemaPedidos
    ClienteMovil -- "Realiza Pedidos, Consulta Estado" --> SistemaPedidos
    Admin -- "Gestiona Productos, Revisa Pedidos, Configura Sistema" --> SistemaPedidos

    SistemaPedidos -- "Procesa Pagos" --> SistemaPagos
    SistemaPedidos -- "Gestiona Envíos" --> SistemaEnvios
    SistemaPedidos -- "Autentica Usuarios" --> ProveedorIdentidad

    %% Estilos
    style SistemaPedidos fill:#1168bd,stroke:#000,stroke-width:2px,color:#fff
    classDef persona fill:#08427b,stroke:#000,stroke-width:2px,color:#fff
    class ClienteWeb,ClienteMovil,Admin persona
    classDef sistemaExterno fill:#999,stroke:#000,stroke-width:2px,color:#fff
    class SistemaPagos,SistemaEnvios,ProveedorIdentidad sistemaExterno