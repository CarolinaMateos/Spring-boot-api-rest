# API de Gestión de Pedidos – Spring Boot

Proyecto desarrollado como práctica de DAW para construir una API REST de gestión de pedidos, productos, categorías y usuarios utilizando **Spring Boot** y **Spring Data JPA** sobre una base de datos MySQL.

La aplicación expone distintos endpoints para:

- Gestionar **productos** y sus categorías.
- Registrar **usuarios**.
- Crear y consultar **pedidos** y sus líneas.
- Obtener algunas **consultas estadísticas** sobre ventas y stock.

---

## 🛠 Tecnologías utilizadas

- **Java 17+**
- **Spring Boot** (Web, Data JPA)
- **Spring Data JPA** (repositorios)
- **MySQL** (base de datos relacional)
- **Maven** (gestor de dependencias y build)
- **Lombok** (reducción de código boilerplate)
- Servidor embebido **Tomcat** (por defecto de Spring Boot)

---

## 🧱 Arquitectura del proyecto

El proyecto sigue una arquitectura por capas:

- **entities/**  
  Entidades JPA que representan las tablas de la base de datos:
  - `Usuario`
  - `Producto`
  - `Categoria`
  - `Pedido`
  - `LineasPedido`

- **repository/**  
  Repositorios de Spring Data JPA para acceder a la base de datos:
  - `UsuarioRepository`
  - `ProductoRepository`
  - `CategoriaRepository`
  - `PedidoRepository`
  - `LineasPedidoRepository`  
  Incluyen métodos derivados (`findBy…`) y algunas consultas personalizadas con `@Query`.

- **service/**  
  Interfaces y clases de servicio que encapsulan la lógica de negocio:
  - `ProductoService`, `CategoriaService`, `UsuarioService`, `PedidoService`, etc.
  - Implementaciones `*Impl` que usan los repositorios.

- **restcontroller/**  
  Controladores REST que exponen los endpoints HTTP:
  - `ProductosRestController`
  - `CategoriasRestController`
  - `UsuariosRestController`
  - `PedidosRestController`
  - `LineasPedidoRestController`

---

## ⚙️ Configuración y ejecución

### 1. Requisitos previos

- JDK 17 o superior instalado.
- MySQL instalado y en ejecución.
- Maven instalado (opcional, si no usas el wrapper).

### 2. Base de datos

1. Crea una base de datos en MySQL, por ejemplo:

   ```sql
   CREATE DATABASE GESTION_PEDIDOS_BBDD;
   USE GESTION_PEDIDOS_BBDD;
    CREATE TABLE USUARIOS
    ( ID_USUARIO INT auto_increment PRIMARY KEY,
    NOMBRE VARCHAR(50) NOT NULL,
    EMAIL VARCHAR(50) NOT NULL UNIQUE,
    PASSWORD VARCHAR(45) NOT NULL,
    ENABLED INT,
    ROL VARCHAR(45) NOT NULL,
    FECHA_REGISTRO DATE,
    CHECK (ENABLED IN (1,0)),
    CHECK (ROL IN ('ADMIN','CLIENTE'))
    );
    
    INSERT INTO USUARIOS VALUES
    (1,'Ramon', 'ramon@gmail.com', 'ramoncin', 1, 'ADMIN', '1997-01-15'),
    (2,'Tomas', 'tomas@gmail.com', 'tomasin', 1, 'CLIENTE', '2001-01-15'),
    (3,'Eva', 'eva@gmail.com', 'evita', 1, 'CLIENTE', '2002-01-15');
    
    CREATE TABLE CATEGORIAS
    ( ID_CATEGORIA INT auto_increment primary KEY,
    NOMBRE VARCHAR(45) NOT NULL,
    DESCRIPCION VARCHAR(250)
    );
    INSERT INTO CATEGORIAS VALUES
    (1,'Ropa', 'Prendas de Hombre mujer y niño'),
    (2,'Calzado', 'De vestir, zapatos y zapatillas'),
    (3,'Complementos', 'Cinturones, Fular, Bolsos');
    
    
    
    CREATE TABLE PRODUCTOS
    (ID_PRODUCTO INT auto_increment PRIMARY KEY,
    NOMBRE VARCHAR(250) NOT NULL,
    PRECIO DEC(11,2),
    STOCK INT,
    ID_CATEGORIA INT NOT NULL,
    FOREIGN KEY(ID_CATEGORIA) REFERENCES CATEGORIAS(ID_CATEGORIA)
    );
    INSERT INTO PRODUCTOS VALUES
    (1,'Camisa fit Talla XS de Mujer entallada', 45,50,1),
    (2,'Camisa regular  Talla M de Mujer', 75,50,1),
    (3,'Falda regular Talla M de Mujer entallada', 45,50,1),
    (4,'Camisa fit Talla L de Hombre ajustada', 145,50,1);
    
    
    
    CREATE TABLE PEDIDOS
    ( ID_PEDIDO INT auto_increment PRIMARY KEY,
    FECHA DATE,
    ID_USUARIO INT NOT NULL,
    FOREIGN KEY(ID_USUARIO) references USUARIOS(ID_USUARIO)
    );
    INSERT INTO PEDIDOS VALUES
    (1,'2024-02-12', 2),
    (2,'2024-05-02', 2),
    (3,'2024-06-07', 2),
    (4,'2024-07-15', 3),
    (5,'2024-10-12', 3);    
    
    CREATE TABLE LINEAS_PEDIDO
    ( NUMERO_ORDEN INT auto_increment PRIMARY KEY,
    ID_PEDIDO INT NOT NULL,
    ID_PRODUCTO INT NOT NULL,
    CANTIDAD INT,
    PRECIO_UNITARIO DEC(11,2),
    FOREIGN KEY(ID_PEDIDO) references PEDIDOS(ID_PEDIDO),
    FOREIGN KEY(ID_PRODUCTO) references PRODUCTOS(ID_PRODUCTO),
    UNIQUE(ID_PEDIDO, ID_PRODUCTO)
    );
    
    INSERT INTO LINEAS_PEDIDO VALUES
    (1,1,1,2,45),
    (2,1,2,2,70),
    (3,1,3,2,30),
    (4,2,1,2,45),
    (5,3,2,2,70),
    (6,4,1,2,45),
    (7,5,2,2,70),
    (8,5,4,4,145);
    
    COMMIT;
