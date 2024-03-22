## Brian Emmanuel Flores Hernandez

### Tipos de datos en MongoDB
- Double
- String
- Object
- Array
- Binary Data
- Undifined -> Deprecated
- ObjectID
- Boolean
- Regular Expresion
- JavaScript
- Int
- Long
- Timestmp
- Biginter

## Comandos Básicos de MongoDB

#### **Ver las bases de datos**
- show dbs

#### **Crear o cambiar base de datos **
- use db

#### **Para ver las colecciones**
- use db
- show Colections

#### **Crear una coleccion**
```json
db.createColection('nombre_de_la_coleccion')
```

#### **Insertar un solo documento a una collection**
```json
db.empleado.insertOne({
    nombre: "Roman",
    edad:10
})
```

#### **Visualizar todos los documentos de la coleccion.**
```json
db.empleado.find({'condicion'})
```
#### **Insertar varios documentos a una coleccion**
```json
 db.empleado.insertMany(
     [
     {
     nombre:"Hermerejilgo",
     edad:67,
     },
     {
     nombre:"Soy la vaca del corral",
     edad:14,
     sexo:"Mujer"
    },
    {
    nombre:"Romulo",
    edad:40,
    nacionalidad:"Nigeriano"
    },
    {
    nombre:"Cristian Andres",
    localidad:"San Miguel de las Piedras",
    nacionalidad:"Aleman",
    edad:"esta morro"
    }
    ]
    )
```

#### **Eliminar una base de datos**
json
db.dropDataBase('')


#### **Limpiar la pantalla de mongo shell:**
- cls

#### **Eliminar varios documentos**
json
db.libros.deleteMany( { cantidad: { $gt: 20 } })


#### **Eliminar un solo documento**
json
db.libros.deleteOne({"_id":13})


#### **Reemplaza un documento**
json
db.libros.replaceOne( { "_id": 13 }, { "_id": 13, "titulo": "Las patoaventuras de Ezequiel alias Mateo" })

#### **Actualizar varios documentos**
json
db.libros.updateMany( { cantidad: { $gt: 20 } }, { $mul: { precio: 2 } })


#### **Actualizar un solo documento**
json
db.libros.updateOne( {_id:13},{$set:{precio:277}},{$set:{cantidad:50}})

---

# AGREGACIONES

### Tipos de agregaciones
- `$match`: Filtra los documentos de una colección para procesar solo aquellos que cumplan con ciertas condiciones.
- `$project`: Permite modificar la estructura de los documentos de salida, incluyendo o excluyendo campos específicos, creando nuevos campos calculados, o renombrando campos existentes.
- `$sort`: Ordena los documentos de una colección según un criterio especificado.
- `$unset`: Elimina campos específicos de los documentos de una colección.
- `$set`: Modifica o agrega campos específicos en los documentos de una colección.
- `$out`: Escribe el resultado de una etapa de agregación en una colección nueva o existente.
- `$group`: Agrupa los documentos de una colección según un campo específico y realiza operaciones de agregación en los datos agrupados.

---
#### **Agregación con 3 Stages**
1. $match
2. $project
3. $sort
```json
[
    {
      $match:
        {
          editorial: "Biblio"
        }
    },
    {
      $project:
        {
          _id: 0,
          titulo: 1,
          precio: 1,
          cantidad: 1,
          "Nombre Editorial": "$editorial",
          "Total Ganancia": {
            $multiply: ["$precio", "$cantidad"]
          }
        }
    },
    {
      $sort:
        {
          precio: 1
        }
    }
]
```

#### **Agregación con 2 Stages**
1. $group
2. $sort
```json
[
    {
      $group:
        /**
         * _id: The id of the group.
         * fieldN: The first field name.
         */
        {
          _id: "$editorial",
          "Numero Documentos": {
            $count: {},
          },
        },
    },
    {
      $sort:
        /**
         * Provide any number of field/order pairs.
         */
        {
          "Numero Documentos": -1,
        },
    },
  ]
```

#### **Agregación con 1 Stage**
1. $group
```json
  [
    {
      $group:
        /**
         * _id: The id of the group.
         * fieldN: The first field name.
         */
        {
          _id: "$editorial",
          "Numero de Documentos": {
            $count: {},
          },
          media: {
            $avg: "$precio",
          },
        },
    },
  ]
```

#### **Agregación con 3 Stages**
1. $group
2. $set
3. $out
```json
  [
    {
      $group:
        /**
         * _id: The id of the group.
         * fieldN: The first field name.
         */
        {
          _id: "$editorial",
          "Numero de Documentos": {
            $count: {},
          },
          media: {
            $avg: "$precio",
          },
        },
    },
    {
      $set:
        /**
         * field: The field name
         * expression: The expression.
         */
        {
          "Media Total": {
            $trunc: ["$media", 2],
          },
        },
    },
    {
      $out:
        /**
         * Provide the name of the output collection.
         */
        "Media_Editoriales",
    },
  ]
```

#### **Agregación con 3 Stages**
1. $group
2. $set
3. $unset
```json
  [
    {
      $group:
        /**
        * _id: The id of the group.
        * fieldN: The first field name.
        */
        {
          _id: "$editorial",
          "Numero de Documentos": {
            $count: {},
          },
          media: {
            $avg: "$precio",
          },
        },
    },
    {
      $set:
        /**
        * field: The field name
        * expression: The expression.
        */
        {
          "Media Total": {
            $trunc: ["$media", 2],
          },
        },
    },
    {
      $unset:
        /**
        * Provide the name of the output collection.
        */
        "media",
    },
  ]
  ```
---

# BUSQUEDAS CONDICIONALES

### CONSULTAS

#### **Consultas con selectores logicos**
1. $and y $or  ->   $eq y $gt
```json
db.libros.fin({ $or: [ { $and: [{ editorial: { $eq: 'Biblio' } }, { precio: { $gt: 40 } }] }, { $and: [{ precio: { $gt: 30 } }, { editorial: { $eq: 'Planeta' } }] }] })
```
2. $or  ->  $eq y $lt
```json
db.libros.find({$or:[{titulo:{$eq:"MongoDB para novatos"}},{cantidad:{$lt:15}}]})
```
3. $and ->  $eq
```json
db.libros.find({$and:[{titulo:{$eq:"MongoDB para novatos"}},{editorial:{$eq:"Biblio"}}]})
```
4. $and y $or   ->  $eq y $gt
```json
db.libros.find({ $or: [ { $and: [{ editorial: { $eq: 'Biblio' } }, { precio: { $gt: 10 } }] }, { $and: [{ precio: { $gt: 30 } }, { editorial: { $eq: 'Planeta' } }] }] }, { titulo:1, cantidad:1, precio:1 ,id_:0})
```
5. $and y $or
```json
db.libros.fin({ $or: [ { $and: [{ editorial: { $eq: 'Biblio' } }, { precio: { $gt: 40 } }] }, { $and: [{ precio: { $gt: 30 } }, { editorial: { $eq: 'Planeta' } }] }] })
```

#### **Visualizar los documentos de la coleccion según las condiciones o filtros de las consultas.**
```json
db.empleado.find({$clave:{$condicion:}})
```
#### **Mostrar el primer documento encontrado en la base de datos**
```json
db.empleado.findOne({})
```

#### **Consulta con limit**
Lo que hace el *limit* es mostrar los primeros documentos que encuentra segun nuestras necesidades. 
```json
db.libros.find({}, { "titulo": 1, "precio": 1, "_id": 0, "editorial": 1 }).limit(2)
```
#### **Consulta con sort**
Lo que hace el *sort* es ordenar los documentos.
- -1 es Descendente 
- 1 es Ascendente
En este caso los ordena por el titulo de manera ascendente.
```json
db.libros.find({}, { "titulo": 1, "precio": 1, "_id": 0, "editorial": 1 }).skip(3).sort({"titulo":1})
```

#### **Consulta con size**
Lo que hace el *size* es decirnos el número de documentos que hay según la consulta. 
```json
db.libros.find({}, { "titulo": 1, "precio": 1, "_id": 0, "editorial": 1 }).size()
```

#### **Consulta con skip**
Lo que hace el *skip* es mostrar algunos documentos, lo que hace es no mostrar los documentos que pongamos, en este ejemplo *muestra del cuarto documento en adelante*, no muestra los primeros 3. 
```json
db.libros.find({}, { "titulo": 1, "precio": 1, "_id": 0, "editorial": 1 }).skip(3)
```

#### **Consulta con exists**
Lo que hace el *exists* es mostrar todos los documentos que contengan el campo. 
```json
db.libros.find({titulo:{$exists:true}})
```

---

# JSON INSERTAR EMPLEADOS
	```json
	{
		"nombre": "Gregory",
		"apellidos": "Juarez",
		"correo": "nisi.mauris.nulla@google.edu",
		"direccion": "2727 Nec, St.",
		"region": "Mizoram",
		"pais": "Norway",
		"empresa": "Google",
		"ventas": 26890,
		"salario": 3265,
		"departamentos": "Legal Department, Accounting, Media Relations, Research and Development"
	}
	{
		"nombre": "Candace",
		"apellidos": "Buck",
		"correo": "donec.dignissim@google.ca",
		"direccion": "Ap #559-7631 Donec Road",
		"region": "Møre og Romsdal",
		"pais": "India",
		"empresa": "Google",
		"ventas": 1023,
		"salario": 6657,
		"departamentos": "Asset Management, Sales and Marketing, Media Relations"
	}
	{
		"nombre": "Lee",
		"apellidos": "Lyons",
		"correo": "ipsum.cursus@google.ca",
		"direccion": "5505 Aliquet St.",
		"region": "Prince Edward Island",
		"pais": "Peru",
		"empresa": "Borland",
		"ventas": 33530,
		"salario": 8245,
		"departamentos": "Asset Management, Advertising, Human Resources, Tech Support, Payroll"
	}
	{
		"nombre": "Imogene",
		"apellidos": "Nolan",
		"correo": "montes@protonmail.com",
		"direccion": "348-6070 Libero. Avenue",
		"region": "Bicol Region",
		"pais": "Costa Rica",
		"empresa": "Amazon",
		"ventas": 938,
		"salario": 6892,
		"departamentos": "Payroll, Public Relations, Customer Service, Advertising"
	}
	{
		"nombre": "Jana",
		"apellidos": "Sykes",
		"correo": "sed.dolor.fusce@protonmail.com",
		"direccion": "782-7657 Mauris Rd.",
		"region": "Wyoming",
		"pais": "Belgium",
		"empresa": "Yahoo",
		"ventas": 14292,
		"salario": 3912,
		"departamentos": "Customer Relations, Finances, Sales and Marketing, Media Relations"
	}
	{
		"nombre": "Olympia",
		"apellidos": "Ruiz",
		"correo": "accumsan.interdum@icloud.org",
		"direccion": "2330 Cursus Rd.",
		"region": "Catalunya",
		"pais": "Indonesia",
		"empresa": "Finale",
		"ventas": 37972,
		"salario": 8513,
		"departamentos": "Asset Management, Media Relations, Human Resources, Research and Development"
	}
	{
		"nombre": "Sopoline",
		"apellidos": "Cummings",
		"correo": "sed.sapien@protonmail.ca",
		"direccion": "689-6101 Iaculis Ave",
		"region": "Cantabria",
		"pais": "Canada",
		"empresa": "Borland",
		"ventas": 20335,
		"salario": 5504,
		"departamentos": "Accounting, Customer Service, Public Relations, Media Relations, Payroll"
	}
	{
		"nombre": "Omar",
		"apellidos": "Gentry",
		"correo": "non.egestas.a@outlook.edu",
		"direccion": "108-4018 Sit Rd.",
		"region": "Manitoba",
		"pais": "Australia",
		"empresa": "Google",
		"ventas": 2151,
		"salario": 1405,
		"departamentos": "Human Resources, Payroll, Research and Development, Media Relations"
	}
	{
		"nombre": "Kieran",
		"apellidos": "Saunders",
		"correo": "egestas@yahoo.edu",
		"direccion": "938-211 In Ave",
		"region": "Luxemburg",
		"pais": "Pakistan",
		"empresa": "Google",
		"ventas": 39042,
		"salario": 3244,
		"departamentos": "Human Resources, Finances, Advertising, Legal Department, Customer Service"
	}
	{
		"nombre": "Arthur",
		"apellidos": "Hyde",
		"correo": "libero.at@outlook.edu",
		"direccion": "429-2910 Purus Road",
		"region": "Goiás",
		"pais": "France",
		"empresa": "Chami",
		"ventas": 11103,
		"salario": 3180,
		"departamentos": "Asset Management, Customer Relations, Customer Service, Accounting"
	}
	{
		"nombre": "Philip",
		"apellidos": "Herrera",
		"correo": "sit.amet@yahoo.org",
		"direccion": "898-9792 Arcu. Ave",
		"region": "Argyllshire",
		"pais": "Costa Rica",
		"empresa": "Amazon",
		"ventas": 5571,
		"salario": 3873,
		"departamentos": "Legal Department, Customer Relations, Tech Support, Advertising, Asset Management"
	}
	{
		"nombre": "Heidi",
		"apellidos": "Thornton",
		"correo": "nulla.donec@yahoo.edu",
		"direccion": "Ap #901-8129 Sodales. St.",
		"region": "Azad Kashmir",
		"pais": "Turkey",
		"empresa": "Finale",
		"ventas": 38009,
		"salario": 5507,
		"departamentos": "Payroll, Advertising, Finances, Legal Department, Human Resources"
	}
	{
		"nombre": "Jameson",
		"apellidos": "Velazquez",
		"correo": "porttitor.vulputate@protonmail.net",
		"direccion": "P.O. Box 571, 8725 Orci Avenue",
		"region": "Gävleborgs län",
		"pais": "Pakistan",
		"empresa": "Google",
		"ventas": 2543,
		"salario": 8847,
		"departamentos": "Public Relations, Advertising, Customer Relations, Human Resources"
	}
	{
		"nombre": "Meghan",
		"apellidos": "Adams",
		"correo": "auctor.non@hotmail.edu",
		"direccion": "P.O. Box 170, 219 Dignissim St.",
		"region": "Bolívar",
		"pais": "Italy",
		"empresa": "Chami",
		"ventas": 33227,
		"salario": 3675,
		"departamentos": "Sales and Marketing, Human Resources, Quality Assurance, Payroll"
	}
	{
		"nombre": "Jaime",
		"apellidos": "Mejia",
		"correo": "sagittis.placerat.cras@icloud.ca",
		"direccion": "P.O. Box 395, 5762 Auctor Rd.",
		"region": "Troms og Finnmark",
		"pais": "Ukraine",
		"empresa": "Adobe",
		"ventas": 31024,
		"salario": 8110,
		"departamentos": "Advertising, Payroll, Tech Support"
	}
	{
		"nombre": "Anika",
		"apellidos": "Russo",
		"correo": "luctus@protonmail.com",
		"direccion": "P.O. Box 954, 2636 A St.",
		"region": "North Region",
		"pais": "Belgium",
		"empresa": "Borland",
		"ventas": 13004,
		"salario": 6600,
		"departamentos": "Asset Management, Quality Assurance, Media Relations, Research and Development"
	}
	{
		"nombre": "Alexis",
		"apellidos": "Phelps",
		"correo": "libero.integer.in@aol.net",
		"direccion": "Ap #779-6571 Mauris. St.",
		"region": "Calabarzon",
		"pais": "Australia",
		"empresa": "Cakewalk",
		"ventas": 23767,
		"salario": 7945,
		"departamentos": "Asset Management, Advertising, Media Relations"
	}
	{
		"nombre": "Hadassah",
		"apellidos": "Whitney",
		"correo": "eget.ipsum@outlook.com",
		"direccion": "4588 Donec St.",
		"region": "Central Visayas",
		"pais": "Russian Federation",
		"empresa": "Apple",
		"ventas": 8183,
		"salario": 5504,
		"departamentos": "Accounting, Asset Management, Human Resources, Finances"
	}
	{
		"nombre": "Basil",
		"apellidos": "Owens",
		"correo": "varius@protonmail.ca",
		"direccion": "767-1617 Nulla Road",
		"region": "Wielkopolskie",
		"pais": "Sweden",
		"empresa": "Amazon",
		"ventas": 25059,
		"salario": 8816,
		"departamentos": "Accounting, Payroll, Legal Department, Human Resources, Public Relations"
	}
	{
		"nombre": "Brady",
		"apellidos": "Castillo",
		"correo": "suscipit.nonummy.fusce@yahoo.com",
		"direccion": "1321 Suspendisse Ave",
		"region": "Magallanes y Antártica Chilena",
		"pais": "Italy",
		"empresa": "Google",
		"ventas": 14054,
		"salario": 5272,
		"departamentos": "Quality Assurance, Human Resources, Advertising, Sales and Marketing"
	}
	```
---
