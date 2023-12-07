#  Examen Ruta de viajes

## Diagrama entidad-relacion

![Tablero en blanco](https://github.com/sebasrb08/Examen_Mysql/assets/118924181/b8bb8f53-5273-4b39-a669-275c1e059150)


## Diagrama Relacional

![drawSQL-rutas-de-viaje-export-2023-12-07](https://github.com/sebasrb08/Examen_Mysql/assets/118924181/48a23b9f-1657-45b8-a5fb-7714fd0f1982)


## Creacion de bases de datos
````
CREATE TABLE zona(
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre_zona VARCHAR(50)
);

CREATE TABLE rutas(
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50),
    tiempo TIME,
    id_zona INT,
    FOREIGN KEY rutas(id_zona) REFERENCES zona(id)
);

CREATE TABLE estaciones(
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50)
);

CREATE TABLE estacion_ruta(
    id INT PRIMARY KEY AUTO_INCREMENT,
    id_ruta INT,
    id_estacion INT,
    FOREIGN KEY (id_ruta) REFERENCES rutas(id),
    FOREIGN KEY (id_estacion) REFERENCES estaciones(id)
);

CREATE TABLE buses(
    id INT PRIMARY KEY AUTO_INCREMENT,
    placa VARCHAR(50)
);

CREATE TABLE conductor(
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50)
);

CREATE TABLE dia(
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(30),
    id_conductor INT,
    id_buses INT,
    id_ruta INT,
    FOREIGN KEY (id_conductor) REFERENCES conductor(id),
    FOREIGN KEY (id_buses) REFERENCES buses(id),
    FOREIGN KEY (id_ruta) REFERENCES rutas(id)
);
````

## Insercion de bases de datos

````
INSERT INTO zona (nombre_zona)
VALUES ("Norte"),("Sur"),("Oriente"),("Occidente"),
("Floridablanca"),("Giron"),("Piedecuesta")


INSERT INTO rutas (nombre,tiempo,id_zona)
VALUES ("Universidades","02:00:00",1), ("Café Madrid","02:15:00",1), ("Cacique","01:45:00",NULL),
("Diamantes","01:50:00",4), ("Terminal","02:00:00",4), ("Prado","01:30:00",NULL), ("Cabecera","01:30:00",NULL), 
("Ciudadela","02:00:00",NULL), ("Punta Estrella","02:30:00",NULL), ("Niza","02:45:00",5), ("Autopista","02:15:00",5), 
("Lagos","02:15:00",5), ("Centro Florida","02:30:00",NULL);


INSERT INTO estaciones(nombre)
VALUES ("Colseguros"), ("Clínica Chicamocha"), 
("Plaza Guarín"), ("Mega Mall"), ("UIS"), ("UDI"), 
("Santo Tomás"), ("Boulevard Santander"), ("Búcaros"), 
("Rosita"), ("Puerta del Sol"), ("Cacique"),
("Plaza Satélite"), ("La Sirena"), ("Provenza"), 
("Fontana"), ("Gibraltar"), ("Terminal"), ("Mutis"),
("Plaza Real");


INSERT INTO buses (placa)
VALUES
("XVH345"),
("XDL965"),
("XFG847"),
("XRJ452"),
("XDF459"),
("XET554"),
("XKL688"),
("XXL757");

INSERT INTO conductor(nombre)
VALUES
("Andrés Manuel López Obrador"),
("Nicolás Maduro Moros"),
("Alberto Fernández"),
("Luiz Inácio Lula da Silva"),
("Gabriel Boric"),
("Miguel Díaz-Canel"),
("Daniel Ortega"),
("Gustavo Petro Urrego"),
("Luis Arce"),
("Xiomara Castro");

INSERT INTO estacion_ruta(id_ruta,id_estacion)
VALUES(1,1),(1,2),(1,3),(1,4),(1,5),(1,6),(1,7),
(3,8),(3,9),(3,10),(3,11),(3,12),(4,13),(4,14),
(4,15),(5,16),(5,17),(8,18),(8,19),(8,20)

INSERT INTO dia(nombre,id_conductor,id_buses,id_ruta)
VALUES("Lunes",5,1,1),("Martes",5,1,1),("Miercoles",5,3,1),
("Jueves",5,3,1),("Viernes",5,5,2),("Sabado",5,5,2),
("Domingo",5,5,2),("Lunes",3,5,4),("Martes",5,6,4),
("Miercoles",5,1,4),("Jueves",5,1,5),("Viernes",5,3,4),
("Sabado",5,3,4),("Domingo",5,3,4),("Lunes",10,3,10),
("Martes",10,3,10),("Miercoles",10,5,10),("Jueves",10,5,10),
("Viernes",10,4,10),("Sabado",10,7,11),("Domingo",10,7,11),
("Lunes",7,7,11),("Martes",7,7,11),("Miercoles",6,7,12),
("Jueves",6,7,12),("Viernes",6,7,12),("Sabado",6,6,12),
("Domingo",6,6,12)
````

## SCRIPT

1 . Cantidad de Paradas por Ruta

````
SELECT rutas.nombre,COUNT(id_estacion) as cantidad_estaciones 
FROM estacion_ruta
INNER JOIN rutas
ON estacion_ruta.id_ruta=rutas.id 
GROUP BY id_ruta;
````
Respuesta

````
+---------------+---------------------+
| nombre        | cantidad_estaciones |
+---------------+---------------------+
| Universidades |                   7 |
| Cacique       |                   5 |
| Diamantes     |                   3 |
| Terminal      |                   2 |
| Ciudadela     |                   3 |
+---------------+---------------------+
````

2 . Nombre de las Paradas de la Ruta Universidades

````
SELECT e.nombre
FROM estacion_ruta as er
INNER JOIN estaciones as e
ON er.id_estacion = e.id
INNER JOIN rutas
ON er.id_ruta = rutas.id
WHERE rutas.nombre = "Universidades"
````
Respuesta

````
+---------------------+
| nombre              |
+---------------------+
| Colseguros          |
| Clínica Chicamocha  |
| Plaza Guarín        |
| Mega Mall           |
| UIS                 |
| UDI                 |
| Santo Tomás         |
+---------------------+
````

3 . Nombres de las Rutas No Programadas

````
SELECT DISTINCT rutas.nombre
FROM rutas
WHERE id NOT IN(SELECT id_ruta FROM estacion_ruta WHERE id_ruta IS NOT NULL)
````

Respuesta

````
+----------------+
| nombre         |
+----------------+
| Café Madrid    |
| Prado          |
| Cabecera       |
| Punta Estrella |
| Niza           |
| Autopista      |
| Lagos          |
| Centro Florida |
+----------------+
````

5 Conductores No Asignados a la Programación

````
SELECT nombre
FROM conductor
WHERE id NOT IN(SELECT id_conductor FROM dia WHERE id_conductor IS NOT NULL)
````

Respuesta

````
+-------------------------------+
| nombre                        |
+-------------------------------+
| Andrés Manuel López Obrador   |
| Nicolás Maduro Moros          |
| Luiz Inácio Lula da Silva     |
| Gustavo Petro Urrego          |
| Luis Arce                     |
+-------------------------------+
````

6 Buses No asignados a la Programación

````
SELECT placa
FROM buses
WHERE id NOT IN(SELECT id_buses FROM dia WHERE id_buses IS NOT NULL)
````

Respuesta

````
+--------+
| placa  |
+--------+
| XDL965 |
| XXL757 |
+--------+
````

8 Programación asignada a cada conductor (Conductor, Ruta y Día)

````
SELECT co.nombre,rutas.nombre,dia.nombre
FROM dia
INNER JOIN conductor as co
ON dia.id_conductor = co.id
INNER JOIN rutas
ON dia.id_ruta = rutas.id
````

Respuesta 

````
+--------------------+---------------+-----------+
| nombre             | nombre        | nombre    |
+--------------------+---------------+-----------+
| Gabriel Boric      | Universidades | Lunes     |
| Gabriel Boric      | Universidades | Martes    |
| Gabriel Boric      | Universidades | Miercoles |
| Gabriel Boric      | Universidades | Jueves    |
| Gabriel Boric      | Café Madrid   | Viernes   |
| Gabriel Boric      | Café Madrid   | Sabado    |
| Gabriel Boric      | Café Madrid   | Domingo   |
| Alberto Fernández  | Diamantes     | Lunes     |
| Gabriel Boric      | Diamantes     | Martes    |
| Gabriel Boric      | Diamantes     | Miercoles |
| Gabriel Boric      | Terminal      | Jueves    |
| Gabriel Boric      | Diamantes     | Viernes   |
| Gabriel Boric      | Diamantes     | Sabado    |
| Gabriel Boric      | Diamantes     | Domingo   |
| Xiomara Castro     | Niza          | Lunes     |
| Xiomara Castro     | Niza          | Martes    |
| Xiomara Castro     | Niza          | Miercoles |
| Xiomara Castro     | Niza          | Jueves    |
| Xiomara Castro     | Niza          | Viernes   |
| Xiomara Castro     | Autopista     | Sabado    |
| Xiomara Castro     | Autopista     | Domingo   |
| Daniel Ortega      | Autopista     | Lunes     |
| Daniel Ortega      | Autopista     | Martes    |
| Miguel Díaz-Canel  | Lagos         | Miercoles |
| Miguel Díaz-Canel  | Lagos         | Jueves    |
| Miguel Díaz-Canel  | Lagos         | Viernes   |
| Miguel Díaz-Canel  | Lagos         | Sabado    |
| Miguel Díaz-Canel  | Lagos         | Domingo   |
+--------------------+---------------+-----------+
````

9 Programación asignada a conductores que hacen rutas de más de dos horas

````
SELECT co.nombre
FROM dia
INNER JOIN conductor as co
ON dia.id_conductor = co.id
INNER JOIN rutas
ON dia.id_ruta = rutas.id
WHERE rutas.tiempo > "2:00:00"
````

Respuesta

````
+--------------------+
| nombre             |
+--------------------+
| Gabriel Boric      |
| Gabriel Boric      |
| Gabriel Boric      |
| Xiomara Castro     |
| Xiomara Castro     |
| Xiomara Castro     |
| Xiomara Castro     |
| Xiomara Castro     |
| Xiomara Castro     |
| Xiomara Castro     |
| Daniel Ortega      |
| Daniel Ortega      |
| Miguel Díaz-Canel  |
| Miguel Díaz-Canel  |
| Miguel Díaz-Canel  |
| Miguel Díaz-Canel  |
| Miguel Díaz-Canel  |
+--------------------+
````



