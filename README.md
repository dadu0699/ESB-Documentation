# ESB Documentation

## MicroServicios

- [Cliente](#cliente)
- [Equipo](#equipo)
- [Partido](#partido)
- [Competencia](#competencia)
- [Estadio](#estadio)
- [Director Técnico](#director-técnico)
- [Jugador](#jugador)
- [Noticia](#noticia)
- [Arbitro](#arbitro)
- [Predicción](#predicción)
- [Autenticación](#autenticación)
- [Administrador](#administrador)
- [Empleado](#empleado)
- [País](#país)

## Consideraciones Generales

- Utilizar la estructura y contenido siguiente para la tabla [Paises](./script/DML_paises.sql)
- Enviar fechas, formato `yyyy-mm-dd`
- Todas las imágenes se envían en base64 _Cadena completa_ `data:image/png;base64,iVBORw0KGgo...`

### Estructura de Endpoint

`http://ip:puerto/esb/<<ruta específica>>`

```json
{
  "status": number,
  "msg": "string",
  "data": [array]
}
```

```js
// Implementación a nivel código
res.status(status_number).send({
  status: status_number,
  msg: "msg",
  data: [],
});
```

- status:
  - 200: Ok
  - 400: Bad request
  - 401: Unauthorized
- data:
  - [{}]
  - [{}, {}, ...]

### Encriptación

- Utilizar [CryptoJS](https://www.npmjs.com/package/crypto-js)
- contraseña para encriptar: `SiSaleSA_`

### JWT

Headers:

**NOTA:** Es necesario que el token este en el header de la petición y que siempre se envíe.

```json
{
  "authorization": Bearer <Token>
}
```

Token

- Colocar como tiempo de expiración: 1 día [24h]
- Colocar como secret seed: `SiSaleSA_`

```json
{
  "id_user": number,
  "id_rol": number
}
```

- id_rol:
  - Administrador = 1
  - Empleado = 2
  - Cliente = 3

**NOTA:** Verificar la coincidencia y la escritura correcta de cada uno de los campos utilizados como entrada o salida.

## Cliente

### Consideraciones especificas

- Puerto: `5000`
- Ruta específica: `/customer/<<prefijo>>`
- Roles (campo id_rol):
  - Administrador = 1
  - Empleado = 2
  - Cliente = 3
- Estados (campo id_status):
  - Activa = 1
  - Congelada = 2
  - Eliminada = 3
- Genero (campo gender):
  - F
  - M
  - U

**NOTA**: Los campos de estado de cuenta, rol, edad, fecha de creación y membresía se deberán manejar a nivel de código y base de datos. El estado inicial de la cuenta de un cliente es congelada, se cambiará por medio de la verificación del correo.

### Registro

Endpoint para creación de cuentas de usuario cliente, considerar envío de correo de verificación con la ruta definida en el microservicio de autenticación. [verificación de correo](#validar-cuenta)

- Método: `POST`
- Prefijo: `/register`
- Entrada

```json
 {
  "name": "string",
  "lastname": "string",
  "password": "string",
  "email": "string",
  "phone": "string",
  "photo": "string base64",
  "gender": "string",
  "birth_date": "string",
  "address": "string",
  "id_country": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al guardar el usuario.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuario creado con éxito.",
  "data": []
}
```

### Darse de baja

Endpoint para que un usuario elimine su cuenta, la eliminación se simulará por medio de un cambio de estado (Estado 3), `es necesario que se envíe el token`.

- Método: `DELETE`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al eliminar el usuario.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuario eliminado con éxito.",
  "data": []
}
```

### Visualizar Perfil

EndPoint que permite visualizar su perfil, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener el usuario.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuario obtenido con éxito.",
  "data": [{
    "name": "string",
    "lastname": "string",
    "email": "string",
    "phone": "string",
    "photo": "string",
    "gender": "string",
    "birth_date": "string",
    "address": "string",
    "age": number,
    "id_country": number,
    "country": "string"
  }]
}
```

### Actualizar Perfil

EndPoint que permite actualizar su perfil, si no se realizará modificación de foto o contraseña, el contenido del campo (photo/password) deberá ser un string vacío. `es necesario que se envíe el token`.

- Método: `PUT`
- Prefijo: `/`
- Entrada

```json
 {
  "id": number,
  "name": "string",
  "lastname": "string",
  "password": "string",
  "email": "string",
  "phone": "string",
  "photo": "string base64",
  "gender": "string",
  "birth_date": "string",
  "address": "string",
  "id_country": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al actualizar el usuario.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuario actualizado con éxito.",
  "data": []
}
```

### Comprar Membresía

Endpoint para que un cliente compre una membresía, `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/membership`
- Entrada

```json
{
  "id_client": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al comprar membresía.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Membresía comprada con éxito.",
  "data": []
}
```

### Dar de baja Membresía

Endpoint para que un cliente cancele su membresía, `es necesario que se envíe el token`.

- Método: `PUT`
- Prefijo: `/membership`
- Entrada

```json
{
  "id_client": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al cancelar membresía.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Membresía cancelada con éxito.",
  "data": []
}
```

### Agregar a favoritos un equipo

Endpoint para agregar a favoritos un equipo específico, `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/follow`
- Entrada

```json
{
  "id_client": number,
  "id_team": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al agregar equipo a favoritos.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Equipo agregado a favoritos con éxito.",
  "data": []
}
```

### Unirse a quiniela [Considerarse hasta la _FASE 3_]

### Jugadores o Técnico de X equipo

Endpoint para obtener los jugadores o técnico de un equipo específico, es necesario enviar los query params, si el resultado a obtener es un técnico el campo **position** deberá ser un string vacío, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id_team=number&player=boolean`
  - `player` es un booleano representado por los valores **0** y **1**
    - 0: Jugadores
    - 1: Técnico
- Prefijo: `/report/1/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los jugadores o técnico del equipo.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugadores o técnico del equipo obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string",
      "position": "string"
    },
    ...
  ]
}
```

### Jugadores o Técnicos mayores a X años

Endpoint para obtener los jugadores o técnicos mayores a una edad especifica, es necesario enviar los query params, si el resultado a obtener es un técnico el campo **position** deberá ser un string vacío, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?age=number&player=boolean`
  - `age` es el numero de años a buscar sin incluir
  - `player` es un booleano representado por los valores **0** y **1**
    - 0: Jugadores
    - 1: Técnico
- Prefijo: `/report/2/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los jugadores o técnicos mayores a x años.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugadores o técnicos mayores a x años obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string",
      "position": "string",
      "team": "string"
    },
    ...
  ]
}
```

### Jugadores o Técnicos menores a X años

Endpoint para obtener los jugadores o técnicos mayores a una edad especifica, es necesario enviar los query params, si el resultado a obtener es un técnico el campo **position** deberá ser un string vacío, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?age=number&player=boolean`
  - `age` es el numero de años a buscar sin incluir
  - `player` es un booleano representado por los valores **0** y **1**
    - 0: Jugadores
    - 1: Técnico
- Prefijo: `/report/3/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los jugadores o técnicos menores a x años.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugadores o técnicos menores a x años obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string",
      "position": "string",
      "team": "string"
    },
    ...
  ]
}
```

### Equipos que participaron en X competición

Endpoint para obtener los equipos que participaron en una competición especifica, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id_competition=number`
- Prefijo: `/report/4/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los equipos que participaron en una competición.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Equipos que participaron en una competición obtenidos con éxito.",
  "data": [
    {
      "id_team": number,
      "team": "string",
      "photo": "string",
      "id_competition": number
    },
    ...
  ]
}
```

### Equipos de X país

Endpoint para obtener los equipos de un país especifico, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id_country=number`
- Prefijo: `/report/5/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los equipos de un país.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Equipos de un país obtenidos con éxito.",
  "data": [
    {
      "id_team": number,
      "team": "string",
      "photo": "string",
      "id_country": number
    },
    ...
  ]
}
```

### Equipos con X años de antigüedad

Endpoint para obtener los equipos con cierto tiempo de antigüedad, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?age=number`
  - `age` es el numero de años exactos a buscar
- Prefijo: `/report/6/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los equipos con x años de antigüedad.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Equipos con x años de antigüedad obtenidos con éxito.",
  "data": [
    {
      "id_team": number,
      "team": "string",
      "photo": "string",
      "foundation_date": "string",
      "country": "string"
    },
    ...
  ]
}
```

### Estadios en X país

Endpoint para obtener los estadios de un país especifico, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id_country=number`
- Prefijo: `/report/7/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los estadios de un país.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Estadios de un país obtenidos con éxito.",
  "data": [
    {
      "id_stadium": number,
      "stadium": "string",
      "photo": "string",
      "id_country": number
    },
    ...
  ]
}
```

### Estadios con capacidad menor o igual a X

Endpoint para obtener los estadios con una capacidad menor o igual a un cierto numero, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?capacity=number`
  - `capacity` es el numero de la capacidad a buscar (inclusive)
- Prefijo: `/report/8/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los estadios con capacidad menor o igual a x.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Estadios con capacidad menor o igual a x obtenidos con éxito.",
  "data": [
    {
      "id_stadium": number,
      "stadium": "string",
      "photo": "string",
      "country": "string",
      "capacity": number
    },
    ...
  ]
}
```

### Histórico de partidos de X equipo

Endpoint para obtener el histórico de partidos de un equipo especifico, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id_team=number`
- Prefijo: `/report/9/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener el histórico de partidos del equipo x.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Histórico de partidos del equipo x obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "game_date": "string",
      "attendees": number,
      "result_local": number,
      "result_visiting": number,
      "status": number,
      "id_stadium" : number,
      "stadium": "string",
      "id_team_local" : number,
      "team_local": "string",
      "photo_local": "string",
      "id_team_visiting" : number,
      "team_visiting": "string",
      "photo_visiting": "string",
      "id_competition": number,
      "competition": "string"
    },
    ...
  ]
}
```

### Equipos en los que ha estado o dirigido X técnico o jugador.

Endpoint para obtener los jugadores o técnicos que han estado en un equipo especifico, es necesario enviar los query params, si el resultado a obtener es un técnico el campo **position** deberá ser un string vacío, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id_team=number&player=boolean`
  - `player` es un booleano representado por los valores **0** y **1**
    - 0: Jugadores
    - 1: Técnico
- Prefijo: `/report/10/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los jugadores o técnicos que han estado en x equipo.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugadores o técnicos que han estado en x equipo obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string",
      "position": "string",
      "start_date": "string",
      "end_date": "string"
    },
    ...
  ]
}
```

### Partidos donde hubo al menos X goles

Endpoint para los partidos donde la sumatoria de los goles de visita y local es igual a una cantidad especifica, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?goals=number`
  - `goals` es el numero de goles exactos a buscar
- Prefijo: `/report/11/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los partidos donde hubo x cantidad de goles.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Partidos donde hubo x cantidad de goles obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "game_date": "string",
      "attendees": number,
      "result_local": number,
      "result_visiting": number,
      "status": number,
      "id_stadium" : number,
      "stadium": "string",
      "id_team_local" : number,
      "team_local": "string",
      "photo_local": "string",
      "id_team_visiting" : number,
      "team_visiting": "string",
      "photo_visiting": "string",
      "id_competition": number,
      "competition": "string"
    },
    ...
  ]
}
```

### Jugadores con más X incidencias en Y competición

Endpoint para obtener los jugadores con cierta cantidad de incidencias en una competición especifica, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?incidence=number&id_competition=number&quantity=number`
  - `incidence` es el tipo de incidencia a buscar definido en el apartado de [Incidencias](#agregar-incidencia)
  - `quantity` es la cantidad de incidencias exactas a buscar
- Prefijo: `/report/12/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los jugadores con x incidencias en y competición.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugadores con x incidencias en y competición obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string",
      "position": "string",
      "team": "string",
      "count": number
    },
    ...
  ]
}
```

### Jugadores con más X incidencias y Y competiciones de Z año

Endpoint para obtener los jugadores con cierta cantidad de incidencias en varias competiciones en un año exacto, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?incidence=number&year=number&quantity=number`
  - `incidence` es el tipo de incidencia a buscar definido en el apartado de [Incidencias](#agregar-incidencia)
  - `quantity` es la cantidad de incidencias exactas a buscar
- Prefijo: `/report/13/<<Query Params>>`
- Entrada

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los jugadores con x incidencias en y competiciones de z año.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugadores con x incidencias en y competiciones de z año obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string",
      "position": "string",
      "team": "string",
      "id_competition": number,
      "competition": "string",
      "count": number
    },
    ...
  ]
}
```

### Cantidad de X competiciones que ha ganado Y equipo

Endpoint para obtener las competiciones que ha ganado un equipo mediante un arreglo donde se mandan los tipos de competiciones deseadas, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `POST`
- Query Params: `?id_team=number`
- Prefijo: `/report/14/<<Query Params>>`
- Entrada

```json
{
  "competitions": [
    type_competition,
    ...
  ],
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener las competiciones que ha ganado x equipo.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Competencias que ha ganado x equipo obtenidas con éxito.",
  "data": [
    {
      "type": "string",
      "id_team": number,
      "team": "string",
      "photo": "string",
      "count": number
    },
    ...
  ]
}
```

### Listado de partidos en X año

Endpoint para obtener el listado de partidos de un año especifico, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?year=number`
- Prefijo: `/report/15/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener el listado de partidos de un año especifico.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Listado de partidos de un año especifico obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "game_date": "string",
      "attendees": number,
      "result_local": number,
      "result_visiting": number,
      "status": number,
      "id_stadium" : number,
      "stadium": "string",
      "id_team_local" : number,
      "team_local": "string",
      "photo_local": "string",
      "id_team_visiting" : number,
      "team_visiting": "string",
      "photo_visiting": "string",
      "id_competition": number,
      "competition": "string"
    },
    ...
  ]
}
```

### Listado de partidos entre X equipo contra Y equipo

Endpoint para obtener el listado de partidos de dos equipos específicos, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id_team=number&id_opposing_team=number`
- Prefijo: `/report/16/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener el listado de partidos entre x equipo y y equipo.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Listado de partidos entre x equipo y y equipo obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "game_date": "string",
      "attendees": number,
      "result_local": number,
      "result_visiting": number,
      "status": number,
      "id_stadium" : number,
      "stadium": "string",
      "id_team_local" : number,
      "team_local": "string",
      "photo_local": "string",
      "id_team_visiting" : number,
      "team_visiting": "string",
      "photo_visiting": "string",
      "id_competition": number,
      "competition": "string"
    },
    ...
  ]
}
```

### Listado de partidos de X equipo

Endpoint para obtener el listado de partidos de un equipo especifico, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id_team=number`
- Prefijo: `/report/17/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener el listado de partidos del equipo x.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Listado de partidos del equipo x obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "game_date": "string",
      "attendees": number,
      "result_local": number,
      "result_visiting": number,
      "status": number,
      "id_stadium" : number,
      "stadium": "string",
      "id_team_local" : number,
      "team_local": "string",
      "photo_local": "string",
      "id_team_visiting" : number,
      "team_visiting": "string",
      "photo_visiting": "string",
      "id_competition": number,
      "competition": "string"
    },
    ...
  ]
}
```

## Equipo

### Consideraciones especificas

- Puerto: `5001`
- Ruta específica: `/team/<<prefijo>>`

### Crear

EndPoint que permite crear un equipo, `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "name": "string",
  "foundation_date": "string",
  "photo": "string base64",
  "id_country": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al crear equipo.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Equipo creado con éxito.",
  "data": []
}
```

### Ver o listar

Endpoint que permite ver o listar los equipos, es necesario enviar el query params si se desea ver un equipo específico de lo contrario no es necesario.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener equipo(s).",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Equipo(s) obtenido(s) con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "foundation_date": "string",
      "photo": "string",
      "id_country": number,
      "country": number,
    },
    ...
  ]
}
```

### Actualizar

Endpoint que permite actualizar un equipo, el contenido del campo photo debe ser un string vacío si la foto no se va a modificar, `es necesario que se envíe el token`.

- Método: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "name": "string",
  "foundation_date": "string",
  "photo": "string base64",
  "id_country": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al actualizar equipo.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Equipo actualizado con éxito.",
  "data": []
}
```

### Eliminar

Endpoint que permite eliminar un equipo, `es necesario que se envíe el token`.

- Método: `DELETE`
- Prefijo: `/`
- Entrada

```json
{
  "id": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al eliminar equipo.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Equipo eliminado con éxito.",
  "data": []
}
```

## Partido

### Consideraciones especificas

- Puerto: `5002`
- Ruta específica: `/match/<<prefijo>>`
- Estados (campo status):
  - Sin iniciar: 1
  - Iniciado = 2
  - Finalizado = 3
  - Suspendido = 4

### Crear

EndPoint que permite crear un partido, `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "game_date": "string",
  "attendees": number,
  "result_local": number,
  "result_visiting": number,
  "status": number,
  "id_stadium" : number,
  "id_team_local" : number,
  "id_team_visiting" : number,
  "id_competition": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al crear partido.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Partido creado con éxito.",
  "data": []
}
```

### Ver o listar

Endpoint que permite ver o listar los partidos, es necesario enviar el query params si se desea ver un equipo partido de lo contrario no es necesario.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/<<Query Params>>`
- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener partido(s).",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Partido(s) obtenido(s) con éxito.",
  "data": [
    {
      "id": number,
      "game_date": "string",
      "attendees": number,
      "result_local": number,
      "result_visiting": number,
      "status": number,
      "id_stadium" : number,
      "stadium": "string",
      "id_team_local" : number,
      "team_local": "string",
      "id_team_visiting" : number,
      "team_visiting": "string",
      "id_competition": number,
      "competition": "string"
    },
    ...
  ]
}
```

### Actualizar

Endpoint que permite actualizar un partido, `es necesario que se envíe el token`.

- Método: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "game_date": "string",
  "attendees": number,
  "result_local": number,
  "result_visiting": number,
  "status": number,
  "id_stadium" : number,
  "id_team_local" : number,
  "id_team_visiting" : number,
  "id_competition": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al actualizar partido.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Partido actualizado con éxito.",
  "data": []
}
```

### Eliminar

Endpoint que permite eliminar un partido, `es necesario que se envíe el token`.

- Método: `DELETE`
- Prefijo: `/`
- Entrada

```json
{
  "id": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al eliminar partido.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Partido eliminado con éxito.",
  "data": []
}
```

## Competencia

### Consideraciones especificas

- Puerto: `5003`
- Ruta específica: `/competition/<<prefijo>>`
- Tipo (campo type):
  - Liga = 1
  - Eliminatoria = 2
  - Copa = 3
  - Super Copa = 4
  - Cuadrangular = 5
  - Triangular = 6

### Crear

EndPoint que permite crear una competencia, `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "name": "string",
  "type": number,
  "year": number,
  "id_country": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al crear competencia.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Competencia creada con éxito.",
  "data": []
}
```

### Ver o listar

Endpoint que permite ver o listar las competencias, es necesario enviar el query params si se desea ver una competencia de lo contrario no es necesario.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/<<Query Params>>`
- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener competencia(s).",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Competencia(s) obtenida(s) con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "type": number,
      "year": number,
      "id_champion_team": number,
      "champion_team": "string",
      "id_country": number,
      "country": "string"
    },
    ...
  ]
}
```

### Actualizar

Endpoint que permite actualizar una competencia, `es necesario que se envíe el token`.

- Método: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "name": "string",
  "type": number,
  "year": number,
  "id_champion_team": number,
  "id_country": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al actualizar competencia.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Competencia actualizado con éxito.",
  "data": []
}
```

### Eliminar

Endpoint que permite eliminar una competición, `es necesario que se envíe el token`.

- Método: `DELETE`
- Prefijo: `/`
- Entrada

```json
{
  "id": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al eliminar competencia.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Competencia eliminada con éxito.",
  "data": []
}
```

## Estadio

### Consideraciones especificas

- Puerto: `5004`
- Ruta específica: `/stadium/<<prefijo>>`
- Estado (campo status):
  - Disponible = 1
  - Remodelación = 2

### Crear

EndPoint que permite crear un estadio, `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "name": "string",
  "foundation_date": "string",
  "capacity": number,
  "id_country": number,
  "address": "string",
  "status": number,
  "photo": "string base64"
}

```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al crear estadio.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Estadio creado con éxito.",
  "data": []
}
```

### Ver o listar

Endpoint que permite ver o listar los estadios, es necesario enviar el query params si se desea ver un estadio específico de lo contrario no es necesario.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener estadio(s).",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Estadio(s) obtenido(s) con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "foundation_date": "string",
      "capacity": number,
      "id_country": number,
      "country": "string",
      "address": "string",
      "status": number,
      "photo": "string"
    },
    ...
  ]
}
```

### Actualizar

Endpoint que permite actualizar un estadio, el contenido del campo photo debe ser un string vacío si la foto no se va a modificar, `es necesario que se envíe el token`.

- Método: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "name": "string",
  "foundation_date": "string",
  "capacity": number,
  "id_country": number,
  "address": "string",
  "status": number,
  "photo": "string base64"
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al actualizar estadio.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Equipo actualizado con estadio.",
  "data": []
}
```

### Eliminar

Endpoint que permite eliminar un estadio, `es necesario que se envíe el token`.

- Método: `DELETE`
- Prefijo: `/`
- Entrada

```json
{
  "id": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al eliminar estadio.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Estadio eliminado con éxito.",
  "data": []
}
```

## Director Técnico

### Consideraciones especificas

- Puerto: `5005`
- Ruta específica: `/technical-director/<<prefijo>>`
- Estado (campo status):
  - Activo = 1
  - Retirado = 2
  - Lesionado = 3

### Crear

EndPoint que permite crear un directo técnico , `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "name": "string",
  "lastname": "string",
  "birth_date": "string",
  "status": number,
  "photo": "string base64",
  "id_country": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al crear directo técnico.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Directo técnico creado con éxito.",
  "data": []
}
```

### Ver o listar

Endpoint que permite ver o listar los directores técnicos, es necesario enviar el query params si se desea ver un director técnico específico de lo contrario no es necesario.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener director(es) técnico(s).",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Director(es) técnico(s) obtenido(s) con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "birth_date": "string",
      "status": number,
      "photo": "string base64",
      "id_team": number,
      "name_team" : string,
      "id_country": number,
      "country": "string"
    },
    ...
  ]
}
```

### Actualizar

Endpoint que permite actualizar un director técnico, el contenido del campo photo debe ser un string vacío si la foto no se va a modificar, `es necesario que se envíe el token`.

- Método: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "name": "string",
  "lastname": "string",
  "birth_date": "string",
  "status": number,
  "photo": "string base64",
  "id_country": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al actualizar director técnico.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Director técnico actualizado con éxito.",
  "data": []
}
```

### Eliminar

Endpoint que permite eliminar un director técnico, `es necesario que se envíe el token`.

- Método: `DELETE`
- Prefijo: `/`
- Entrada

```json
{
  "id": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al eliminar director técnico.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Director técnico eliminado con éxito.",
  "data": []
}
```

## Jugador

### Consideraciones especificas

- Puerto: `5006`
- Ruta específica: `/player/<<prefijo>>`
- Posición (campo position):
  - Portero = 1
  - Defensa = 2
  - Medio = 3
  - Delantero = 4
- Estado (campo status):
  - Activo = 1
  - Retirado = 2
  - Lesionado = 3

### Crear

EndPoint que permite crear un jugador, `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "name": "string",
  "lastname": "string",
  "birth_date": "string",
  "id_nationality": number,
  "position": number,
  "status": number,
  "photo": "string base64",
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al crear jugador.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugador creado con éxito.",
  "data": []
}
```

### Ver o listar

Endpoint que permite ver o listar los jugadores, es necesario enviar el query params si se desea ver un jugador específico de lo contrario no es necesario.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener jugador(s).",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugador(es) obtenido(s) con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "birth_date": "string",
      "id_nationality": number,
      "nationality": "string",
      "position": number,
      "status": number,
      "id_team": number,
      "name_team" : string,
      "photo": "string",
    },
    ...
  ]
}
```

### Actualizar

Endpoint que permite actualizar un jugador, el contenido del campo photo debe ser un string vacío si la foto no se va a modificar, `es necesario que se envíe el token`.

- Método: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "name": "string",
  "lastname": "string",
  "birth_date": "string",
  "id_nationality": number,
  "position": number,
  "status": number,
  "photo": "string base64",
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al actualizar jugador.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugador actualizado con éxito.",
  "data": []
}
```

### Eliminar

Endpoint que permite eliminar un jugador, `es necesario que se envíe el token`.

- Método: `DELETE`
- Prefijo: `/`
- Entrada

```json
{
  "id": number
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al eliminar jugador.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugador eliminado con éxito.",
  "data": []
}
```

## Noticia

### Consideraciones especificas

- Puerto: `5007`
- Ruta específica: `/post/<<prefijo>>`

### Crear

EndPoint que permite crear un post/noticia, `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "id_team": number,
  "id_user": number,
  "title": "string",
  "description": "string",
  "date": "string"
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al crear noticia.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Noticia creada con éxito.",
  "data": []
}
```

### Ver o listar

Endpoint que permite ver o listar los post/noticias, es necesario enviar el query params `id` si se desea ver una noticia específica de lo contrario no es necesario, es necesario enviar el query params `team` si se desea ver las noticias relacionadas a un equipo de lo contrario no es necesario.

- Método: `GET`
- Query Params: `?id=number`, `?team=number`
- Prefijo: `/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener noticia(s).",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Noticia(s) obtenida(s) con éxito.",
  "data": [
    {
      "id": number,
      "id_team": number,
      "team": "string",
      "id_user": number,
      "title": "string",
      "description": "string",
      "date": "string"
    },
    ...
  ]
}
```

## Arbitro

### Consideraciones especificas

- Puerto: `5008`
- Ruta específica: `/referee/<<prefijo>>`

## Predicción [Considerarse hasta la _FASE 3_]

### Consideraciones especificas

- Puerto: `5009`
- Ruta específica: `/prediction/<<prefijo>>`

## Autenticación

### Consideraciones especificas

- Puerto: `5010`
- Ruta específica: `/auth/<<prefijo>>`

### Login

Endpoint para el inicio de sesión de todos los usuarios, generar token según especificaciones descritas.

- Método: `POST`
- Prefijo: `/`
- Roles (campo id_rol):
  - Administrador = 1
  - Empleado = 2
  - Cliente = 3
- Estados (campo id_status):
  - Activa = 1
  - Congelada = 2
  - Eliminada = 3
- Entrada

```json
{
  "email": string,
  "password": string
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error de autenticación.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "",
  "data": {
    "token": "string",
    "id_status": number,
    "id_rol": number,
  }
}
```

### Validar Cuenta

Endpoint para verificación de correo al crear la cuenta de cliente. Cambiar el estado de la cuenta de congelada a activa para poder iniciar sesión.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/<<Query Params>>`
- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al verificar correo.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Correo verificado con éxito.",
  "data": []
}
```

### Envío de contraseña temporal

Endpoint para enviar el correo electrónico con la contraseña que debemos generar en el sistema y el link que redirige al login, por la solicitud de un cliente que no puede acceder al sistema.

- Método: `POST`
- Prefijo: `/temporal-password`
- Entrada

```json
{
  "email": "string"
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al enviar la contraseña temporal.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Se ha enviado un correo para restablecer la contraseña.",
  "data": []
}
```

### Restablecer contraseña

Endpoint para que después de haber ingresado en el link que se envió en el correo. Al momento que el usuario cambia su contraseña. El cambio de contraseña es obligatorio.

- Método: `POST`
- Prefijo: `/reset-password`
- Entrada

```json
{
  "email": "string",
  "temporal_password": "string",
  "new_password": "string"
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al restablecer la contraseña.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Se ha restablecido la contraseña",
  "data": []
}
```

## Administrador

### Consideraciones especificas

- Puerto: `5011`
- Ruta específica: `/administrator/<<prefijo>>`
- Roles (campo id_rol):
  - Administrador = 1
  - Empleado = 2
  - Cliente = 3
- Estados (campo id_status):
  - Activa = 1
  - Congelada = 2
  - Eliminada = 3
- Genero (campo gender):
  - F
  - M
  - U

**NOTA**: Los campos de estado de cuenta, edad, fecha de creación se deberán manejar a nivel de código y base de datos. El estado inicial de la cuenta para los usuarios empleado y administrador debe ser activa.

### Usuarios Suscritos a X equipo

Endpoint para obtener los usuarios que contengan en sus favoritos a un equipo especifico, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id_team=number`
- Prefijo: `/report/1/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener usuarios suscritos al equipo x.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuarios suscritos al equipo x obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string"
    },
    ...
  ]
}
```

### Usuario Con o Sin Membresía

Endpoint para obtener los usuarios que poseen o no una membresía, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?membership=boolean`
  - `membership` es un booleano representado por los valores **0** y **1**
    - 0: No posee membresía
    - 1: Posee membresía
- Prefijo: `/report/2/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener usuarios con o sin membresía.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuarios con o sin membresía obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string"
    },
    ...
  ]
}
```

### Usuarios que Mas membresías han adquirido

Endpoint para obtener los 10 usuarios que mas membresías han obtenido, `es necesario que se envíe el token`.

- Método: `GET`
- Prefijo: `/report/3`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener usuarios con mas membresías.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuarios con mas membresías obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string",
      "count": number
    },
    ...
  ]
}
```

### Usuarios que más dinero han gastado

Endpoint para obtener los 10 usuarios que mas dinero han gastado, para obtener esta cantidad el calculo se debe realizar mediante la cantidad de membresías compradas por el precio de las mismas, `es necesario que se envíe el token`.

- Método: `GET`
- Prefijo: `/report/4`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener usuarios con mas dinero gastado.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuarios con mas dinero gastado obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string",
      "amount": number
    },
    ...
  ]
}
```

### Usuarios de X País

Endpoint para obtener los usuarios de un país especifico, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id_country=number`
- Prefijo: `/report/5/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener usuarios de x país.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuarios de x país obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "photo": "string",
    },
    ...
  ]
}
```

### Usuarios de X genero

Endpoint para obtener los usuarios de un genero especifico, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?gender=char`
  - `gender` es el carácter del genero a buscar, este carácter fue definido en las consideraciones especificas del [administrador](#administrador)
- Prefijo: `/report/6/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener usuarios de x genero.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuarios de x genero obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "photo": "string",
      "nationality": "string"
    },
    ...
  ]
}
```

### Usuarios con al menos X años de edad

Endpoint para obtener los usuarios con una edad minima, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?age=number`
  - `age` es el número de años mínimos que debe tener el usuario
- Prefijo: `/report/7/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener usuarios con al menos x años de edad.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuarios con al menos x años de edad, obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "photo": "string",
      "nationality": "string",
      "birth_date": "string",
      "age": "string"
    },
    ...
  ]
}
```

### Empleados que MAS/MENOS noticias han publicado

Endpoint para obtener los empleados con mas o menos noticias publicadas, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?order=boolean`
  - `order` es un booleano representado por los valores **0** y **1**
    - 0: Mas noticias publicadas
    - 1: Menos noticias publicadas
- Prefijo: `/report/8/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener empleados con mas o menos noticias publicadas.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Empleados con mas o menos noticias publicadas, obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string",
      "count": number
    },
    ...
  ]
}
```

### Empleados que MAS/MENOS noticias han publicado de X Equipo

Endpoint para obtener los empleados con mas o menos noticias publicadas de un equipo especifico, es necesario enviar los query params, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?order=boolean&id_team=number`
  - `order` es un booleano representado por los valores **0** y **1**
    - 0: Mas noticias publicadas
    - 1: Menos noticias publicadas
- Prefijo: `/report/9/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener empleados con mas o menos noticias publicadas de x equipo.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Empleados con mas o menos noticias publicadas de x equipo, obtenidos con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "nationality": "string",
      "photo": "string",
      "count": number
    },
    ...
  ]
}
```

### Bitácoras de los administradores

Endpoint para obtener los registros contenidos en la bitácora con el detalle de las acciones realizadas, `es necesario que se envíe el token`.

- Método: `GET`
- Prefijo: `/report/10`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener bitácora de los administradores.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Bitácora de los administradores obtenida con éxito.",
  "data": [
    {
      "id": number,
      "user_name": "string",
      "user_lastname": "string",
      "user_photo": "string",
      "user_role": number,
      "action": "string",
      "date": "string",
      "description": "string",
      "database_table": "string"
    },
    ...
  ]
}
```

### Crear usuario empleado/administrador

Endpoint para creación de cuentas de usuario administrador y empleado, se deberá enviar un correo electrónico con las credenciales de autenticación para el nuevo usuario creado.

- Método: `POST`
- Prefijo: `/user`
- Entrada

```json
 {
  "name": "string",
  "lastname": "string",
  "password": "string",
  "email": "string",
  "phone": "string",
  "photo": "string base64",
  "gender": "string",
  "birth_date": "string",
  "address": "string",
  "id_country": number,
  "id_rol": number,
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al guardar el usuario.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuario creado con éxito.",
  "data": []
}
```

### Dar de alta/baja o activar/congelar un usuario

Endpoint para que un administrador actualice el estado de la cuenta de un usuario, la eliminación se simulará por medio de un cambio de estado (Estado 3), `es necesario que se envíe el token`.

**NOTA**: El campo descripción se utilizará para enviar la razón por la cual el administrador decide realizar cierta acción sobre una cuenta específica.

- Método: `PUT`
- Prefijo: `/user/status`
- Entrada

```json
{
  "id": number,
  "id_status": number,
  "description": "string"
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al actualizar el estado del usuario.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Estado del usuario actualizado con éxito.",
  "data": []
}
```

### Ver o listar Usuarios

EndPoint que permite ver o listar usuarios, es necesario enviar el query params si se desea ver un usuario específico de lo contrario no es necesario, `es necesario que se envíe el token`.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/user/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener usuario(s).",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuario(s) obtenido(s) con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "email": "string",
      "phone": "string",
      "photo": "string",
      "gender": "string",
      "birth_date": "string",
      "address": "string",
      "age": number,
      "id_rol": number,
      "id_status": number,
      "id_country": number,
      "country": "string"
    },
    ...
  ]
}
```

### Actualizar usuario

EndPoint que permite actualizar un usuario, si no se realizará modificación de foto o contraseña, el contenido del campo (photo/password) deberá ser un string vacío. `es necesario que se envíe el token`.

**NOTA**: El campo descripción se utilizará para enviar la razón por la cual el administrador decide realizar cierta acción sobre una cuenta específica.

- Método: `PUT`
- Prefijo: `/user`
- Entrada

```json
{
  "id": number,
  "name": "string",
  "lastname": "string",
  "password": "string",
  "email": "string",
  "phone": "string",
  "photo": "string base64",
  "gender": "string",
  "birth_date": "string",
  "address": "string",
  "id_country": number,
  "description": "string"
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al actualizar el usuario.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Usuario actualizado con éxito.",
  "data": []
}
```

## Empleado

### Consideraciones especificas

- Puerto: `5012`
- Ruta específica: `/employee/<<prefijo>>`

### Transferir Jugador

Endpoint para que un empleado transfiera un jugador a otro equipo, `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/player-transfer`
- Entrada

```json
{
  "id_player": number,
  "id_team_origin" : number,
  "id_team_destination" : number,
  "start_date": "string",
  "end_date": "string"
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al transferir jugador.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Jugador transferido con éxito.",
  "data": []
}
```

### Log de Transferencia Jugador

Endpoint que permite ver o listar el registro de transferencias de los jugadores, es necesario enviar el query params si se desea ver el log de transferencias de un jugador específico de lo contrario no es necesario.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/player-transfer/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener el log de transferencias.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Log de transferencias obtenido con éxito.",
  "data": [
    {
      "id": number,
      "id_player": number,
      "player_name": "string",
      "player_lastname": "string",
      "id_team_origin" : number,
      "team_origin": "string",
      "id_team_destination" : number,
      "team_destination": "string",
      "start_date": "string",
      "end_date": "string"
    },
    ...
  ]
}
```

### Transferir Director Técnico

Endpoint para que un empleado transfiera un director técnico a otro equipo, `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/technical-director-transfer`
- Entrada

```json
{
  "id_coach": number,
  "id_team_origin" : number,
  "id_team_destination" : number,
  "start_date": "string",
  "end_date": "string"
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al transferir director técnico.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Director técnico transferido con éxito.",
  "data": []
}
```

### Log de Transferencia Director Técnico

Endpoint que permite ver o listar el registro de transferencias de los directores técnicos, es necesario enviar el query params si se desea ver el log de transferencias de un director técnico específico de lo contrario no es necesario.

- Método: `GET`
- Query Params: `?id=number`
- Prefijo: `/technical-director-transfer/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener el log de transferencias.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Log de transferencias obtenido con éxito.",
  "data": [
    {
      "id": number,
      "id_coach": number,
      "coach_name": "string",
      "coach_lastname": "string",
      "id_team_origin" : number,
      "team_origin": "string",
      "id_team_destination" : number,
      "team_destination": "string",
      "start_date": "string",
      "end_date": "string"
    },
    ...
  ]
}
```

### Agregar Incidencia

Endpoint para la creación de incidencias. El campo descripción servirá para manejar los detalles que se muestran en el diagrama del enunciado. `es necesario que se envíe el token`.

- Método: `POST`
- Prefijo: `/incidence`
- Tipo (campo id_type):
  - Gol = 1
  - Autogol = 2
  - Tarjeta amarilla = 3
  - Tarjeta roja = 4
- Entrada

```json
{
  "id_player": number,
  "id_game" : number,
  "id_type" : number,
  "description": "string",
  "minute": "string"
}
```

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al agregar incidencia.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Incidencia agregada con éxito.",
  "data": []
}
```

## País

### Consideraciones especificas

- Puerto: `5013`
- Ruta específica: `/country/<<prefijo>>`

### Listar países

Endpoint para listar todos los países, es necesario enviar el query params si se desea ver un pais específico de lo contrario no es necesario.

- Método: `Get`
- Query Params: `?id=number`
- Prefijo: `/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener (los) país(es).",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Se han obtenido (los) país(es).",
  "data": [
    {
      "id": number,
      "name": "string"
    },
    ...
  ]
}
```
