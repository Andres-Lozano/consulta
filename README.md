# Gestión de Proyectos de Investigación

## Configuración del Proyecto (build.sbt)
```scala
ThisBuild / version := "0.1.0-SNAPSHOT"
ThisBuild / scalaVersion := "3.3.1"

lazy val root = (project in file("."))
  .settings(
    name := "proyectos-investigacion",
    libraryDependencies ++= Seq(
      "mysql" % "mysql-connector-java" % "8.0.27",
      "org.scalikejdbc" %% "scalikejdbc" % "4.0.0-RC1",
      "ch.qos.logback" % "logback-classic" % "1.2.6"
    )
  )
```

## Configuración Base de Datos MySQL
```sql
CREATE DATABASE centro_investigacion;
USE centro_investigacion;

CREATE TABLE investigadores (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100),
    area_investigacion VARCHAR(100),
    publicaciones INT,
    financiamiento DECIMAL(10,2)
);

INSERT INTO investigadores (nombre, area_investigacion, publicaciones, financiamiento) VALUES 
('Dr. Raúl Fernández', 'Biotecnología', 12, 250000.75),
('Dra. Sofía Ramírez', 'Inteligencia Artificial', 18, 300000.50);
```

## Código de Conexión
```scala
import scalikejdbc._

object GestionInvestigadores {
  def main(args: Array[String]): Unit = {
    Class.forName("com.mysql.cj.jdbc.Driver")
    ConnectionPool.singleton(
      "jdbc:mysql://localhost:3306/centro_investigacion",
      "root",
      "tu_contraseña"
    )

    DB.readOnly { implicit session =>
      val investigadores = SQL("SELECT * FROM investigadores")
        .map(rs => (
          rs.int("id"),
          rs.string("nombre"),
          rs.string("area_investigacion"),
          rs.int("publicaciones"),
          rs.double("financiamiento")
        )).list()

      investigadores.foreach { investigador =>
        println(s"ID: ${investigador._1}, " +
                s"Nombre: ${investigador._2}, " +
                s"Área: ${investigador._3}, " +
                s"Publicaciones: ${investigador._4}, " +
                f"Financiamiento: $${investigador._5}%.2f")
      }
    }
  }
}
```

## Consideraciones
- Reemplazar contraseña de MySQL
- Verificar conexión al servidor
- Instalar dependencias con `sbt update`
