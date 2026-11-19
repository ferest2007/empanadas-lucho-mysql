# Empanadas Lucho — Base de Datos Relacional (MySQL)

Base de datos relacional normalizada hasta Tercera Forma Normal (**3FN**) diseñada para gestionar la operación comercial, el inventario y la auditoría de caja de un punto de venta gastronómico multicanal.

## Modelo Entidad-Relación (EER)

![Diagrama Entidad-Relación](docs/diagrama_eer.png)

### Arquitectura de Entidades (8 Tablas)
* **Catálogos Maestros:** `Categoria`, `Metodo_Pago` y `Producto` (control de precio unitario, stock y disponibilidad).
* **Actores:** `Cliente` (datos de contacto y entrega) y `Usuario` (control de roles `Admin`, `Cajero`, `Cocina` y estado activo).
* **Operación y Auditoría:** `Venta` (canales `Local`, `Domicilio`, `Para_Llevar`), `Detalle_Venta` (con precio histórico y columna calculada `Subtotal`) y `Cierre_Caja` (conciliación entre recaudo en efectivo, medios digitales y cálculo del sistema).

## Características Técnicas Implementadas
* **Integridad Referencial y de Dominio:** Llaves foráneas (`FOREIGN KEY`), restricciones `ENUM`, precisión financiera `DECIMAL(10,2)` y columna almacenada `GENERATED ALWAYS AS (Cantidad * Precio_Unitario) STORED`.
* **Dataset de Validación:** 837 registros poblados (250 clientes, 30 productos, 150 ventas, 324 detalles de venta y 60 cierres de caja).
* **Analítica de Negocio (DQL):** Consultas con `INNER JOIN`, `LEFT JOIN`, agregaciones (`SUM`, `COUNT`, `AVG`) y clasificación condicional (`CASE WHEN`) para alertas de stock crítico, ranking de productos y auditoría de descuadres.
* **Automatización en Motor:**
  * **Vistas (`VIEW`):** `vw_catalogo_inventario`, `vw_ventas_diarias`, `vw_auditoria_cierres`.
  * **Disparadores (`TRIGGER`):** Validación previa de disponibilidad y stock, descuento automático de inventario, actualización del total de factura y cálculo de diferencias en cierre de caja.
  * **Procedimientos Almacenados (`PROCEDURE`):** `sp_registrar_venta_rapida` (con control transaccional `START TRANSACTION` / `COMMIT` / `ROLLBACK`), `sp_reabastecer_producto` y `sp_generar_cierre_caja`.

## Orden de Ejecución
1. Ejecutar `scripts/01_DDL_estructura.sql` en MySQL 8.0+ (Workbench o phpMyAdmin).
2. Ejecutar `scripts/02_DML_datos_prueba.sql` para poblar las tablas en orden jerárquico.
3. Ejecutar `scripts/04_objetos_programables.sql` para instalar vistas, triggers y procedimientos.
4. Ejecutar `scripts/03_DQL_consultas.sql` para obtener los reportes analíticos.
