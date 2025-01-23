# Scala Database Connectivity Guide

## 1. ¿Qué es JDBC y cuáles son sus componentes?

JDBC (Java Database Connectivity) es una API de Java que permite la conexión y ejecución de consultas a bases de datos relacionales. Sus componentes principales son:

- **DriverManager**: Gestor que carga y administra los drivers de bases de datos.
- **Connection**: Representa una sesión de conexión con una base de datos específica.
- **Statement**: Objeto utilizado para enviar consultas SQL a la base de datos.
- **PreparedStatement**: Versión optimizada de Statement que permite consultas parametrizadas.
- **ResultSet**: Representa los datos devueltos por una consulta, permitiendo navegar y recuperar información.

## 2. Librerías de Scala para Conexión a Bases de Datos Relacionales

| Característica | Slick | Doobie |
|---------------|-------|--------|
| Paradigma | Framework ORM funcional | Librería de acceso a datos funcional |
| Soporte de Bases de Datos | MySQL, PostgreSQL, H2, Oracle | MySQL, PostgreSQL, SQLite |
| Tipado | Fuertemente tipado | Fuertemente tipado con soporte para F.P. |
| Abstracción | Alto nivel de abstracción | Abstracción a nivel de consultas |
| Curva de Aprendizaje | Moderada | Empinada (requiere conocimientos de Cats) |
| Compilación de Consultas | En tiempo de compilación | En tiempo de compilación |
| Transacciones | Soporte nativo | Soporte nativo con monads |

## 3. Conexión a Base de Datos MySQL con Scala

### Preparación del Entorno

1. **Dependencias requeridas** (build.sbt):
```scala
    libraryDependencies ++= Seq(
      "mysql" % "mysql-connector-java" % "8.0.27",
      "org.scalikejdbc" %% "scalikejdbc" % "4.0.0-RC1",
      "ch.qos.logback" % "logback-classic" % "1.2.6"
    )
```

### Pasos de Configuración

#### 3.1 Crear Base de Datos MySQL
```sql
-- Crear base de datos
CREATE DATABASE estudiantes_db;

-- Usar base de datos
USE estudiantes_db;

-- Crear tabla de ejemplo
CREATE TABLE estudiantes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100),
    edad INT,
    carrera VARCHAR(100)
);

-- Insertar datos de prueba
INSERT INTO estudiantes (nombre, edad, carrera) VALUES 
('Juan Pérez', 22, 'Ingeniería de Sistemas'),
('María García', 20, 'Ciencias de la Computación');
```

#### 3.2 Conexión con ScalikeJDBC en Scala
```scala
import scalikejdbc._

object database {
  def main(args: Array[String]): Unit = {
    Class.forName("com.mysql.cj.jdbc.Driver")
    ConnectionPool.singleton(
      "jdbc:mysql://localhost:3306/estudiantes_db",
      "root",
      "aj3427kc"
    )

    DB.readOnly { implicit session =>
      val estudiantes = SQL("SELECT * FROM estudiantes")
        .map(rs => (
          rs.int("id"),
          rs.string("nombre"),
          rs.int("edad"),
          rs.string("carrera")
        )).list()

      estudiantes.foreach { estudiante =>
        println(s"ID: ${estudiante._1}, Nombre: ${estudiante._2}, Edad: ${estudiante._3}, Carrera: ${estudiante._4}")
      }
    }
  }
}
```

### Consideraciones de Seguridad

- Nunca incluyas credenciales directamente en el código
- Utiliza variables de entorno o archivos de configuración externos
- Implementa principios de mínimo privilegio en los usuarios de base de datos

## Recursos Adicionales

- [Documentación oficial de ScalikeJDBC](http://scalikejdbc.org/)
- [Guía de JDBC de Oracle](https://docs.oracle.com/javase/tutorial/jdbc/overview/index.html)

## Licencia

Este proyecto está bajo licencia MIT.