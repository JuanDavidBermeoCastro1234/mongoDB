# Mis Recursos App - Gestor de Series, Películas y Libros

## Descripción del Proyecto

Aplicación web para gestionar el progreso en el consumo de contenido multimedia (series, películas) y libros. Permite a los usuarios registrar, organizar y valorar sus recursos con un sistema CRUD completo.

## Estructura de la Base de Datos

### Colección: `recursos`

Cada documento contiene los siguientes campos:

- `nombre`: String (Nombre del recurso)
- `genero`: String (Género del contenido)
- `plataforma`: String (Donde se consume el recurso)
- `estado`: String ["En progreso", "Terminado", "Pendiente"]
- `formato`: String ["Serie", "Película", "Libro"]
- `fecha_terminacion`: Date (Opcional, solo si está terminado)
- `valoracion`: Number (1-5, opcional)
- `reseña`: String (Comentarios personales)
- `fecha_agregado`: Date (Fecha de creación del registro)
- `usuario_id`: String (Identificador del usuario)

Comandos para Configurar la Base de Datos
bash
# Conectar a MongoDB
mongo

# Crear/Seleccionar la base de datos
use mis_recursos_db

# Crear la colección principal
db.createCollection("recursos")

# Importar datos desde archivo JSON (ejecutar en terminal)
mongoimport --db mis_recursos_db --collection recursos --file recursos.json --jsonArray
Operaciones CRUD
Crear recursos
javascript
// Insertar un solo documento
db.recursos.insertOne({
  "nombre": "Ejemplo",
  "genero": "Drama",
  "plataforma": "Netflix",
  "estado": "En progreso",
  "formato": "Serie",
  "fecha_terminacion": null,
  "valoracion": null,
  "reseña": "Comentario inicial",
  "fecha_agregado": new Date(),
  "usuario_id": "user123"
})

// Insertar múltiples documentos
db.recursos.insertMany([{...}, {...}])
Leer recursos
javascript
// Obtener todos los recursos
db.recursos.find()

// Recursos de un usuario específico
db.recursos.find({"usuario_id": "user123"})
Actualizar recursos
javascript
// Actualizar un recurso específico
db.recursos.updateOne(
  { "nombre": "Breaking Bad", "usuario_id": "user123" },
  { 
    $set: { 
      "estado": "Terminado",
      "valoracion": 5,
      "reseña": "Obra maestra del drama televisivo" 
    } 
  }
)
Eliminar recursos
javascript
// Eliminar un recurso específico
db.recursos.deleteOne({ "nombre": "Ejemplo", "usuario_id": "user123" })
Sistema de Filtros y Búsqueda
Filtrado básico
javascript
// Por estado
db.recursos.find({ 
  "estado": "Terminado", 
  "usuario_id": "user123" 
})

// Por formato
db.recursos.find({ "formato": "Libro", "usuario_id": "user123" })

// Por plataforma
db.recursos.find({ "plataforma": "Netflix", "usuario_id": "user123" })
Búsqueda avanzada
javascript
// Búsqueda por nombre (insensible a mayúsculas)
db.recursos.find({ 
  "nombre": { "$regex": "witcher", "$options": "i" },
  "usuario_id": "user123"
})

// Recursos terminados con valoración alta
db.recursos.find({ 
  "estado": "Terminado",
  "valoracion": { "$gte": 4 },
  "usuario_id": "user123"
})
Archivos de Datos
El repositorio incluye los siguientes archivos JSON:

recursos.json: Contiene 50 registros de ejemplo con variedad de formatos, estados y plataformas para pruebas.

Validación de Datos
Aunque la validación primaria se implementa en la aplicación, se recomienda añadir las siguientes reglas de validación en MongoDB:

javascript
db.createCollection("recursos", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["nombre", "genero", "plataforma", "estado", "formato", "fecha_agregado", "usuario_id"],
      properties: {
        valoracion: {
          bsonType: "int",
          minimum: 1,
          maximum: 5,
          description: "Debe ser un entero entre 1 y 5"
        },
        estado: {
          enum: ["En progreso", "Terminado", "Pendiente"],
          description: "Debe ser uno de los estados permitidos"
        },
        formato: {
          enum: ["Serie", "Película", "Libro"],
          description: "Debe ser uno de los formatos permitidos"
        },
        fecha_terminacion: {
          bsonType: ["date", "null"],
          description: "Debe ser una fecha válida o nula"
        }
      }
    }
  }
})