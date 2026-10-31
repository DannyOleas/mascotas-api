# mascotas-api

API REST para consultar y administrar mascotas. Está construida con Node.js, Express y TypeScript, y persiste los datos en PostgreSQL mediante `pg` y un `Pool` de conexiones. Al iniciar, crea automáticamente la tabla `mascota` si todavía no existe.

## Requisitos

- Node.js y npm.
- Docker con PostgreSQL (o una instancia PostgreSQL compatible).

Para iniciar una base PostgreSQL local en Docker con los valores configurados por defecto:

```sh
docker run --name mascotas-postgres -e POSTGRES_PASSWORD=admin123 -e POSTGRES_DB=mascotas_db -p 5432:5432 -d postgres
```

## Configuración

Copia `.env.example` como `.env` y ajusta las variables según tu instancia. El archivo `.env` incluido contiene valores locales de ejemplo.

```env
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=your_password
DB_NAME=mascotas_db
PORT=4000
```

El archivo `db/mascotas_db.sql` contiene el DDL y algunos INSERT de ejemplo (perros, gatos y aves); puedes ejecutarlo manualmente si quieres cargar esos datos. La API solo crea automáticamente la tabla y no inserta semillas al arrancar.

## Ejecución

```sh
npm install
npm run dev
```

El servidor queda disponible en `http://localhost:4000`. Para compilar y ejecutar la versión compilada:

```sh
npm run build
npm start
```

## Endpoints

| Método | Ruta | Descripción |
| --- | --- | --- |
| GET | `/mascota` | Devuelve todas las mascotas. |
| GET | `/mascota/:param` | Si `:param` es un entero, busca por ID; si es texto, busca por especie sin distinguir mayúsculas/minúsculas. |
| POST | `/mascota` | Crea una mascota y responde `201`. Requiere `nombre` y `especie`. |
| PUT | `/mascota` | Actualiza una mascota. Requiere `id`, `nombre` y `especie` en el body. |
| DELETE | `/mascota` | Elimina una mascota. El ID puede venir en el body o como `?id=`. |

**Nota sobre `GET /mascota/:especie` y `GET /mascota/:id`:** ambas tienen el mismo patrón de ruta y Express no puede distinguirlas por el nombre del parámetro. Por eso se implementan en una única ruta `GET /mascota/:param`: un valor entero (por ejemplo, `/mascota/12`) se interpreta como ID y devuelve `404` si no existe; cualquier valor de texto (por ejemplo, `/mascota/perro`) se interpreta como especie y busca sin distinguir mayúsculas/minúsculas.

Ejemplos de body JSON:

Crear una mascota (`POST /mascota`):

```json
{
  "nombre": "Luna",
  "especie": "Perro",
  "raza": "Labrador",
  "edad": 4,
  "peso": 28.5
}
```

Actualizar una mascota (`PUT /mascota`):

```json
{
  "id": 1,
  "nombre": "Luna",
  "especie": "Perro",
  "raza": "Labrador",
  "edad": 5,
  "peso": 29.1
}
```

Eliminar mediante body (`DELETE /mascota`):

```json
{
  "id": 1
}
```

También se acepta `DELETE /mascota?id=1`.
