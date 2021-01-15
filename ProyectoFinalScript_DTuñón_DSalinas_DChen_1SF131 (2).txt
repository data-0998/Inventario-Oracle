DROP TABLE OCUPACION CASCADE CONSTRAINT;
DROP TABLE CLIENTE CASCADE CONSTRAINT;
DROP TABLE SUCURSAL CASCADE CONSTRAINT;
DROP TABLE EMPLEADO CASCADE CONSTRAINT;
DROP TABLE ARTICULO CASCADE CONSTRAINT;
DROP TABLE BODEGA CASCADE CONSTRAINT;
DROP TABLE ARTICULO_BODEGA CASCADE CONSTRAINT;
DROP TABLE PROVEEDOR CASCADE CONSTRAINT;
DROP TABLE ARTICULO_PROVEEDOR CASCADE CONSTRAINT;
DROP TABLE CATEGORIA CASCADE CONSTRAINT;
DROP TABLE ARTICULO_CATEGORIA CASCADE CONSTRAINT;
DROP TABLE VENTA CASCADE CONSTRAINT;
DROP TABLE VENTA_ARTICULO CASCADE CONSTRAINT;
DROP TABLE PEDIDO CASCADE CONSTRAINT;
DROP TABLE PEDIDO_ARTICULO CASCADE CONSTRAINT;
DROP TABLE COMPRA CASCADE CONSTRAINT;
DROP TABLE COMPRA_articulo CASCADE CONSTRAINT;

DROP SEQUENCE Venta_seq;
DROP SEQUENCE SEQ_ARTICULO;
DROP SEQUENCE SEQ_PROVEEDOR;
DROP SEQUENCE seq_ID_Cliente;
DROP SEQUENCE seq_ID_Pedido;
DROP SEQUENCE SEQ_COMPRA;

drop view Vista_Categoria cascade constraint;
drop view Vista_RelacionId cascade constraint;
drop view Vista_Articulo cascade constraint;
drop view Vista_Bodega cascade constraint;
drop view Vista_Proveedor cascade constraint;
drop view Factura_Venta cascade constraint;
drop view Factura_Venta_Articulo cascade constraint;
drop view Ventas_Canceladas cascade constraint;
drop view FACT_PEDIDOS_EJECU cascade constraint;
drop view FACT_PEDIDOS_CANCE cascade constraint;
drop view REPARTIR_PEDIDOS cascade constraint;
drop view ARTICULOS_PEDIDOS cascade constraint;
drop view EMPLEADOS_SUCURSAL cascade constraint;
drop view EMPLEADOS_VENTAS cascade constraint;

-----------------------------CONFIGURACION----------------------------
set serveroutput on
set pagesize 100 linesize 300

-----------------------------TABLAS-----------------------------------
CREATE TABLE OCUPACION (
ID_Ocupacion NUMBER NOT NULL,
Puesto VARCHAR2(20) NOT NULL,
CONSTRAINT ID_Ocupacion_pk PRIMARY KEY (ID_Ocupacion));

CREATE TABLE CLIENTE (
ID_Cliente NUMBER NOT NULL,
Cedula VARCHAR(15) NOT NULL,
Nombre VARCHAR(15) NOT NULL,
Apellido VARCHAR(15) NOT NULL,
Celular NUMBER NOT NULL,
Lugar VARCHAR(40) NOT NULL,
Provincia VARCHAR(15) NOT NULL,
CONSTRAINT ID_Cliente_pk PRIMARY KEY (ID_Cliente),
CONSTRAINT ID_Cliente_uq UNIQUE (Cedula),
CONSTRAINT Celular_ck CHECK (Celular BETWEEN 60000000 AND 69999999));

CREATE TABLE SUCURSAL (
ID_Sucursal NUMBER NOT NULL,
Telefono NUMBER NOT NULL,
Lugar VARCHAR(40) NOT NULL,
Provincia VARCHAR(15) NOT NULL,
CONSTRAINT ID_Sucursal PRIMARY KEY (ID_SucursaL),
CONSTRAINT Telefono_ck CHECK (Telefono BETWEEN 2000000 AND 3999999 OR Telefono BETWEEN 5000000 AND 5999999));

CREATE TABLE EMPLEADO (
ID_Empleado NUMBER NOT NULL,
Nombre VARCHAR2(20) NOT NULL,
Apellido VARCHAR2(20) NOT NULL,
Cedula VARCHAR2(15) NOT NULL,
Fecha_Nacimiento DATE NOT NULL, 
Edad NUMBER NOT NULL,
ID_Sucursal NUMBER NOT NULL, 
ID_Ocupacion NUMBER NOT NULL,
CONSTRAINT ID_Empleado_pk PRIMARY KEY (ID_Empleado),
CONSTRAINT CedulaEmpleado_uq UNIQUE (Cedula),
CONSTRAINT ID_SucursalEmpleado_fk FOREIGN KEY (ID_Sucursal) REFERENCES SUCURSAL (ID_Sucursal), 
CONSTRAINT ID_Ocupacion_fk FOREIGN KEY (ID_Ocupacion) REFERENCES OCUPACION (ID_Ocupacion));

create table ARTICULO(
ID_ARTICULO NUMBER NOT NULL,
NOMBRE VARCHAR2(50) NOT NULL,
PRECIO_ARTICULO FLOAT NOT NULL,
ESTADO VARCHAR2(20) NOT NULL,
DESCRIPCION VARCHAR2(150),
CONSTRAINT PK_ARTICULO_ID_ARTICULO PRIMARY KEY(ID_ARTICULO),
CONSTRAINT Estado_Articulo_ck CHECK (Estado IN ('Disponible', 'No Disponible')));

create table BODEGA(
ID_BODEGA NUMBER NOT NULL,
CAPACIDAD NUMBER NOT NULL,
UBICACION VARCHAR2(30) NOT NULL,
CONSTRAINT PK_BODEGA_ID_BODEGA PRIMARY KEY(ID_BODEGA)
);

create table ARTICULO_BODEGA(
ID_ARTICULO NUMBER NOT NULL,
ID_BODEGA NUMBER NOT NULL,
CANTIDAD NUMBER NOT NULL,
CONSTRAINT PK_ARTICULO_BODEGA_ID PRIMARY KEY(ID_BODEGA,ID_ARTICULO),
CONSTRAINT FK_ARTICULO_BODEGA_ID_ARTI FOREIGN KEY (ID_ARTICULO) REFERENCES ARTICULO(ID_ARTICULO),
CONSTRAINT FK_ARTICULO_BODEGA_ID_BODE FOREIGN KEY (ID_BODEGA) REFERENCES BODEGA(ID_BODEGA)
);

create table PROVEEDOR(
ID_PROVEEDOR NUMBER NOT NULL,
NOMBRE VARCHAR2(50) NOT NULL,
LUGAR VARCHAR2(30) NOT NULL,
PROVINCIA VARCHAR2(30) NOT NULL,
CONSTRAINT PK_PROVEEDOR_ID_PROVEEDOR PRIMARY KEY(ID_PROVEEDOR)
);

create table COMPRA(
ID_COMPRA NUMBER NOT NULL,
ID_PROVEEDOR NUMBER NOT NULL,
SUBTOTAL FLOAT DEFAULT (0.0) NULL,
IMPUESTO FLOAT DEFAULT (0.0) NULL,
MONTOTOTAL FLOAT DEFAULT (0.0) NULL,
FECHA_COMPRA TIMESTAMP(0) NULL,
ID_Empleado NUMBER NOT NULL,
Estado VARCHAR2(20) DEFAULT ('Ejecucion') NULL,
CONSTRAINT PK_COMPRA PRIMARY KEY(ID_COMPRA),
CONSTRAINT FK_COMPRA_ID_EMPL FOREIGN KEY (ID_Empleado) REFERENCES EMPLEADO(ID_Empleado),
CONSTRAINT FK_COMPRA_ID_PROVEE FOREIGN KEY (ID_PROVEEDOR) REFERENCES PROVEEDOR(ID_PROVEEDOR),
CONSTRAINT Estado_Compra_ck CHECK (Estado IN ('Ejecucion', 'Terminada', 'Cancelado')));

create table COMPRA_ARTICULO(
ID_COMPRA NUMBER NOT NULL,
ID_ARTICULO NUMBER NOT NULL,
PRECIO_COMPRA FLOAT NOT NULL,
CANTIDAD NUMBER NOT NULL,
CONSTRAINT PK_COMPRA_ART PRIMARY KEY(ID_COMPRA,ID_ARTICULO),
CONSTRAINT FK_COMPRA_ART_ID_ARTI FOREIGN KEY (ID_ARTICULO) REFERENCES ARTICULO(ID_ARTICULO),
CONSTRAINT FK_COMPRA_ART_ID_COMPRA FOREIGN KEY (ID_COMPRA) REFERENCES COMPRA(ID_COMPRA));

create table ARTICULO_PROVEEDOR(
ID_ARTICULO NUMBER NOT NULL,
ID_PROVEEDOR NUMBER NOT NULL,
CANTIDAD_TOTAL NUMBER NOT NULL,
MONTO_TOTAL FLOAT NOT NULL,
CONSTRAINT PK_ARTICULO_PROVEEDOR_ID PRIMARY KEY(ID_PROVEEDOR,ID_ARTICULO),
CONSTRAINT FK_ARTICULO_PROVEEDOR_ID_ARTI FOREIGN KEY (ID_ARTICULO) REFERENCES ARTICULO(ID_ARTICULO),
CONSTRAINT FK_ARTICULO_PROVEEDOR_ID_PROV FOREIGN KEY (ID_PROVEEDOR) REFERENCES PROVEEDOR(ID_PROVEEDOR));

create table CATEGORIA(
ID_CATEGORIA NUMBER NOT NULL,
NOMBRE VARCHAR2(30) NOT NULL,
DESCRIPCION VARCHAR2(150) NOT NULL,
ABREVIATURA VARCHAR2(5) NOT NULL,
CONSTRAINT PK_PROVEEDOR_ID_CATEGORIA PRIMARY KEY(ID_CATEGORIA)
);

create table ARTICULO_CATEGORIA(
ID_ARTICULO NUMBER NOT NULL,
ID_CATEGORIA NUMBER NOT NULL,
CONSTRAINT PK_ARTICULO_CATEGORIA_ID PRIMARY KEY(ID_CATEGORIA,ID_ARTICULO),
CONSTRAINT FK_ARTICULO_CATEGORIA_ID_ARTI FOREIGN KEY (ID_ARTICULO) REFERENCES ARTICULO(ID_ARTICULO),
CONSTRAINT FK_ARTICULO_CATEGORIA_ID_CATE FOREIGN KEY (ID_CATEGORIA) REFERENCES CATEGORIA(ID_CATEGORIA)
);

CREATE TABLE VENTA (
ID_Venta NUMBER NOT NULL,
ID_Empleado NUMBER NOT NULL,
ID_Sucursal NUMBER NOT NULL,
ID_Cliente NUMBER NOT NULL,
Subtotal FLOAT DEFAULT (0.0) NULL,
Impuesto FLOAT DEFAULT (0.0) NULL,
MontoTotal FLOAT DEFAULT (0.0) NULL,
Estado VARCHAR2(20) DEFAULT ('Ejecucion') NULL,
Fecha_Venta TIMESTAMP(0) NOT NULL,
CONSTRAINT ID_Venta_pk PRIMARY KEY (ID_Venta),
CONSTRAINT ID_EmpleadoVenta_fk FOREIGN KEY (ID_Empleado) REFERENCES EMPLEADO (ID_Empleado),
CONSTRAINT ID_SucursalVenta_fk FOREIGN KEY (ID_Sucursal) REFERENCES SUCURSAL (ID_Sucursal),
CONSTRAINT Estado_Venta_ck CHECK (Estado IN ('Ejecucion', 'Terminada', 'Cancelado')),
CONSTRAINT ID_ClienteVenta_fk FOREIGN KEY (ID_Cliente) REFERENCES CLIENTE (ID_Cliente));

CREATE TABLE VENTA_ARTICULO (
ID_Venta NUMBER NOT NULL,
ID_Articulo NUMBER NOT NULL,
Cantidad INT NOT NULL,
CONSTRAINT ID_VentaArticulo_pk PRIMARY KEY (ID_Venta, ID_Articulo),
CONSTRAINT ID_VentaA_fk FOREIGN KEY (ID_Venta) REFERENCES VENTA (ID_Venta),
CONSTRAINT ID_ArticuloV_fk FOREIGN KEY (ID_Articulo) REFERENCES ARTICULO (ID_Articulo));

CREATE TABLE PEDIDO (
ID_Pedido NUMBER NOT NULL,
ID_Cliente NUMBER NOT NULL,
ID_SucursaL NUMBER NOT NULL,
Descripcion VARCHAR(50) NOT NULL,
Fecha_Insercion TIMESTAMP(0) NOT NULL,
Subtotal FLOAT DEFAULT (0.0) NOT NULL,
Impuesto NUMBER(8,2) DEFAULT (0.0) NOT NULL,
MontoTotal FLOAT DEFAULT (0.0) NOT NULL,
Fecha_Entrega DATE NULL,
Estado VARCHAR2(20) DEFAULT ('Ejecucion') NOT NULL,
CONSTRAINT ID_Pedido_pk PRIMARY KEY (ID_Pedido),
CONSTRAINT ID_Cliente_Pedido_fk FOREIGN KEY (ID_Cliente) REFERENCES CLIENTE (ID_Cliente),
CONSTRAINT ID_Sucursal_Pedido_fk FOREIGN KEY (ID_SUCURSAL) REFERENCES SUCURSAL (ID_SUCURSAL),
CONSTRAINT Estado_Pedido_ck CHECK (Estado IN ('Ejecucion', 'Terminada', 'Cancelado')));

CREATE TABLE PEDIDO_ARTICULO (
ID_Pedido NUMBER NOT NULL,
ID_Articulo NUMBER NOT NULL,
Cantidad NUMBER NOT NULL,
CONSTRAINT Pedido_Articulo_pk PRIMARY KEY (ID_Articulo, ID_Pedido),
CONSTRAINT ID_Articulo_PedidoArticulo_fk FOREIGN KEY (ID_Articulo) REFERENCES ARTICULO (ID_Articulo),
CONSTRAINT ID_Pedido_PedidoArticulo_fk FOREIGN KEY (ID_Pedido) REFERENCES PEDIDO (ID_Pedido));

-----------------------------SECUENCIAS PARA TABLAS------------------------------------------------
CREATE SEQUENCE Venta_seq
    MAXVALUE 99999
    MINVALUE 1
    START WITH 1
    INCREMENT BY 1
    CACHE 20;

CREATE SEQUENCE SEQ_ARTICULO
    MAXVALUE 99999
    MINVALUE 1
    START WITH 1
    INCREMENT BY 1
    CACHE 20;

CREATE SEQUENCE SEQ_PROVEEDOR
    MAXVALUE 99999
    MINVALUE 1
    START WITH 1
    INCREMENT BY 1
    CACHE 20;

CREATE SEQUENCE seq_ID_Cliente
    MAXVALUE 99999
    MINVALUE 1
    START WITH 1
    INCREMENT BY 1
    CACHE 20;


CREATE SEQUENCE seq_ID_Pedido
    MAXVALUE 99999
    MINVALUE 1
    START WITH 1
    INCREMENT BY 1
    CACHE 20;


CREATE SEQUENCE SEQ_COMPRA
    MAXVALUE 99999
    MINVALUE 1
    START WITH 1
    INCREMENT BY 1
    CACHE 20;

-----------------------------PROCEDIMIENTOS PARA LAS INSERCIONES-----------------------------------
-----OCUPACION-----
CREATE OR REPLACE PROCEDURE CrearOcupacion (
    p_ID_Ocupacion IN OCUPACION.ID_Ocupacion%TYPE,
    p_puesto IN OCUPACION.Puesto%TYPE,
    p_mensaje OUT VARCHAR2)AS
BEGIN
    p_mensaje:='Ocupación creada con éxito';
    INSERT INTO OCUPACION VALUES (p_ID_Ocupacion, p_puesto);
EXCEPTION
    WHEN dup_val_on_index THEN
        p_mensaje:='Registro duplicado';
    WHEN others THEN
        p_mensaje:='Error desconocido';
COMMIT;
END CrearOcupacion;
/
--Anonimos: 1-Gerente, 2-Vendedor, 3-Seguridad, 4-Limpieza--
DECLARE
    v_ID_Ocupacion OCUPACION.ID_Ocupacion%TYPE:=1;
    v_Puesto OCUPACION.Puesto%TYPE:='Gerente';
    v_mensaje VARCHAR2(50);
BEGIN
    CrearOcupacion(v_ID_Ocupacion, v_Puesto, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Ocupacion OCUPACION.ID_Ocupacion%TYPE:=2;
    v_Puesto OCUPACION.Puesto%TYPE:='Vendedor';
    v_mensaje VARCHAR2(50);
BEGIN
    CrearOcupacion(v_ID_Ocupacion, v_Puesto, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Ocupacion OCUPACION.ID_Ocupacion%TYPE:=3;
    v_Puesto OCUPACION.Puesto%TYPE:='Seguridad';
    v_mensaje VARCHAR2(50);
BEGIN
    CrearOcupacion(v_ID_Ocupacion, v_Puesto, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Ocupacion OCUPACION.ID_Ocupacion%TYPE:=4;
    v_Puesto OCUPACION.Puesto%TYPE:='Limpieza';
    v_mensaje VARCHAR2(50);
BEGIN
    CrearOcupacion(v_ID_Ocupacion, v_Puesto, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

-----CLIENTE-----
CREATE OR REPLACE PROCEDURE crearCliente (
    p_Cedula CLIENTE.Cedula%TYPE,
    p_Nombre CLIENTE.Nombre%TYPE,
    p_Apellido CLIENTE.Apellido%TYPE,
    p_Celular CLIENTE.Celular%TYPE,
    p_Lugar CLIENTE.Lugar%TYPE,
    p_Provincia CLIENTE.Provincia%TYPE,
    p_Mensaje OUT VARCHAR2
    ) AS

BEGIN
    INSERT INTO CLIENTE (ID_Cliente, Cedula, Nombre, Apellido, Celular, Lugar, Provincia)
    VALUES (seq_ID_Cliente.nextval, p_Cedula, p_Nombre, p_Apellido, p_Celular, p_Lugar, p_Provincia);
    p_Mensaje := 'Registro creado con éxito.';
    
EXCEPTION
    WHEN DUP_VAL_ON_INDEX THEN
        p_Mensaje := 'El registro ya existe';
    WHEN OTHERS THEN
        p_Mensaje :='Error desconocido';

COMMIT;
END crearCliente;
/

--anonimo--
DECLARE
    v_Cedula CLIENTE.Cedula%TYPE := '8-934-2092';
    v_Nombre CLIENTE.Nombre%TYPE := 'Lara';
    v_Apellido CLIENTE.Apellido%TYPE := 'Jean';
    v_Celular CLIENTE.Celular%TYPE := 62806565;
    v_Lugar CLIENTE.Lugar%TYPE := 'Transístmica, entrando por Lung Fung';
    v_Provincia CLIENTE.Provincia%TYPE := 'Panamá';
    v_Mensaje VARCHAR2(35);

BEGIN
    crearCliente (v_Cedula, v_Nombre, v_Apellido, v_Celular, v_Lugar, v_Provincia, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_Cedula CLIENTE.Cedula%TYPE := '8-938-468';
    v_Nombre CLIENTE.Nombre%TYPE := 'Peter';
    v_Apellido CLIENTE.Apellido%TYPE := 'Kavinsky';
    v_Celular CLIENTE.Celular%TYPE := 63907676;
    v_Lugar CLIENTE.Lugar%TYPE := 'La Siesta, Calle 13';
    v_Provincia CLIENTE.Provincia%TYPE := 'Panamá';
    v_Mensaje VARCHAR2(35);

BEGIN
    crearCliente (v_Cedula, v_Nombre, v_Apellido, v_Celular, v_Lugar, v_Provincia, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_Cedula CLIENTE.Cedula%TYPE := '8-942-523';
    v_Nombre CLIENTE.Nombre%TYPE := 'Josh';
    v_Apellido CLIENTE.Apellido%TYPE := 'Sanderson';
    v_Celular CLIENTE.Celular%TYPE := 64008787;
    v_Lugar CLIENTE.Lugar%TYPE := 'Chorrera, El Coco';
    v_Provincia CLIENTE.Provincia%TYPE := 'Panamá';
    v_Mensaje VARCHAR2(35);

BEGIN
    crearCliente (v_Cedula, v_Nombre, v_Apellido, v_Celular, v_Lugar, v_Provincia, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_Cedula CLIENTE.Cedula%TYPE := '4-523-9875';
    v_Nombre CLIENTE.Nombre%TYPE := 'John';
    v_Apellido CLIENTE.Apellido%TYPE := 'Ambrose';
    v_Celular CLIENTE.Celular%TYPE := 65109898;
    v_Lugar CLIENTE.Lugar%TYPE := 'Vía España, entrando por el IPA';
    v_Provincia CLIENTE.Provincia%TYPE := 'Panamá';
    v_Mensaje VARCHAR2(35);

BEGIN
    crearCliente (v_Cedula, v_Nombre, v_Apellido, v_Celular, v_Lugar, v_Provincia, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_Cedula CLIENTE.Cedula%TYPE := '4-268-478';
    v_Nombre CLIENTE.Nombre%TYPE := 'Margot';
    v_Apellido CLIENTE.Apellido%TYPE := 'Jean';
    v_Celular CLIENTE.Celular%TYPE := 66201919;
    v_Lugar CLIENTE.Lugar%TYPE := 'Juan Díaz, Concepción';
    v_Provincia CLIENTE.Provincia%TYPE := 'Panamá';
    v_Mensaje VARCHAR2(35);

BEGIN
    crearCliente (v_Cedula, v_Nombre, v_Apellido, v_Celular, v_Lugar, v_Provincia, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

-----SUCURSAL---
CREATE OR REPLACE PROCEDURE crearSucursal (
    p_ID_Sucursal SUCURSAL.ID_Sucursal%TYPE,
    p_Telefono SUCURSAL.Telefono%TYPE,
    p_Lugar SUCURSAL.Lugar%TYPE,
    p_Provincia SUCURSAL.Provincia%TYPE,
    p_Mensaje OUT VARCHAR2
) AS

BEGIN
    INSERT INTO SUCURSAL (ID_Sucursal, Telefono, Lugar, Provincia)
    VALUES (p_ID_Sucursal, p_Telefono, p_Lugar, p_Provincia);
    p_Mensaje := 'Registro creado con éxito.';
    
EXCEPTION
    WHEN DUP_VAL_ON_INDEX THEN
        p_Mensaje := 'El registro ya existe.';
    WHEN OTHERS THEN
        p_Mensaje :='Error desconocido.';

COMMIT;
END crearSucursal;
/

--anonimos---
DECLARE
    v_ID_Sucursal SUCURSAL.ID_Sucursal%TYPE := 1;
    v_Telefono SUCURSAL.Telefono%TYPE := 3912020;
    v_Lugar SUCURSAL.Lugar%TYPE := 'Centro Comercial el Dorado';
    v_Provincia SUCURSAL.Provincia%TYPE := 'Panamá';
    v_Mensaje VARCHAR2(35);

BEGIN
    crearSucursal (v_ID_Sucursal, v_Telefono, v_Lugar, v_Provincia, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_ID_Sucursal SUCURSAL.ID_Sucursal%TYPE := 2;
    v_Telefono SUCURSAL.Telefono%TYPE := 3912021;
    v_Lugar SUCURSAL.Lugar%TYPE := 'Plaza Altos de Tocumen';
    v_Provincia SUCURSAL.Provincia%TYPE := 'Panamá';
    v_Mensaje VARCHAR2(35);

BEGIN
    crearSucursal (v_ID_Sucursal, v_Telefono, v_Lugar, v_Provincia, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_ID_Sucursal SUCURSAL.ID_Sucursal%TYPE := 3;
    v_Telefono SUCURSAL.Telefono%TYPE := 3912022;
    v_Lugar SUCURSAL.Lugar%TYPE := 'Arraijan Town Center';
    v_Provincia SUCURSAL.Provincia%TYPE := 'Panamá';
    v_Mensaje VARCHAR2(35);

BEGIN
    crearSucursal (v_ID_Sucursal, v_Telefono, v_Lugar, v_Provincia, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

-----EMPLEADOS-----
CREATE OR REPLACE FUNCTION CalcularEdad (v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE) RETURN NUMBER AS
    v_Edad NUMBER;
BEGIN
    v_Edad:=TRUNC(MONTHS_BETWEEN(sysdate, v_Fecha_Nacimiento)/12);
    RETURN v_Edad;
END CalcularEdad;
/

CREATE OR REPLACE PROCEDURE RegistrarEmpleado(
    p_ID_Empleado IN EMPLEADO.ID_Empleado%TYPE,
    p_Nombre IN EMPLEADO.Nombre%TYPE,
    p_Apellido IN EMPLEADO.Apellido%TYPE,
    p_Cedula IN EMPLEADO.Cedula%TYPE,
    p_Fecha_Nacimiento IN EMPLEADO.Fecha_Nacimiento%TYPE,
    p_ID_Sucursal IN EMPLEADO.ID_Sucursal%TYPE,
    p_ID_Ocupacion IN EMPLEADO.ID_Ocupacion%TYPE,
    p_mensaje OUT VARCHAR2)AS 
    v_Edad EMPLEADO.Edad%TYPE := CalcularEdad(p_Fecha_Nacimiento);
BEGIN
    p_mensaje:='Empleado registrado con éxito';
    INSERT INTO EMPLEADO VALUES (p_ID_Empleado, p_Nombre, p_Apellido, p_Cedula, p_Fecha_Nacimiento, v_Edad, p_ID_Sucursal, p_ID_Ocupacion);
EXCEPTION
    WHEN dup_val_on_index THEN
        p_mensaje:='Registro duplicado';
    WHEN others THEN
        p_mensaje:='Error desconocido';
COMMIT;
END RegistrarEmpleado;
/
--anonimo sucursal 1--
DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=1001;
    v_Nombre EMPLEADO.Nombre%TYPE:='Diana';
    v_Apellido EMPLEADO.Apellido%TYPE:='Chen';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-666-6666';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('10-09-1998','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=1;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=1;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=1002;
    v_Nombre EMPLEADO.Nombre%TYPE:='Juana';
    v_Apellido EMPLEADO.Apellido%TYPE:='Perez';
    v_Cedula EMPLEADO.Cedula%TYPE:='N-333-4444';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('11-02-1989','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=1;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=2;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=1012;
    v_Nombre EMPLEADO.Nombre%TYPE:='Jose';
    v_Apellido EMPLEADO.Apellido%TYPE:='Quintero';
    v_Cedula EMPLEADO.Cedula%TYPE:='20-555-555';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('01-04-1985','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=1;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=2;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=1003;
    v_Nombre EMPLEADO.Nombre%TYPE:='Luis';
    v_Apellido EMPLEADO.Apellido%TYPE:='Prado';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-888-888';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('09-07-1990','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=1;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=3;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=1004;
    v_Nombre EMPLEADO.Nombre%TYPE:='Maria';
    v_Apellido EMPLEADO.Apellido%TYPE:='Quiros';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-999-999';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('23-09-1993','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=1;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=4;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/
--anonimo sucursal 2--
DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=2001;
    v_Nombre EMPLEADO.Nombre%TYPE:='Dalila';
    v_Apellido EMPLEADO.Apellido%TYPE:='Salinas';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-111-111';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('12-08-1998','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=2;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=1;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=2002;
    v_Nombre EMPLEADO.Nombre%TYPE:='Genesis';
    v_Apellido EMPLEADO.Apellido%TYPE:='Pertuz';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-234-234';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('19-05-1999','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=2;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=2;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=2012;
    v_Nombre EMPLEADO.Nombre%TYPE:='Venus';
    v_Apellido EMPLEADO.Apellido%TYPE:='Citeres';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-123-1232';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('25-01-1997','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=2;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=2;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=2003;
    v_Nombre EMPLEADO.Nombre%TYPE:='Ana';
    v_Apellido EMPLEADO.Apellido%TYPE:='Melendez';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-678-6789';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('21-09-1995','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=2;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=3;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=2004;
    v_Nombre EMPLEADO.Nombre%TYPE:='Andres';
    v_Apellido EMPLEADO.Apellido%TYPE:='Navarro';
    v_Cedula EMPLEADO.Cedula%TYPE:='9-112-112';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('27-07-1979','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=2;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=4;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/
--anonimo sucursal 3--
DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=3001;
    v_Nombre EMPLEADO.Nombre%TYPE:='Diego';
    v_Apellido EMPLEADO.Apellido%TYPE:='Tuñon';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-222-2222';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('19-12-1992','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=3;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=1;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=3002;
    v_Nombre EMPLEADO.Nombre%TYPE:='Felipe';
    v_Apellido EMPLEADO.Apellido%TYPE:='Loo';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-345-345';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('04-09-1996','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=3;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=2;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=3012;
    v_Nombre EMPLEADO.Nombre%TYPE:='Lorena';
    v_Apellido EMPLEADO.Apellido%TYPE:='Gonzales';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-989-989';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('05-11-1992','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=3;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=2;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=3003;
    v_Nombre EMPLEADO.Nombre%TYPE:='Maykol';
    v_Apellido EMPLEADO.Apellido%TYPE:='Chang';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-321-3211';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('27-12-1989','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=3;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=3;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

DECLARE
    v_ID_Empleado EMPLEADO.ID_Empleado%TYPE:=3004;
    v_Nombre EMPLEADO.Nombre%TYPE:='Juan';
    v_Apellido EMPLEADO.Apellido%TYPE:='Navarro';
    v_Cedula EMPLEADO.Cedula%TYPE:='8-444-9999';
    v_Fecha_Nacimiento EMPLEADO.Fecha_Nacimiento%TYPE:=TO_DATE('04-10-1982','DD-MM-YYYY');
    v_ID_Sucursal EMPLEADO.ID_Sucursal%TYPE:=3;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE:=4;
    p_mensaje VARCHAR2(50);
BEGIN
    RegistrarEmpleado(v_ID_Empleado, v_Nombre, v_Apellido, v_Cedula, v_Fecha_Nacimiento, v_ID_Sucursal, v_ID_Ocupacion, p_mensaje);
    DBMS_OUTPUT.PUT_LINE(p_mensaje);
END;
/

-----ARTICULO-----
create or replace procedure INSERTAR_ARTICULO(
    P_NOMBRE ARTICULO.NOMBRE%TYPE,
    P_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE,
    P_ESTADO ARTICULO.ESTADO%TYPE,
    P_DESCRIPCION ARTICULO.DESCRIPCION%TYPE,
    P_MENSAJE OUT VARCHAR2) IS
    BEGIN
        P_MENSAJE := 'Se ha insertado';
        INSERT INTO ARTICULO VALUES(SEQ_ARTICULO.nextval,P_NOMBRE,P_PRECIO_ARTICULO,P_ESTADO,P_DESCRIPCION);
    EXCEPTION
      when dup_val_on_index then
        P_MENSAJE := 'Indice Repetido';
    when others then
        P_MENSAJE := 'Error desconocido';
COMMIT;
END INSERTAR_ARTICULO;
/

--anonimos--
DECLARE
    V_NOMBRE ARTICULO.NOMBRE%TYPE:= 'Manguera Whirpool MGWP42';
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE:= 2.50;
    V_ESTADO ARTICULO.ESTADO%TYPE:= 'No Disponible';
    V_DESCRIPCION ARTICULO.DESCRIPCION%TYPE:= 'Manguera de 2 metros';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO(V_NOMBRE,V_PRECIO_ARTICULO,V_ESTADO,V_DESCRIPCION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/
DECLARE
    V_NOMBRE ARTICULO.NOMBRE%TYPE:= 'Manguera Whirpool MGWP55';
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE:= 5.50;
    V_ESTADO ARTICULO.ESTADO%TYPE:= 'No Disponible';
    V_DESCRIPCION ARTICULO.DESCRIPCION%TYPE:= 'Manguera de 5 metros';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO(V_NOMBRE,V_PRECIO_ARTICULO,V_ESTADO,V_DESCRIPCION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/
DECLARE
    V_NOMBRE ARTICULO.NOMBRE%TYPE:= 'Pintura Heicer Verde HCVD2';
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE:= 10.00;
    V_ESTADO ARTICULO.ESTADO%TYPE:= 'No Disponible';
    V_DESCRIPCION ARTICULO.DESCRIPCION%TYPE:= 'Pintura verde de 1 litro marca Heicer';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO(V_NOMBRE,V_PRECIO_ARTICULO,V_ESTADO,V_DESCRIPCION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/
DECLARE
    V_NOMBRE ARTICULO.NOMBRE%TYPE:= 'Pintura Heicer AZUL HCAZ5';
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE:= 10.00;
    V_ESTADO ARTICULO.ESTADO%TYPE:= 'No Disponible';
    V_DESCRIPCION ARTICULO.DESCRIPCION%TYPE:= 'Pintura azul de 1 litro marca Heicer';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO(V_NOMBRE,V_PRECIO_ARTICULO,V_ESTADO,V_DESCRIPCION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/
DECLARE
    V_NOMBRE ARTICULO.NOMBRE%TYPE:= 'Pintura Whiteheim Blanca WHBA3';
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE:= 12.00;
    V_ESTADO ARTICULO.ESTADO%TYPE:= 'No Disponible';
    V_DESCRIPCION ARTICULO.DESCRIPCION%TYPE:= 'Pintura blanca de 1.5 litro marca Whiteheim';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO(V_NOMBRE,V_PRECIO_ARTICULO,V_ESTADO,V_DESCRIPCION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_NOMBRE ARTICULO.NOMBRE%TYPE:= 'Pintura Whiteheim Naranjita WHNJ7';
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE:= 12.00;
    V_ESTADO ARTICULO.ESTADO%TYPE:= 'No Disponible';
    V_DESCRIPCION ARTICULO.DESCRIPCION%TYPE:= 'Pintura azul de 1.5 litro marca Whiteheim';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO(V_NOMBRE,V_PRECIO_ARTICULO,V_ESTADO,V_DESCRIPCION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_NOMBRE ARTICULO.NOMBRE%TYPE:= 'Regadera Automatica Whirpool RGWP';
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE:= 20.00;
    V_ESTADO ARTICULO.ESTADO%TYPE:= 'No Disponible';
    V_DESCRIPCION ARTICULO.DESCRIPCION%TYPE:= 'Regadera Automatica para  plantas marca Whirpool, 5plg de ancho 10 de alto';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO(V_NOMBRE,V_PRECIO_ARTICULO,V_ESTADO,V_DESCRIPCION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_NOMBRE ARTICULO.NOMBRE%TYPE:= 'Estufa Whirpool ETWH4';
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE:= 200.00;
    V_ESTADO ARTICULO.ESTADO%TYPE:= 'No Disponible';
    V_DESCRIPCION ARTICULO.DESCRIPCION%TYPE:= 'Estufa Whirepool de 6 fogones con horno';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO(V_NOMBRE,V_PRECIO_ARTICULO,V_ESTADO,V_DESCRIPCION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_NOMBRE ARTICULO.NOMBRE%TYPE:= 'Lavadora Whirpool LVWH1';
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE:= 150.00;
    V_ESTADO ARTICULO.ESTADO%TYPE:= 'No Disponible';
    V_DESCRIPCION ARTICULO.DESCRIPCION%TYPE:= 'Lavadora Whirepool de 5 velocidades';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO(V_NOMBRE,V_PRECIO_ARTICULO,V_ESTADO,V_DESCRIPCION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_NOMBRE ARTICULO.NOMBRE%TYPE:= 'Secadodra Whirpool SCWH1';
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE:= 150.00;
    V_ESTADO ARTICULO.ESTADO%TYPE:= 'No Disponible';
    V_DESCRIPCION ARTICULO.DESCRIPCION%TYPE:= 'Secadora Whirepool clasica modelo basico';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO(V_NOMBRE,V_PRECIO_ARTICULO,V_ESTADO,V_DESCRIPCION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

-----BODEGA----
create or replace procedure INSERTAR_BODEGA(
    P_ID_BODEGA BODEGA.ID_BODEGA%TYPE,
    P_CAPACIDAD BODEGA.CAPACIDAD%TYPE,
    P_UBICACION BODEGA.UBICACION%TYPE,
    P_MENSAJE OUT VARCHAR2) IS
    BEGIN
        P_MENSAJE := 'Se ha insertado';
        INSERT INTO BODEGA VALUES(P_ID_BODEGA,P_CAPACIDAD,P_UBICACION);
    EXCEPTION
      when dup_val_on_index then
        P_MENSAJE := 'Indice Repetido';
    when others then
        P_MENSAJE := 'Error desconocido';
COMMIT;
END INSERTAR_BODEGA;
/

---anonimos---
DECLARE
    V_ID_BODEGA BODEGA.ID_BODEGA%TYPE := 1;
    V_CAPACIDAD BODEGA.CAPACIDAD%TYPE := 40;
    V_UBICACION BODEGA.UBICACION%TYPE := 'Tumba Muerto';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_BODEGA(V_ID_BODEGA,V_CAPACIDAD,V_UBICACION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_BODEGA BODEGA.ID_BODEGA%TYPE := 2;
    V_CAPACIDAD BODEGA.CAPACIDAD%TYPE := 40;
    V_UBICACION BODEGA.UBICACION%TYPE := 'Tocumen';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_BODEGA(V_ID_BODEGA,V_CAPACIDAD,V_UBICACION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_BODEGA BODEGA.ID_BODEGA%TYPE := 3;
    V_CAPACIDAD BODEGA.CAPACIDAD%TYPE := 40;
    V_UBICACION BODEGA.UBICACION%TYPE := 'Arraijan';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_BODEGA(V_ID_BODEGA,V_CAPACIDAD,V_UBICACION,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

----PROVEEDOR--
create or replace procedure INSERTAR_PROVEEDOR(
    P_NOMBRE PROVEEDOR.NOMBRE%TYPE,
    P_LUGAR PROVEEDOR.LUGAR%TYPE,
    P_PROVINCIA PROVEEDOR.PROVINCIA%TYPE,
    P_MENSAJE OUT VARCHAR2) IS
    BEGIN
        P_MENSAJE := 'Se ha insertado';
        INSERT INTO PROVEEDOR VALUES(SEQ_PROVEEDOR.nextval,P_NOMBRE,P_LUGAR,P_PROVINCIA);
    EXCEPTION
      when dup_val_on_index then
        P_MENSAJE := 'Indice Repetido';
    when others then
        P_MENSAJE := 'Error desconocido';
COMMIT;
END INSERTAR_PROVEEDOR;
/
--anonimos--
DECLARE
    V_NOMBRE PROVEEDOR.NOMBRE%TYPE:= 'Distribuidora Pacífico' ;
    V_LUGAR PROVEEDOR.LUGAR%TYPE:= 'Area Industrial';
    V_PROVINCIA PROVEEDOR.PROVINCIA%TYPE:= 'Panama';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_PROVEEDOR(V_NOMBRE,V_LUGAR,V_PROVINCIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_NOMBRE PROVEEDOR.NOMBRE%TYPE:= 'El Cocodrilo S.A.' ;
    V_LUGAR PROVEEDOR.LUGAR%TYPE:= 'Area Industrial';
    V_PROVINCIA PROVEEDOR.PROVINCIA%TYPE:= 'Panama';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_PROVEEDOR(V_NOMBRE,V_LUGAR,V_PROVINCIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_NOMBRE PROVEEDOR.NOMBRE%TYPE:= 'Distribuidora Cheritz' ;
    V_LUGAR PROVEEDOR.LUGAR%TYPE:= 'El Coco';
    V_PROVINCIA PROVEEDOR.PROVINCIA%TYPE:= 'Chorrera';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_PROVEEDOR(V_NOMBRE,V_LUGAR,V_PROVINCIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

----------------------- ARTICULO_ BODEGA----------
create or replace procedure INSERTAR_ARTICULO_BODEGA(
    P_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE,
    P_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE,
    P_MENSAJE OUT VARCHAR2) IS
    BEGIN
        P_MENSAJE := 'Se ha insertado';
        INSERT INTO ARTICULO_BODEGA VALUES(P_ID_ARTICULO,P_ID_BODEGA,0);
    EXCEPTION
      when dup_val_on_index then
        P_MENSAJE := 'Indice Repetido';
    when others then
        P_MENSAJE := 'Error desconocido';
COMMIT;
END INSERTAR_ARTICULO_BODEGA;
/
--anonimos--
DECLARE
    V_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE:= 1;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE:= 1;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_BODEGA(V_ID_ARTICULO,V_ID_BODEGA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE:= 2;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE:= 1;

    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_BODEGA(V_ID_ARTICULO,V_ID_BODEGA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE:= 3;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE:= 2;

    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_BODEGA(V_ID_ARTICULO,V_ID_BODEGA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE:= 4;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE:= 2;
 
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_BODEGA(V_ID_ARTICULO,V_ID_BODEGA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE:= 5;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE:= 2;
 
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_BODEGA(V_ID_ARTICULO,V_ID_BODEGA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE:= 6;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE:= 2;

    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_BODEGA(V_ID_ARTICULO,V_ID_BODEGA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE:= 7;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE:= 1;

    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_BODEGA(V_ID_ARTICULO,V_ID_BODEGA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE:= 8;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE:= 3;
 
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_BODEGA(V_ID_ARTICULO,V_ID_BODEGA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE:= 9;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE:= 3;

    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_BODEGA(V_ID_ARTICULO,V_ID_BODEGA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_BODEGA.ID_ARTICULO%TYPE:= 10;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE:= 3;

    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_BODEGA(V_ID_ARTICULO,V_ID_BODEGA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/




---ARTICULO_PROVEEDOR----
create or replace procedure INSERTAR_ARTICULO_PROVEEDOR(
    P_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE,
    P_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE,
    P_MENSAJE OUT VARCHAR2) IS
    BEGIN
        P_MENSAJE := 'Se ha insertado';
        INSERT INTO ARTICULO_PROVEEDOR VALUES(P_ID_ARTICULO,P_ID_PROVEEDOR, 0, 0);
    EXCEPTION
      when dup_val_on_index then
        P_MENSAJE := 'Indice Repetido';
    when others then
        P_MENSAJE := 'Error desconocido';
COMMIT;
END INSERTAR_ARTICULO_PROVEEDOR;
/

--ANONIMOS----
DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 1;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=1 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 2;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=1 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 3;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=2 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 4;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=2 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 5;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=3 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 6;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=3 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 7;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=1 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 8;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=1 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 9;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=1 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 10;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=1 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 10;
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE:=2 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_PROVEEDOR(V_ID_ARTICULO,V_ID_PROVEEDOR,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

----INSERTAR_CATEGORIA----
create or replace procedure INSERTAR_CATEGORIA(
    P_ID_CATEGORIA CATEGORIA.ID_CATEGORIA%TYPE,
    P_NOMBRE CATEGORIA.NOMBRE%TYPE,
    P_DESCRIPCION CATEGORIA.DESCRIPCION%TYPE,
    P_ABREVIATURA CATEGORIA.ABREVIATURA%TYPE,
    P_MENSAJE OUT VARCHAR2) IS
    BEGIN
        P_MENSAJE := 'Se ha insertado';
        INSERT INTO CATEGORIA VALUES(P_ID_CATEGORIA,P_NOMBRE,P_DESCRIPCION,P_ABREVIATURA);
    EXCEPTION
      when dup_val_on_index then
        P_MENSAJE := 'Indice Repetido';
    when others then
        P_MENSAJE := 'Error desconocido';
COMMIT;
END INSERTAR_CATEGORIA;
/

----ANONIMOS----
DECLARE
    V_ID_CATEGORIA CATEGORIA.ID_CATEGORIA%TYPE:= 1;
    V_NOMBRE CATEGORIA.NOMBRE%TYPE:= 'Jardin';
    V_DESCRIPCION CATEGORIA.DESCRIPCION%TYPE := 'Todos los productos relacionados el mantenimiento y decoracion de jardines';
    V_ABREVIATURA CATEGORIA.ABREVIATURA%TYPE := 'JD';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_CATEGORIA(V_ID_CATEGORIA,V_NOMBRE,V_DESCRIPCION,V_ABREVIATURA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_CATEGORIA CATEGORIA.ID_CATEGORIA%TYPE:= 2;
    V_NOMBRE CATEGORIA.NOMBRE%TYPE:= 'Pintura';
    V_DESCRIPCION CATEGORIA.DESCRIPCION%TYPE := 'Todos  los prodcutos relacionadoss con pintura';
    V_ABREVIATURA CATEGORIA.ABREVIATURA%TYPE := 'PT';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_CATEGORIA(V_ID_CATEGORIA,V_NOMBRE,V_DESCRIPCION,V_ABREVIATURA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/


DECLARE
    V_ID_CATEGORIA CATEGORIA.ID_CATEGORIA%TYPE:= 3;
    V_NOMBRE CATEGORIA.NOMBRE%TYPE:= 'Linea Blanca';
    V_DESCRIPCION CATEGORIA.DESCRIPCION%TYPE := 'Todos  los prodcutos de la Linea Blanca';
    V_ABREVIATURA CATEGORIA.ABREVIATURA%TYPE := 'LB';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_CATEGORIA(V_ID_CATEGORIA,V_NOMBRE,V_DESCRIPCION,V_ABREVIATURA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_CATEGORIA CATEGORIA.ID_CATEGORIA%TYPE:= 4;
    V_NOMBRE CATEGORIA.NOMBRE%TYPE:= 'Cocina';
    V_DESCRIPCION CATEGORIA.DESCRIPCION%TYPE := 'Todos los productos relacionados con el mantenimiento y utencilios de cocina';
    V_ABREVIATURA CATEGORIA.ABREVIATURA%TYPE := 'CN';
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_CATEGORIA(V_ID_CATEGORIA,V_NOMBRE,V_DESCRIPCION,V_ABREVIATURA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

-----ARTICULO_CATEGORIA--------
create or replace procedure INSERTAR_ARTICULO_CATEGORIA(
    P_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE,
    P_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE,
    P_MENSAJE OUT VARCHAR2) IS
    BEGIN
        P_MENSAJE := 'Se ha insertado';
        INSERT INTO ARTICULO_CATEGORIA VALUES(P_ID_ARTICULO,P_ID_CATEGORIA);
    EXCEPTION
      when dup_val_on_index then
        P_MENSAJE := 'Indice Repetido';
    when others then
        P_MENSAJE := 'Error desconocido';
COMMIT;
END INSERTAR_ARTICULO_CATEGORIA;
/
---ANONIMOS----
DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 1;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=1 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 2;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=1 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 3;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=2 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 4;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=2 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 5;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=2 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 6;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=2 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 7;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=1 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 8;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=3 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 9;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=3 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 10;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=3 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_ARTICULO ARTICULO_CATEGORIA.ID_ARTICULO%TYPE:= 8;
    V_ID_CATEGORIA ARTICULO_CATEGORIA.ID_CATEGORIA%TYPE:=4 ;
    V_MENSAJE VARCHAR2(30);
begin
  INSERTAR_ARTICULO_CATEGORIA(V_ID_ARTICULO,V_ID_CATEGORIA,V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

----------------------COMPRA----------------------
CREATE OR REPLACE TRIGGER ACTUALIZAR_ESTADO_ARTICULO BEFORE UPDATE OR INSERT ON ARTICULO_BODEGA FOR EACH ROW
BEGIN
  IF( :NEW.CANTIDAD = 0) THEN
    UPDATE ARTICULO
    SET ESTADO = 'No Disponible'
    WHERE ID_ARTICULO = :NEW.ID_ARTICULO;
  ELSE
    UPDATE ARTICULO
    SET ESTADO = 'Disponible'
    WHERE ID_ARTICULO = :NEW.ID_ARTICULO;
  END IF;
END ACTUALIZAR_ESTADO_ARTICULO;
/


CREATE OR REPLACE FUNCTION Calcular_MONTO (
    P_PRECIO_COMPRA VENTA.MONTOTOTAL%TYPE,
    P_CANTIDAD COMPRA_ARTICULO.CANTIDAD%TYPE) RETURN FLOAT AS
    v_Subtotal COMPRA.SUBTOTAL%TYPE;
BEGIN
    v_Subtotal := P_PRECIO_COMPRA*P_CANTIDAD;
RETURN v_Subtotal;
END Calcular_MONTO;
/


CREATE OR REPLACE FUNCTION Calcular_Impuesto (
    v_Subtotal VENTA.Subtotal%TYPE) RETURN FLOAT AS
    v_Impuesto VENTA.Impuesto%TYPE; 
BEGIN
    v_Impuesto := v_Subtotal*0.07;
RETURN v_Impuesto;
END Calcular_Impuesto;
/


CREATE OR REPLACE TRIGGER Actualiza_Compra AFTER INSERT ON COMPRA_ARTICULO FOR EACH ROW 
DECLARE
    v_Subtotal VENTA.Subtotal%TYPE;
    v_Impuesto VENTA.Impuesto%TYPE;
    v_MontoTotal VENTA.MontoTotal%TYPE;
    V_ID_PROVEEDOR COMPRA.ID_PROVEEDOR%TYPE;
BEGIN

    SELECT ID_PROVEEDOR INTO V_ID_PROVEEDOR FROM COMPRA WHERE ID_COMPRA = :NEW.ID_COMPRA;

    v_Subtotal := CALCULAR_MONTO(:NEW.PRECIO_COMPRA,:NEW.Cantidad);
    v_Impuesto := Calcular_Impuesto(v_Subtotal);
    v_MontoTotal := v_Subtotal + v_Impuesto;

    UPDATE COMPRA 
    SET 
        Subtotal = Subtotal + v_Subtotal,
        Impuesto = Impuesto + v_Impuesto,
        MontoTotal = MontoTotal + v_MontoTotal 
    WHERE ID_COMPRA = :NEW.ID_COMPRA;

    UPDATE ARTICULO_PROVEEDOR
    SET
        CANTIDAD_TOTAL = CANTIDAD_TOTAL + :NEW.CANTIDAD,
        MONTO_TOTAL = MONTO_TOTAL + V_MONTOTOTAL
    WHERE
        ID_ARTICULO = :NEW.ID_ARTICULO AND
        ID_PROVEEDOR = V_ID_PROVEEDOR;

END Actualiza_Compra;
/

create or replace procedure REALIZAR_COMPRA(
    P_ID_PROVEEDOR COMPRA.ID_PROVEEDOR%TYPE,
    p_ID_Empleado COMPRA.ID_Empleado%TYPE,
    P_MENSAJE OUT VARCHAR2) AS 
    V_ID_OCUPACION OCUPACION.ID_OCUPACION%TYPE;
    BEGIN
        SELECT ID_OCUPACION INTO V_ID_OCUPACION FROM EMPLEADO WHERE ID_Empleado = P_ID_Empleado;
        IF V_ID_OCUPACION = 1 THEN
            INSERT INTO COMPRA (ID_COMPRA, ID_PROVEEDOR, FECHA_COMPRA, ID_EMPLEADO)
            VALUES (SEQ_COMPRA.NEXTVAL, P_ID_PROVEEDOR, SYSTIMESTAMP, P_ID_EMPLEADO);
            P_MENSAJE :='Registro con éxito';
        ELSE
            P_MENSAJE :='Permisos de compra no autorizados';
        END IF;
    
    EXCEPTION
        when dup_val_on_index then
            P_MENSAJE := 'Indice Repetido';
        WHEN OTHERS THEN
            P_MENSAJE := 'Error desconocido';
    END REALIZAR_COMPRA;
/

-------------------ANONIMOS----------------
DECLARE
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE := 1;
    V_ID_EMPLEADO EMPLEADO.ID_EMPLEADO%TYPE := 1001;
    V_MENSAJE VARCHAR2(50);
begin
    REALIZAR_COMPRA(V_ID_PROVEEDOR, V_ID_EMPLEADO, V_MENSAJE);
    DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/


DECLARE
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE := 2;
    V_ID_EMPLEADO EMPLEADO.ID_EMPLEADO%TYPE := 2001;
    V_MENSAJE VARCHAR2(50);
begin
    REALIZAR_COMPRA(V_ID_PROVEEDOR, V_ID_EMPLEADO, V_MENSAJE);
    DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/


DECLARE
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE := 3;
    V_ID_EMPLEADO EMPLEADO.ID_EMPLEADO%TYPE := 3001;
    V_MENSAJE VARCHAR2(50);
begin
    REALIZAR_COMPRA(V_ID_PROVEEDOR, V_ID_EMPLEADO, V_MENSAJE);
    DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE := 1;
    V_ID_EMPLEADO EMPLEADO.ID_EMPLEADO%TYPE := 2001;
    V_MENSAJE VARCHAR2(50);
begin
    REALIZAR_COMPRA(V_ID_PROVEEDOR, V_ID_EMPLEADO, V_MENSAJE);
    DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/


DECLARE
    V_ID_PROVEEDOR ARTICULO_PROVEEDOR.ID_PROVEEDOR%TYPE := 1;
    V_ID_EMPLEADO EMPLEADO.ID_EMPLEADO%TYPE := 2012;
    V_MENSAJE VARCHAR2(50);
begin
    REALIZAR_COMPRA(V_ID_PROVEEDOR, V_ID_EMPLEADO, V_MENSAJE);
    DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/


----------------------COMPRA_ARTICULO----------------------

create or replace procedure COMPRAR_ARTICULO(
    P_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE,
    P_ID_ARTICULO COMPRA_ARTICULO.ID_ARTICULO%TYPE,
    P_PRECIO_COMPRA COMPRA_ARTICULO.PRECIO_COMPRA%TYPE,
    P_CANTIDAD COMPRA_ARTICULO.CANTIDAD%TYPE,
    P_ID_BODEGA BODEGA.ID_BODEGA%TYPE, 
    P_ESTADO COMPRA.ESTADO%TYPE,
    P_MENSAJE OUT VARCHAR2) AS 

    V_CANTIDAD COMPRA_ARTICULO.CANTIDAD%TYPE:= 0;
    V_CAPACIDAD BODEGA.CAPACIDAD%TYPE;
    V_ESTADO COMPRA.ESTADO%TYPE;
    v_MONTO COMPRA.MONTOTOTAL%TYPE;

    CURSOR C_ARTI_BOD IS
    SELECT ID_BODEGA,CANTIDAD FROM ARTICULO_BODEGA WHERE ID_BODEGA = P_ID_BODEGA;

    BEGIN

            SELECT CAPACIDAD INTO V_CAPACIDAD FROM BODEGA WHERE ID_BODEGA = P_ID_BODEGA;
	    SELECT ESTADO INTO V_ESTADO FROM COMPRA WHERE ID_COMPRA = P_ID_COMPRA;
	    SELECT MONTOTOTAL INTO V_MONTO FROM COMPRA WHERE ID_COMPRA = P_ID_COMPRA;
        
            FOR V_INDEX IN C_ARTI_BOD LOOP
        
                V_CANTIDAD := V_CANTIDAD + V_INDEX.CANTIDAD;

            end loop;

            V_CANTIDAD := V_CANTIDAD + P_CANTIDAD;
	    
	    IF(V_ESTADO = 'Terminada' OR V_ESTADO = 'Cancelado') THEN
		P_MENSAJE := 'Compra ya ha terminado';
	    ELSE

            IF(V_CAPACIDAD < V_CANTIDAD AND P_ESTADO = 'Terminada' AND V_MONTO = 0) THEN
			UPDATE COMPRA
			SET
				ESTADO = 'Cancelado'
			WHERE
				ID_COMPRA = P_ID_COMPRA;
 
			P_MENSAJE := 'Capcidad menor que la cantidad';
           ELSIF( V_CAPACIDAD < V_CANTIDAD AND V_ESTADO = 'Ejecucion') THEN

			UPDATE 	
				COMPRA
			SET
				ESTADO = P_ESTADO
			WHERE
				ID_COMPRA = P_ID_COMPRA;

			P_MENSAJE := 'Articulo cancelado, compra en ejecucion';
		
		

            ELSE 

                P_MENSAJE := 'Se ha insertado';

                INSERT INTO COMPRA_ARTICULO 
                VALUES (P_ID_COMPRA,P_ID_ARTICULO,P_PRECIO_COMPRA,P_CANTIDAD);
		
		IF( P_ESTADO = 'Ejecucion') THEN 
			NULL;
		
		ELSE

			
			UPDATE 	
				COMPRA
			SET
				ESTADO = P_ESTADO
			WHERE
				ID_COMPRA = P_ID_COMPRA;
		
		END IF;

		UPDATE ARTICULO_BODEGA
                SET
                	CANTIDAD = CANTIDAD + P_CANTIDAD
               	WHERE
                   	 ID_ARTICULO = P_ID_ARTICULO;
            END IF;
	 END IF;
    EXCEPTION
        when dup_val_on_index then
            P_MENSAJE := 'Indice Repetido';
	when others then
	    P_MENSAJE := 'Error desconocido';
    END COMPRAR_ARTICULO;
/

DECLARE
    V_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE:= 1;
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 1;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE;
    V_PRECIO_COMPRA COMPRA_ARTICULO.PRECIO_COMPRA%TYPE:= 2;
    V_CANTIDAD COMPRA_ARTICULO.CANTIDAD%TYPE := 10 ;
    V_ESTADO COMPRA.ESTADO%TYPE := 'Ejecucion';
    V_MENSAJE VARCHAR2(50);
begin

  SELECT ID_BODEGA INTO V_ID_BODEGA FROM ARTICULO_BODEGA WHERE ID_ARTICULO = V_ID_ARTICULO;

  COMPRAR_ARTICULO(V_ID_COMPRA,V_ID_ARTICULO,V_PRECIO_COMPRA,V_CANTIDAD,V_ID_BODEGA,V_ESTADO, V_MENSAJE);
  
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/




DECLARE
    V_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE:= 1;
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 2;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE;
    V_PRECIO_COMPRA COMPRA_ARTICULO.PRECIO_COMPRA%TYPE:= 3.5;
    V_ESTADO COMPRA.ESTADO%TYPE := 'Terminada';
    V_CANTIDAD COMPRA_ARTICULO.CANTIDAD%TYPE := 10 ;
    V_MENSAJE VARCHAR2(50);
begin
  SELECT ID_BODEGA INTO V_ID_BODEGA FROM ARTICULO_BODEGA WHERE ID_ARTICULO = V_ID_ARTICULO;

  COMPRAR_ARTICULO(V_ID_COMPRA,V_ID_ARTICULO,V_PRECIO_COMPRA,V_CANTIDAD,V_ID_BODEGA,V_ESTADO,V_MENSAJE);
  
 DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE:= 2;
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 3;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE;
    V_PRECIO_COMPRA COMPRA_articulo.PRECIO_COMPRA%TYPE:= 5;
    V_CANTIDAD COMPRA_articulo.CANTIDAD%TYPE := 5 ;
    V_ESTADO COMPRA.ESTADO%TYPE := 'Ejecucion';
    V_MENSAJE VARCHAR2(50);
begin
  SELECT ID_BODEGA INTO V_ID_BODEGA FROM ARTICULO_BODEGA WHERE ID_ARTICULO = V_ID_ARTICULO;

  COMPRAR_ARTICULO(V_ID_COMPRA,V_ID_ARTICULO,V_PRECIO_COMPRA,V_CANTIDAD,V_ID_BODEGA,V_ESTADO, V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE:= 2;
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 4;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE;
    V_PRECIO_COMPRA COMPRA_articulo.PRECIO_COMPRA%TYPE:= 10;
    V_CANTIDAD COMPRA_articulo.CANTIDAD%TYPE := 5 ;
    V_ESTADO COMPRA.ESTADO%TYPE := 'Terminada';
    V_MENSAJE VARCHAR2(50);
begin
   SELECT ID_BODEGA INTO V_ID_BODEGA FROM ARTICULO_BODEGA WHERE ID_ARTICULO = V_ID_ARTICULO;
  COMPRAR_ARTICULO(V_ID_COMPRA,V_ID_ARTICULO,V_PRECIO_COMPRA,V_CANTIDAD,V_ID_BODEGA,V_ESTADO, V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE:= 3;
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 5;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE;
    V_PRECIO_COMPRA COMPRA_articulo.PRECIO_COMPRA%TYPE:= 10;
    V_CANTIDAD COMPRA_articulo.CANTIDAD%TYPE := 5 ;
    V_ESTADO COMPRA.ESTADO%TYPE := 'Ejecucion';
    V_MENSAJE VARCHAR2(50);
begin
    SELECT ID_BODEGA INTO V_ID_BODEGA FROM ARTICULO_BODEGA WHERE ID_ARTICULO = V_ID_ARTICULO;

  
  COMPRAR_ARTICULO(V_ID_COMPRA,V_ID_ARTICULO,V_PRECIO_COMPRA,V_CANTIDAD,V_ID_BODEGA,V_ESTADO, V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE:= 3;
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 6;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE;
    V_PRECIO_COMPRA COMPRA_articulo.PRECIO_COMPRA%TYPE:= 5;
    V_CANTIDAD COMPRA_articulo.CANTIDAD%TYPE := 5 ;
    V_ESTADO COMPRA.ESTADO%TYPE := 'Terminada';
    V_MENSAJE VARCHAR2(50);
begin
    SELECT ID_BODEGA INTO V_ID_BODEGA FROM ARTICULO_BODEGA WHERE ID_ARTICULO = V_ID_ARTICULO;
    
    
  COMPRAR_ARTICULO(V_ID_COMPRA,V_ID_ARTICULO,V_PRECIO_COMPRA,V_CANTIDAD,V_ID_BODEGA,V_ESTADO, V_MENSAJE);
    DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE:=4;
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 7;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE;
    V_PRECIO_COMPRA COMPRA_articulo.PRECIO_COMPRA%TYPE:= 15;
    V_CANTIDAD COMPRA_articulo.CANTIDAD%TYPE := 15;
    V_ESTADO COMPRA.ESTADO%TYPE := 'Ejecucion';
    V_MENSAJE VARCHAR2(50);
begin
    SELECT ID_BODEGA INTO V_ID_BODEGA FROM ARTICULO_BODEGA WHERE ID_ARTICULO = V_ID_ARTICULO;

    
  COMPRAR_ARTICULO(V_ID_COMPRA,V_ID_ARTICULO,V_PRECIO_COMPRA,V_CANTIDAD,V_ID_BODEGA,V_ESTADO, V_MENSAJE);
    DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE:=4;
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 8;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE;
    V_PRECIO_COMPRA COMPRA_articulo.PRECIO_COMPRA%TYPE:= 100;
    V_CANTIDAD COMPRA_articulo.CANTIDAD%TYPE := 3;
    V_ESTADO COMPRA.ESTADO%TYPE := 'Ejecucion';
    V_MENSAJE VARCHAR2(50);
begin
    SELECT ID_BODEGA INTO V_ID_BODEGA FROM ARTICULO_BODEGA WHERE ID_ARTICULO = V_ID_ARTICULO;

    
    COMPRAR_ARTICULO(V_ID_COMPRA,V_ID_ARTICULO,V_PRECIO_COMPRA,V_CANTIDAD,V_ID_BODEGA,V_ESTADO, V_MENSAJE);
    DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE:=4;
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 9;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE;
    V_PRECIO_COMPRA COMPRA_articulo.PRECIO_COMPRA%TYPE:= 100;
    V_CANTIDAD COMPRA_articulo.CANTIDAD%TYPE := 7 ;
    V_ESTADO COMPRA.ESTADO%TYPE := 'Ejecucion';
    V_MENSAJE VARCHAR2(50);
begin
    SELECT ID_BODEGA INTO V_ID_BODEGA FROM ARTICULO_BODEGA WHERE ID_ARTICULO = V_ID_ARTICULO;

    
    COMPRAR_ARTICULO(V_ID_COMPRA,V_ID_ARTICULO,V_PRECIO_COMPRA,V_CANTIDAD,V_ID_BODEGA,V_ESTADO, V_MENSAJE);
    DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/

DECLARE
    V_ID_COMPRA COMPRA_ARTICULO.ID_COMPRA%TYPE:=4;
    V_ID_ARTICULO ARTICULO_PROVEEDOR.ID_ARTICULO%TYPE:= 10;
    V_ID_BODEGA ARTICULO_BODEGA.ID_BODEGA%TYPE;
    V_PRECIO_COMPRA COMPRA_articulo.PRECIO_COMPRA%TYPE:=100;
    V_CANTIDAD COMPRA_articulo.CANTIDAD%TYPE := 7 ;
    V_ESTADO COMPRA.ESTADO%TYPE := 'Terminada';
    V_MENSAJE VARCHAR2(50);
begin
    SELECT ID_BODEGA INTO V_ID_BODEGA FROM ARTICULO_BODEGA WHERE ID_ARTICULO = V_ID_ARTICULO;
  
  COMPRAR_ARTICULO(V_ID_COMPRA,V_ID_ARTICULO,V_PRECIO_COMPRA,V_CANTIDAD,V_ID_BODEGA,V_ESTADO, V_MENSAJE);
  DBMS_OUTPUT.PUT_LINE(V_MENSAJE);
end;
/


-----VENTA-----
CREATE OR REPLACE PROCEDURE RegistrarVenta(
    p_ID_Empleado IN VENTA.ID_Empleado%TYPE,
    p_ID_Cliente IN VENTA.ID_Cliente%TYPE,
    p_mensaje OUT VARCHAR2)AS

    v_ID_Cliente CLIENTE.ID_Cliente%TYPE;
    v_ID_Ocupacion EMPLEADO.ID_Ocupacion%TYPE;
    v_ID_Sucursal VENTA.ID_Sucursal%TYPE;
BEGIN
    SELECT ID_Cliente INTO v_ID_Cliente FROM CLIENTE WHERE ID_Cliente=p_ID_Cliente; --solo para validar si el cliente está o no registrado; Si no existe, va a la exception
    SELECT ID_Ocupacion INTO v_ID_Ocupacion FROM EMPLEADO WHERE ID_Empleado = p_ID_Empleado; 

    IF v_ID_Ocupacion = 1 OR v_ID_Ocupacion = 2 THEN -- valida que el empleado que este registrando la venta sea gerente o vendedor
        p_mensaje:='Venta registrada con éxito';
        SELECT ID_Sucursal INTO v_ID_Sucursal FROM EMPLEADO WHERE ID_Empleado = p_ID_Empleado; --busca la sucursal correspondiente al empleado--

        INSERT INTO VENTA (ID_Venta, ID_Empleado, ID_Sucursal, ID_Cliente, Fecha_Venta)
        VALUES (Venta_seq.NEXTVAL, p_ID_Empleado, v_ID_Sucursal, p_ID_Cliente, SYSTIMESTAMP);-- SE DEBERIA PONER POR DEFAULT MONTO TOTAL = 0.0--
    ELSE
        p_mensaje:='Su usuario no puede realizar ventas';
    END IF;
EXCEPTION
    WHEN no_data_found THEN
        p_mensaje:='El empleado o el cliente no existen.'; -- podria tratar de llamar al procedimiento cliente para crearlo de una vez, PEEERO no se tiene informacion sufiente.
    WHEN dup_val_on_index THEN
        p_mensaje:='Esta venta ya existe.';
    WHEN others THEN
        p_mensaje:='Error desconocido';
COMMIT;
END RegistrarVenta;
/
--anonimo--
DECLARE
    v_ID_Empleado VENTA.ID_Empleado%TYPE:=1002;
    v_ID_Cliente VENTA.ID_Cliente%TYPE:=1;
    v_mensaje VARCHAR2(50);
BEGIN
    RegistrarVenta(v_ID_Empleado, v_ID_Cliente, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/


DECLARE
    v_ID_Empleado VENTA.ID_Empleado%TYPE:=1012;
    v_ID_Cliente VENTA.ID_Cliente%TYPE:=2;
    v_mensaje VARCHAR2(50);
BEGIN
    RegistrarVenta(v_ID_Empleado, v_ID_Cliente, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Empleado VENTA.ID_Empleado%TYPE:=2002;
    v_ID_Cliente VENTA.ID_Cliente%TYPE:=1;
    v_mensaje VARCHAR2(50);
BEGIN
    RegistrarVenta(v_ID_Empleado, v_ID_Cliente, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Empleado VENTA.ID_Empleado%TYPE:=2001;
    v_ID_Cliente VENTA.ID_Cliente%TYPE:=3;
    v_mensaje VARCHAR2(50);
BEGIN
    RegistrarVenta(v_ID_Empleado, v_ID_Cliente, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Empleado VENTA.ID_Empleado%TYPE:=3002;
    v_ID_Cliente VENTA.ID_Cliente%TYPE:=4;
    v_mensaje VARCHAR2(50);
BEGIN
    RegistrarVenta(v_ID_Empleado, v_ID_Cliente, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Empleado VENTA.ID_Empleado%TYPE:=3002;
    v_ID_Cliente VENTA.ID_Cliente%TYPE:=5;
    v_mensaje VARCHAR2(50);
BEGIN
    RegistrarVenta(v_ID_Empleado, v_ID_Cliente, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Empleado VENTA.ID_Empleado%TYPE:=3012;
    v_ID_Cliente VENTA.ID_Cliente%TYPE:=5;
    v_mensaje VARCHAR2(50);
BEGIN
    RegistrarVenta(v_ID_Empleado, v_ID_Cliente, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE 
    v_ID_Empleado VENTA.ID_Empleado%TYPE:=2001; -- VENTA CANCELADA--
    v_ID_Cliente VENTA.ID_Cliente%TYPE:=4;
    v_mensaje VARCHAR2(50);
BEGIN
    RegistrarVenta(v_ID_Empleado, v_ID_Cliente, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Empleado VENTA.ID_Empleado%TYPE:=3002; -- VENTA EN EJECUCION--
    v_ID_Cliente VENTA.ID_Cliente%TYPE:=2;
    v_mensaje VARCHAR2(50);
BEGIN
    RegistrarVenta(v_ID_Empleado, v_ID_Cliente, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Empleado VENTA.ID_Empleado%TYPE:=1001; -- VENTA EN CANCELADA--
    v_ID_Cliente VENTA.ID_Cliente%TYPE:=1;
    v_mensaje VARCHAR2(50);
BEGIN
    RegistrarVenta(v_ID_Empleado, v_ID_Cliente, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

-----VENTA_ARTICULO-----



-----------VENTA-------------------
CREATE OR REPLACE TRIGGER Actualiza_Monto_Venta AFTER INSERT ON VENTA_ARTICULO FOR EACH ROW 
DECLARE
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE;
    v_Subtotal VENTA.Subtotal%TYPE;
    v_Impuesto VENTA.Impuesto%TYPE;
    v_MontoTotal VENTA.MontoTotal%TYPE;
BEGIN
    SELECT PRECIO_ARTICULO INTO V_PRECIO_ARTICULO FROM ARTICULO WHERE ID_Articulo = :NEW.ID_Articulo;
    v_Subtotal := CALCULAR_MONTO(V_PRECIO_ARTICULO,:NEW.Cantidad);
    v_Impuesto := Calcular_Impuesto(v_Subtotal);
    v_MontoTotal := v_Subtotal + v_Impuesto;
    UPDATE VENTA 
    SET 
        Subtotal = Subtotal + v_Subtotal,
        Impuesto = Impuesto + v_Impuesto,
        MontoTotal = MontoTotal + v_MontoTotal 
    WHERE ID_Venta = :NEW.ID_Venta;
END Actualiza_Monto_Venta;
/

CREATE OR REPLACE PROCEDURE Ingresar_Venta_Articulo ( 
    p_ID_Venta IN VENTA_ARTICULO.ID_Venta%TYPE,
    p_ID_Articulo IN VENTA_ARTICULO.ID_Articulo%TYPE,
    p_Cantidad IN VENTA_ARTICULO.Cantidad%TYPE,
    p_Estado IN VENTA.Estado%TYPE,
    p_mensaje OUT VARCHAR2)AS
    v_Cantidad ARTICULO_BODEGA.Cantidad%TYPE;
    v_Estado VENTA.Estado%TYPE;-- busca el estado actual de la venta
    v_Subtotal VENTA.Subtotal%TYPE;
BEGIN
    SELECT Estado INTO v_Estado FROM VENTA WHERE ID_Venta = p_ID_Venta; -- 1. esto mismo me valida si la venta existe o no/ busca el estado actual--

    IF (v_Estado = 'Ejecucion') THEN
        SELECT Cantidad INTO v_Cantidad FROM ARTICULO_BODEGA WHERE ID_Articulo = p_ID_Articulo; -- busco la cantidad en bodega del articulo.--
        SELECT Subtotal INTO v_Subtotal FROM VENTA WHERE ID_Venta = p_ID_Venta; --- para validar el ELSEIF---

        IF (v_Cantidad >= p_Cantidad) THEN
            p_mensaje:='Articulo registrado correctamente.';
            INSERT INTO VENTA_ARTICULO (ID_Venta, ID_Articulo, Cantidad) 
            VALUES (p_ID_Venta, p_ID_Articulo, p_Cantidad);

            UPDATE ARTICULO_BODEGA --ACTUALIZA CANTIDAD EN BODEGA
            SET Cantidad = Cantidad - p_Cantidad
            WHERE ID_Articulo = p_ID_Articulo;

            UPDATE VENTA --ACTUALIZA EL ESTADO DE LA VENTA--
            SET Estado = p_Estado 
            WHERE ID_Venta = p_ID_Venta;

        ELSIF ((v_Cantidad < p_Cantidad) AND p_Estado = 'Terminada' AND v_Subtotal = 0)THEN
            p_mensaje:='Venta cancelada.';
            UPDATE VENTA 
            SET Estado = 'Cancelado'
            WHERE ID_Venta = p_ID_Venta;

        ELSIF ((v_cantidad < p_Cantidad) AND p_Estado = 'Terminada' AND v_Subtotal > 0) THEN
            p_mensaje:='No hay sufientes articulos.'; 
            UPDATE VENTA --ACTUALIZA EL ESTADO DE LA VENTA--
            SET Estado = p_Estado 
            WHERE ID_Venta = p_ID_Venta;
        ELSE
            p_mensaje:='No hay sufientes articulos.';
        END IF;
    ELSE 
        p_mensaje:='Esta venta ya está cerrada. Cree una nueva.';
    END IF;
EXCEPTION
    WHEN no_data_found THEN -- 1. si el id venta no existe, deberia venir aqui y mandar este mensaje
        p_mensaje:='Esta venta no está registrada.';
    WHEN dup_val_on_index THEN
        p_mensaje:='Ya registró este articulo en la venta.';
    WHEN others THEN 
        p_mensaje:='Error desconocido';
COMMIT;
END Ingresar_Venta_Articulo;
/
--anonimo--
DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=1;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=3;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=2;
    v_Estado VENTA.ESTADO%TYPE:='Ejecucion';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=1;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=10;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=1;
    v_Estado VENTA.ESTADO%TYPE:='Ejecucion';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=1;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=4;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=1;
    v_Estado VENTA.ESTADO%TYPE:='Terminada';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=2;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=1;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=6;
    v_Estado VENTA.ESTADO%TYPE:='Ejecucion';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=2;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=2;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=4;
    v_Estado VENTA.ESTADO%TYPE:='Terminada';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=3;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=3;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=2;
    v_Estado VENTA.ESTADO%TYPE:='Terminada';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=4;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=8;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=1;
    v_Estado VENTA.ESTADO%TYPE:='Ejecucion';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=4;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=6;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=2;
    v_Estado VENTA.ESTADO%TYPE:='Terminada';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=5;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=9;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=1;
    v_Estado VENTA.ESTADO%TYPE:='Ejecucion';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=5;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=7;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=3;
    v_Estado VENTA.ESTADO%TYPE:='Terminada';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=6;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=5;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=1;
    v_Estado VENTA.ESTADO%TYPE:='Ejecucion';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=6;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=4;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=1;
    v_Estado VENTA.ESTADO%TYPE:='Ejecucion';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=6;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=3;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=2;
    v_Estado VENTA.ESTADO%TYPE:='Terminada';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=7;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=10;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=2;
    v_Estado VENTA.ESTADO%TYPE:='Terminada';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=8;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=6;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=8;
    v_Estado VENTA.ESTADO%TYPE:='Terminada';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=9;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=8;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=1;
    v_Estado VENTA.ESTADO%TYPE:='Ejecucion';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

DECLARE
    v_ID_Venta VENTA_ARTICULO.ID_Venta%TYPE:=10;
    v_ID_Articulo VENTA_ARTICULO.ID_Articulo%TYPE:=3;
    v_Cantidad VENTA_ARTICULO.Cantidad%TYPE:=30;
    v_Estado VENTA.ESTADO%TYPE:='Terminada';
    v_mensaje VARCHAR2(50);
BEGIN
    Ingresar_Venta_Articulo(v_ID_Venta, v_ID_Articulo, v_Cantidad, v_Estado, v_mensaje);
    DBMS_OUTPUT.PUT_LINE(v_mensaje);
END;
/

---------------------------PROCEDURE HACER PEDIDO-------------------------------------

CREATE OR REPLACE PROCEDURE hacerPedido (
    p_ID_Cliente PEDIDO.ID_Cliente%TYPE,
    p_ID_Sucursal PEDIDO.ID_Sucursal%TYPE,
    p_Descripcion PEDIDO.Descripcion%TYPE,
    p_Fecha_Insercion PEDIDO.Fecha_Insercion%TYPE,
    p_Mensaje OUT VARCHAR2
) AS

    v_ID_Cliente PEDIDO.ID_Cliente%TYPE;

BEGIN

    SELECT ID_Cliente INTO v_ID_Cliente FROM CLIENTE WHERE ID_Cliente = p_ID_Cliente;

    INSERT INTO PEDIDO (ID_Pedido, ID_Cliente, ID_Sucursal, Descripcion, Fecha_Insercion)
    VALUES (seq_ID_Pedido.nextval, p_ID_Cliente, p_ID_SucursaL, p_Descripcion, p_Fecha_Insercion);
    p_Mensaje := 'Pedido registrado con éxito.';
    
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        p_Mensaje:='El cliente no existe.';
    WHEN DUP_VAL_ON_INDEX THEN
        p_Mensaje := 'El registro ya existe.';
    WHEN OTHERS THEN
        p_Mensaje :='Error desconocido.';

COMMIT;
END hacerPedido;
/

---------------------------ANONIMO HACER PEDIDO-------------------------------------

DECLARE
    v_ID_Cliente PEDIDO.ID_Cliente%TYPE := 1;
    v_ID_Sucursal PEDIDO.ID_Sucursal%TYPE := 1;
    v_Descripcion PEDIDO.Descripcion%TYPE := 'Tener mucho cuidado con la estufa y la secadora';
    v_Fecha_Insercion PEDIDO.Fecha_Insercion%TYPE := SYSTIMESTAMP;
    v_Mensaje VARCHAR2(35);

BEGIN
    hacerPedido (v_ID_Cliente, v_ID_SucursaL, v_Descripcion, v_Fecha_Insercion, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/


DECLARE
    v_ID_Cliente PEDIDO.ID_Cliente%TYPE := 2;
    v_ID_Sucursal PEDIDO.ID_Sucursal%TYPE := 2;
    v_Descripcion PEDIDO.Descripcion%TYPE := 'Cuidado con el perro';
    v_Fecha_Insercion PEDIDO.Fecha_Insercion%TYPE := SYSTIMESTAMP;
    v_Mensaje VARCHAR2(35);

BEGIN
    hacerPedido (v_ID_Cliente, v_ID_SucursaL, v_Descripcion, v_Fecha_Insercion, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);
    
END;
/


DECLARE
    v_ID_Cliente PEDIDO.ID_Cliente%TYPE := 3;
    v_ID_Sucursal PEDIDO.ID_Sucursal%TYPE := 3;
    v_Descripcion PEDIDO.Descripcion%TYPE := 'No me juzguen por comprar muchas mangueras';
    v_Fecha_Insercion PEDIDO.Fecha_Insercion%TYPE := SYSTIMESTAMP;
    v_Mensaje VARCHAR2(35);

BEGIN
    hacerPedido (v_ID_Cliente, v_ID_SucursaL, v_Descripcion, v_Fecha_Insercion, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/


DECLARE
    v_ID_Cliente PEDIDO.ID_Cliente%TYPE := 5;
    v_ID_Sucursal PEDIDO.ID_Sucursal%TYPE := 1;
    v_Descripcion PEDIDO.Descripcion%TYPE := 'Llamar al celular que el timbre no funciona';
    v_Fecha_Insercion PEDIDO.Fecha_Insercion%TYPE := SYSTIMESTAMP;
    v_Mensaje VARCHAR2(35);

BEGIN
    hacerPedido (v_ID_Cliente, v_ID_SucursaL, v_Descripcion, v_Fecha_Insercion, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);
    
END;
/


DECLARE
    v_ID_Cliente PEDIDO.ID_Cliente%TYPE := 1;
    v_ID_Sucursal PEDIDO.ID_Sucursal%TYPE := 3;
    v_Descripcion PEDIDO.Descripcion%TYPE := 'Tener mucho cuidado con la lavadora';
    v_Fecha_Insercion PEDIDO.Fecha_Insercion%TYPE := SYSTIMESTAMP;
    v_Mensaje VARCHAR2(35);

BEGIN
    hacerPedido (v_ID_Cliente, v_ID_SucursaL, v_Descripcion, v_Fecha_Insercion, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);
    
END;
/


DECLARE
    v_ID_Cliente PEDIDO.ID_Cliente%TYPE := 4;
    v_ID_Sucursal PEDIDO.ID_Sucursal%TYPE := 2;
    v_Descripcion PEDIDO.Descripcion%TYPE := 'Dejar las pinturas en el patio';
    v_Fecha_Insercion PEDIDO.Fecha_Insercion%TYPE := SYSTIMESTAMP;
    v_Mensaje VARCHAR2(35);

BEGIN
    hacerPedido (v_ID_Cliente, v_ID_SucursaL, v_Descripcion, v_Fecha_Insercion, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);
    
END;
/


---------------------------PROCEDURE CREAR PEDIDO_ARTICULO-------------------------------------

CREATE OR REPLACE TRIGGER actualizarContabilidadPedido AFTER INSERT ON PEDIDO_ARTICULO FOR EACH ROW 
DECLARE
    V_PRECIO_ARTICULO ARTICULO.PRECIO_ARTICULO%TYPE;
    v_Subtotal PEDIDO.Subtotal%TYPE;
    v_Impuesto PEDIDO.Impuesto%TYPE;
    v_MontoTotal PEDIDO.MontoTotal%TYPE;

BEGIN
    SELECT PRECIO_ARTICULO INTO V_PRECIO_ARTICULO FROM ARTICULO WHERE ID_Articulo = :NEW.ID_Articulo;
    
    v_Subtotal := CALCULAR_MONTO(V_PRECIO_ARTICULO,:NEW.Cantidad);
    v_Impuesto := Calcular_Impuesto (v_Subtotal);
    v_MontoTotal := v_Subtotal + v_Impuesto;

    UPDATE PEDIDO
    SET 
        Subtotal = Subtotal + v_Subtotal,
        Impuesto = Impuesto + v_Impuesto,
        MontoTotal = MontoTotal + v_MontoTotal
    WHERE ID_Pedido = :NEW.ID_Pedido;

END actualizarContabilidadPedido;
/


CREATE OR REPLACE PROCEDURE insertarArticulosPedido ( 
    p_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE,
    p_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE,
    p_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE,
    p_Estado PEDIDO.Estado%TYPE,
    p_Mensaje OUT VARCHAR2
)AS
    v_Cantidad ARTICULO_BODEGA.Cantidad%TYPE;
    v_Estado PEDIDO.Estado%TYPE;
    v_Subtotal PEDIDO.Subtotal%TYPE;
BEGIN
    SELECT Estado INTO v_Estado FROM PEDIDO WHERE ID_Pedido = p_ID_Pedido;
    IF (v_Estado = 'Ejecucion') THEN
        SELECT Cantidad INTO v_Cantidad FROM ARTICULO_BODEGA WHERE ID_Articulo = p_ID_Articulo;
        SELECT Subtotal INTO v_Subtotal FROM PEDIDO WHERE ID_Pedido = p_ID_Pedido; 
        IF (v_Cantidad >= p_Cantidad) THEN
            INSERT INTO PEDIDO_ARTICULO (ID_Pedido, ID_Articulo, Cantidad) 
            VALUES (p_ID_Pedido, p_ID_Articulo, p_Cantidad);
            p_Mensaje:='Artículo registrado con éxito.';
            UPDATE ARTICULO_BODEGA 
            SET Cantidad = Cantidad - p_Cantidad
            WHERE ID_Articulo = p_ID_Articulo;
            IF (p_Estado ='Terminada') THEN
                UPDATE PEDIDO
                SET 
                    Estado = p_Estado,
                    MONTOTOTAL = MONTOTOTAL + 3.50,
                    Fecha_Entrega = Fecha_Insercion + 2
                WHERE ID_Pedido = p_ID_Pedido;
            ELSE
                UPDATE PEDIDO
                SET Estado = p_Estado
                WHERE ID_Pedido = p_ID_Pedido;
            END IF;
        ELSIF ((v_Cantidad < p_Cantidad) AND p_Estado = 'Terminada' AND v_Subtotal = 0)THEN
            p_Mensaje:='Pedido cancelado.'; 
            UPDATE PEDIDO 
            SET Estado = 'Cancelado'
            WHERE ID_Pedido = p_ID_Pedido;
        ELSIF ((v_Cantidad < p_Cantidad) AND p_Estado = 'Terminada' AND v_Subtotal > 0) THEN
            p_Mensaje:='No hay suficientes artículos.'; 
            UPDATE PEDIDO
            SET 
                Estado = p_Estado,
                MONTOTOTAL = MONTOTOTAL + 3.50,
                Fecha_Entrega = Fecha_Insercion + 2
            WHERE ID_Pedido = p_ID_Pedido;
        ELSE
            p_Mensaje:='No hay suficientes artículos.';
        END IF;
    ELSE 
        p_mensaje:='Este pedido se encuentra terminado o cancelado. Cree una nueva.';
    END IF;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        p_Mensaje:='Este pedido no se encuentra registrado.';
    WHEN DUP_VAL_ON_INDEX THEN
        p_Mensaje := 'Ya se registro este artículo.';
    WHEN OTHERS THEN
        p_Mensaje :='Error desconocido.';
COMMIT;
END insertarArticulosPedido;
/


---------------------------ANONIMO CREAR PEDIDO_ARTICULO-------------------------------------

---------------------------PEDIDO 1-------------------------------------
DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 1;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 1;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 2;
    v_Estado PEDIDO.ESTADO%TYPE:='Ejecucion';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 1;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 8;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 1;
    v_Estado PEDIDO.ESTADO%TYPE:='Ejecucion';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/
    
DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 1;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 10;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 1;
    v_Estado PEDIDO.ESTADO%TYPE:='Terminada';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

---------------------------PEDIDO 2-------------------------------------

DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 2;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 3;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 1;
    v_Estado PEDIDO.ESTADO%TYPE:='Ejecucion';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 2;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 4;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 1;
    v_Estado PEDIDO.ESTADO%TYPE:='Ejecucion';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 2;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 5;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 1;
    v_Estado PEDIDO.ESTADO%TYPE:='Ejecucion';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 2;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 6;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 1;
    v_Estado PEDIDO.ESTADO%TYPE:='Terminada';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

---------------------------PEDIDO 3-------------------------------------

DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 3;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 2;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 3;
    v_Estado PEDIDO.ESTADO%TYPE:='Terminada';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/


---------------------------PEDIDO 4-------------------------------------

DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 4;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 7;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 2;
    v_Estado PEDIDO.ESTADO%TYPE:='Ejecucion';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 4;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 9;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 2;
    v_Estado PEDIDO.ESTADO%TYPE:='Terminada';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/
    
---------------------------PEDIDO 5-------------------------------------

DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 5;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 9;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 1;
    v_Estado PEDIDO.ESTADO%TYPE:='Terminada';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/

---------------------------PEDIDO 6-------------------------------------

DECLARE
    v_ID_Pedido PEDIDO_ARTICULO.ID_Pedido%TYPE := 6;
    v_ID_Articulo PEDIDO_ARTICULO.ID_Articulo%TYPE := 6;
    v_Cantidad PEDIDO_ARTICULO.Cantidad%TYPE := 5;
    v_Estado PEDIDO.ESTADO%TYPE:='Terminada';
    v_Mensaje VARCHAR2(70);

BEGIN
    insertarArticulosPedido (v_ID_Pedido, v_ID_Articulo, v_Cantidad, v_Estado, v_Mensaje);
    DBMS_OUTPUT.PUT_LINE(v_Mensaje);

END;
/


--------------------------VISTAS------------------------

---- View: Factura de ventas terminadas---
CREATE OR REPLACE VIEW FACTURA_VENTA AS
SELECT
    A.ID_VENTA,
    A.ID_Empleado,
    (C.Nombre||' '|| C.Apellido) AS CLIENTE,
    B.Lugar AS SUCURSAL,
    A.Subtotal,
    A.Impuesto,
    A.MontoTotal
FROM 
    VENTA A JOIN SUCURSAL B ON A.ID_SucursaL = B.ID_SucursaL
    JOIN CLIENTE C ON A.ID_Cliente = C.ID_Cliente WHERE A.ESTADO = 'Terminada'
    ORDER BY A.ID_VENTA; 

CREATE OR REPLACE VIEW FACTURA_VENTA_ARTICULOS AS 
SELECT 
    A.ID_VENTA, 
    A.ID_Empleado, 
    B.ID_Cliente,
    C.Lugar AS Sucursal,
    D.Nombre AS Articulo, 
    D.Precio_Articulo,
    E.Cantidad,
    (D.Precio_Articulo*E.Cantidad) AS Monto
FROM 
    VENTA A JOIN CLIENTE B ON A.ID_Cliente = B.ID_Cliente
    JOIN SUCURSAL C ON A.ID_Sucursal = C.ID_Sucursal
    JOIN VENTA_ARTICULO E ON A.ID_VENTA = E.ID_VENTA
    JOIN ARTICULO D ON E.ID_ARTICULO = D.ID_ARTICULO
    WHERE A.ESTADO = 'Terminada'
    ORDER BY A.ID_VENTA;

---VIEW: Ventas canceladas---
CREATE OR REPLACE VIEW VENTAS_CANCELADAS AS
SELECT
A.ID_VENTA,
A.ID_Empleado,
(C.Nombre||' '|| C.Apellido) AS CLIENTE,
B.Lugar AS SUCURSAL
FROM 
VENTA A JOIN SUCURSAL B ON A.ID_SucursaL = B.ID_SucursaL
JOIN CLIENTE C ON A.ID_Cliente = C.ID_Cliente WHERE A.ESTADO = 'Cancelado'
ORDER BY A.ID_VENTA; 

-----------------ARTICULO,CATEGORIA,BODEGA,PROVEEDOR---------------------------------------------------
CREATE OR REPLACE VIEW VISTA_CATEGORIA AS 
SELECT C.NOMBRE AS CATEGORIA, A.NOMBRE AS ARTICULO, A.DESCRIPCION
FROM ARTICULO A 
JOIN ARTICULO_CATEGORIA B ON A.ID_ARTICULO = B.ID_ARTICULO
JOIN CATEGORIA C ON B.ID_CATEGORIA = C.ID_CATEGORIA;

/*Esta vista es utilizada para conocer la descripción general de los articulos, ademas de saber a que categria pertenece cada articulo.*/

CREATE OR REPLACE  VIEW VISTA_RELACIONID AS 
SELECT J.ID_CATEGORIA,F.ID_BODEGA,A.ID_ARTICULO,G.ID_PROVEEDOR
FROM ARTICULO A 
JOIN ARTICULO_BODEGA B ON A.ID_ARTICULO = B.ID_ARTICULO 
JOIN BODEGA F ON F.ID_BODEGA = B.ID_BODEGA 
JOIN ARTICULO_PROVEEDOR D ON A.ID_ARTICULO = D.ID_ARTICULO 
JOIN PROVEEDOR G ON D.ID_PROVEEDOR = G.ID_PROVEEDOR
JOIN ARTICULO_CATEGORIA H ON A.ID_ARTICULO = H.ID_ARTICULO
JOIN CATEGORIA J ON J.ID_CATEGORIA = H.ID_CATEGORIA 
ORDER BY J.ID_CATEGORIA;

/*Esta vista permite identificar de forma rapida las llaves que representan todas las relaciones referentes a los articulos. El principal uso que se le dio en el desarrollo fue para determinar las relaciones correctamente*/

CREATE OR REPLACE  VIEW VISTA_ARTICULO AS 
SELECT F.ID_BODEGA,F.UBICACION AS BODEGA,A.NOMBRE AS ARTICULO,B.CANTIDAD AS CANTIDAD,G.NOMBRE AS PROVEEDOR
FROM ARTICULO A 
JOIN ARTICULO_BODEGA B ON A.ID_ARTICULO = B.ID_ARTICULO 
JOIN BODEGA F ON F.ID_BODEGA = B.ID_BODEGA 
JOIN ARTICULO_PROVEEDOR D ON A.ID_ARTICULO = D.ID_ARTICULO 
JOIN PROVEEDOR G ON D.ID_PROVEEDOR = G.ID_PROVEEDOR
JOIN ARTICULO_CATEGORIA H ON A.ID_ARTICULO = H.ID_ARTICULO
JOIN CATEGORIA J ON J.ID_CATEGORIA = H.ID_CATEGORIA 
ORDER BY F.ID_BODEGA;

/*El objetivo de esta vista es visualizar todos los articulos organizados mediante bodega, y saber que  almacena cada una. Se tiene la cantidad de cada articulo, y el nombre del proveedor. */


CREATE OR REPLACE VIEW VISTA_BODEGA AS
SELECT  A.UBICACION AS UBICACION, A.ID_BODEGA, SUM(B.CANTIDAD) AS CANT_ALMACENADA,COUNT(B.CANTIDAD) AS CANT_ARTICULOS ,A.CAPACIDAD FROM BODEGA A 
JOIN ARTICULO_BODEGA B ON A.ID_BODEGA = B.ID_BODEGA 
GROUP BY A.ID_BODEGA,A.CAPACIDAD,A.UBICACION;

/*Esta vista es utilizada para saber la información general de las bodegas, la ubicación, el id, la cantidad de elementos almacenados en esa bodega, y la cantidad de que articulos, con la capacidad total de la bodega.*/

CREATE OR REPLACE VIEW VISTA_PROVEEDOR AS
SELECT A.ID_PROVEEDOR, A.NOMBRE AS PROVEEDOR, SUM(B.CANTIDAD_TOTAL) AS CANTIDAD_TOTAL,SUM(B.MONTO_TOTAL) AS MONTO_TOTAL FROM PROVEEDOR A 
JOIN ARTICULO_PROVEEDOR B ON A.ID_PROVEEDOR = B.ID_PROVEEDOR 
GROUP BY A.ID_PROVEEDOR,A.NOMBRE;


--------------FACTURA DE LOS PEDIDOS QUE SE REALIZARON CON EXITO----------------
CREATE OR REPLACE VIEW FACT_PEDIDOS_EJECU AS SELECT A.ID_Pedido, (B.Nombre ||' '||B.Apellido) as Cliente, C.Lugar AS Sucursal, SUM (D.Cantidad) AS Cant_Articulos, A.SUBTOTAL, A.Impuesto, A.MONTOTOTAL, A.Fecha_Insercion
FROM PEDIDO A JOIN CLIENTE B ON A.ID_Cliente = B.ID_Cliente JOIN SUCURSAL C ON A.ID_SUCURSAL = C.ID_SUCURSAL JOIN PEDIDO_ARTICULO D ON A.ID_Pedido = D.ID_Pedido
WHERE A.Estado = 'Terminada'
GROUP BY A.ID_Pedido, B.Nombre, B.Apellido, C.Lugar, A.SUBTOTAL, A.Impuesto, A.MONTOTOTAL, A.Fecha_Insercion
ORDER BY A.ID_PEDIDO;

--------------FACTURA DE LOS PEDIDOS QUE SE REALIZARON SIN EXITO----------------
CREATE OR REPLACE VIEW FACT_PEDIDOS_CANCE AS SELECT A.ID_Pedido, (B.Nombre ||' '||B.Apellido) as Cliente, C.Lugar AS Sucursal, A.Fecha_Insercion
FROM PEDIDO A JOIN CLIENTE B ON A.ID_Cliente = B.ID_Cliente JOIN SUCURSAL C ON A.ID_SUCURSAL = C.ID_SUCURSAL
WHERE A.Estado = 'Cancelado'
ORDER BY A.ID_PEDIDO;

--------------VISTA PARA VER HACIA DONDE SE ENTREGA LOS PRODUCTOS----------------
CREATE OR REPLACE VIEW REPARTIR_PEDIDOS AS SELECT A.ID_Pedido, (B.Nombre ||' '||B.Apellido) as Cliente, B.Lugar AS DIRECCION, B.Celular, SUM (C.CANTIDAD) AS Cant_Articulos, A.Fecha_Entrega, A.DESCRIPCION
FROM PEDIDO A JOIN CLIENTE B ON A.ID_Cliente = B.ID_Cliente JOIN PEDIDO_ARTICULO C ON A.ID_PEDIDO = C.ID_PEDIDO
WHERE A.Estado = 'Terminada'
GROUP BY A.ID_Pedido, B.Nombre, B.Apellido, B.Lugar, B.Celular, A.Fecha_Entrega, A.DESCRIPCION
ORDER BY A.ID_PEDIDO;

--------------VISTA PARA VER LOS PRODUCTOS----------------
CREATE OR REPLACE VIEW ARTICULOS_PEDIDOS AS SELECT A.ID_Pedido, B.nombre, B.PRECIO_ARTICULO AS PREC_UNITARIO, A.CANTIDAD, A.CANTIDAD * B.PRECIO_ARTICULO AS Monto
FROM PEDIDO_ARTICULO A JOIN ARTICULO B ON A.ID_ARTICULO = B.ID_ARTICULO
ORDER BY A.ID_PEDIDO;

--------------TODOS LOS EMPLEADOS DE LAS SUCURSALES----------------
CREATE OR REPLACE VIEW EMPLEADOS_SUCURSAL AS SELECT
A.Lugar, B.ID_EMPLEADO, (B.Nombre ||' '||B.Apellido) as Empleado, B.cedula, B.Edad, C.Puesto
FROM EMPLEADO B
JOIN SUCURSAL A ON B.ID_SUCURSAL = A.ID_SUCURSAL
JOIN OCUPACION C ON B.ID_OCUPACION = C.ID_OCUPACION
ORDER BY A.ID_SUCURSAL, B.nombre ASC;

--------------EMPLEADOS QUE HALLAN REALIZADO VENTAS----------------
CREATE OR REPLACE VIEW EMPLEADOS_VENTAS AS SELECT
A.Lugar, (B.Nombre ||' '||B.Apellido)as Empleado, C.Puesto, COUNT(D.ID_VENTA) AS CANT_VENTAS, SUM(D.MONTOTOTAL) AS TOTALVENTAS
FROM EMPLEADO B
JOIN SUCURSAL A ON B.ID_SUCURSAL = A.ID_SUCURSAL
JOIN OCUPACION C ON B.ID_OCUPACION = C.ID_OCUPACION
JOIN VENTA D ON B.ID_EMPLEADO = D.ID_EMPLEADO
WHERE D.ESTADO = 'Terminada'
GROUP BY A.Lugar, B.nombre, B.Apellido, B.Edad, C.Puesto, A.ID_SUCURSAL
ORDER BY A.ID_SUCURSAL, B.nombre ASC;
