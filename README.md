# golang-academy-db

API REST de ejemplo escrita en [Go](https://go.dev/) que implementa un servicio de carritos de compra (`carts`) con persistencia en archivo. El proyecto fue creado como ejercicio práctico para la academia de Go de Globant y muestra una arquitectura por capas (router → service → model/db) sencilla y fácil de seguir.

## Características

- API REST sobre HTTP usando [`httprouter`](https://github.com/julienschmidt/httprouter).
- Arquitectura desacoplada en capas: `router`, `service`, `model` y `db`.
- Dos implementaciones de almacenamiento intercambiables:
  - `FileDb`: persistencia en un archivo local (`filedb.dat`).
  - `InMemoryDb`: almacenamiento en memoria, útil para pruebas.
- Apagado controlado del servidor mediante `SIGINT` (Ctrl+C).
- Respuestas en JSON con un formato uniforme (`ApiResponse`).

## Estructura del proyecto

```
.
├── main.go                 # Punto de entrada: configura DB, servicio, router y servidor HTTP
├── go.mod
├── db/                     # Capa de persistencia (FileDb, InMemoryDb)
├── model/                  # Structs del dominio y adaptadores hacia la DB
├── router/                 # Definición de rutas y handlers HTTP
├── service/                # Lógica de negocio
└── util/                   # Utilidades comunes
```

## Requisitos

- Go 1.15 o superior.

## Instalación y ejecución

Clona el repositorio y descarga las dependencias:

```sh
git clone https://github.com/srobles-globant/golang-academy-db.git
cd golang-academy-db
go mod download
```

Ejecuta el servidor:

```sh
go run .
```

El servidor quedará escuchando en el puerto `8080`. Para detenerlo, presiona `Ctrl+C`; el shutdown es controlado con un timeout de 5 segundos.

## Pruebas

```sh
go test ./...
```

## Endpoints

Base URL: `http://localhost:8080`

| Método | Ruta                          | Descripción                                  |
| ------ | ----------------------------- | -------------------------------------------- |
| POST   | `/carts`                      | Crea un nuevo carrito                        |
| GET    | `/carts/:cart`                | Obtiene un carrito por su ID                 |
| POST   | `/carts/:cart/items`          | Agrega items al carrito                      |
| GET    | `/carts/:cart/items`          | Lista los items del carrito                  |
| PATCH  | `/carts/:cart/items/:item`    | Cambia la cantidad de un item                |
| DELETE | `/carts/:cart/items/:item`    | Elimina un item del carrito                  |
| DELETE | `/carts/:cart/items`          | Vacía el carrito (elimina todos los items)   |

La documentación completa de la API (con ejemplos de requests y responses) está disponible en Postman:

https://documenter.getpostman.com/view/4345481/TVCjx62o

### Ejemplos rápidos con `curl`

Crear un carrito:

```sh
curl -X POST http://localhost:8080/carts \
  -H "Content-Type: application/json" \
  -d '{"owner":"sergio"}'
```

Agregar items a un carrito:

```sh
curl -X POST http://localhost:8080/carts/1/items \
  -H "Content-Type: application/json" \
  -d '[{"articleId":10,"quantity":2}]'
```

Listar items de un carrito:

```sh
curl http://localhost:8080/carts/1/items
```

Cambiar la cantidad de un item:

```sh
curl -X PATCH http://localhost:8080/carts/1/items/1 \
  -H "Content-Type: application/json" \
  -d '{"quantity":5}'
```

## Modelo de datos

```go
type Cart struct {
    ID    int    `json:"id"`
    Owner string `json:"owner"`
    Items []Item `json:"items"`
}

type Item struct {
    ID        int `json:"id"`
    ArticleID int `json:"articleId"`
    Quantity  int `json:"quantity"`
}
```

Todas las respuestas siguen el formato:

```json
{
  "message": "string",
  "data": { }
}
```

## Licencia

Este proyecto se distribuye bajo la licencia MIT. Consulta el archivo [LICENSE](LICENSE) para más información.