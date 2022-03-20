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
- Todas las imagenes se envían en base64 _Cadena completa_ `data:image/png;base64,iVBORw0KGgo...`

### Estructura de Endpoint

`http://ip:puerto/esb/<<ruta específica>>`

```json
{
  "status": number,
  "msg": "string",
  "data": [array]
}
```

- status:
  - 200: ok
  - 400: bad request
  - 401: unauthorization
  - 500: Server error or 'Error de llave foránea'
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
- Colocar como salt rounds: 10
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

**NOTA**: Los campos de estado de cuenta, rol, edad, fecha de creación y membresia se deberán manejar a nivel de código y base de datos. El estado inicial de la cuenta es congelada, se cambiará por medio de la verificación del correo.

### Registro

Endpoint para creación de cuentas de usuario cliente, considerar envío de correo de verificación con la ruta definida en el microservicio de autenticación. [verificación de correo](#validar-cuenta)

- Metodo: `POST`
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

- Metodo: `DELETE`
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

- Metodo: `GET`
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

EndPoint que permite actualizar su perfil, el contenido del campo photo debe ser un string vacio si la foto no se va a modificar, `es necesario que se envíe el token`.

- Metodo: `PUT`
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

### Comprar Membresia

Endpoint para que un cliente compre una membresía, `es necesario que se envíe el token`.

- Metodo: `POST`
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
  "msg": "Membresia comprada con éxito.",
  "data": []
}
```

### Dar de baja Membresia

Endpoint para que un cliente cancele su membresía, `es necesario que se envíe el token`.

- Metodo: `PUT`
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
  "msg": "Membresia cancelada con éxito.",
  "data": []
}
```

### Agregar a favoritos un equipo

Endpoint para agregar a favoritos un equipo específico, `es necesario que se envíe el token`.

- Metodo: `POST`
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

### Jugadores o Técnico mayores a X años

### Jugadores o Técnico menores a X años

### Equipos que participaron en X competición

### Equipos de X país

### Equipos con X años de antigüedad

### Estadios en X país

### Estadios con capacidad menor o igual a X

### Histórico de partidos de X equipo

### Equipos en los que ha estado o dirigido X técnico o jugador.

### Partidos donde hubo al menos X goles

### Jugadores con más X incidencias en Y competición

### Jugadores con más X incidencias y Y competiciones de Z año

### Cantidad de X competiciones que ha ganado Y equipo

### Listado de partidos en X año

### Listado de partidos entre X equipo contra Y equipo

### Listado de partidos de X equipo

## Equipo

### Consideraciones especificas

- Puerto: `5001`
- Ruta específica: `/team/<<prefijo>>`

### Crear

EndPoint que permite crear un equipo, `es necesario que se envíe el token`.

- Metodo: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "name": "string",
  "fundation_date": "string",
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

- Metodo: `GET`
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
      "fundation_date": "string",
      "photo": "string",
      "id_country": number
    },
    ...
  ]
}
```

### Actualizar

Endpoint que permite actualizar un equipo, el contenido del campo photo debe ser un string vacio si la foto no se va a modificar, `es necesario que se envíe el token`.

- Metodo: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "name": "string",
  "fundation_date": "string",
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

- Metodo: `DELETE`
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
- Estados (campo state):
  - Sin iniciar: 1
  - Iniciado = 2
  - Finalizado = 3
  - Suspendido = 4

### Crear

EndPoint que permite crear un partido, `es necesario que se envíe el token`.

- Metodo: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "game_date": "string",
  "attendees": number,
  "result_local": number,
  "result_visiting": number,
  "state": number,
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

- Metodo: `GET`
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
      "state": number,
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

Endpoint que permite actualizar un partido, el contenido del campo photo debe ser un string vacio si la foto no se va a modificar, `es necesario que se envíe el token`.

- Metodo: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "game_date": "string",
  "attendees": number,
  "result_local": number,
  "result_visiting": number,
  "state": number,
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

- Metodo: `DELETE`
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

- Metodo: `POST`
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

- Metodo: `GET`
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

Endpoint que permite actualizar un partido, el contenido del campo photo debe ser un string vacio si la foto no se va a modificar, `es necesario que se envíe el token`.

- Metodo: `PUT`
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

Endpoint que permite eliminar una competicion, `es necesario que se envíe el token`.

- Metodo: `DELETE`
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

### Crear

EndPoint que permite crear un estadio, `es necesario que se envíe el token`.

- Metodo: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "name": "string",
  "fundation_date": "string",
  "capacity": number,
  "id_country": number,
  "address": "string",
  "state": "string", // Pendiente de definición
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

- Metodo: `GET`
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
      "fundation_date": "string",
      "capacity": number,
      "id_country": number,
      "country": "string",
      "address": "string",
      "state": "string", // Pendiente de definición
      "photo": "string"
    },
    ...
  ]
}
```

### Actualizar

Endpoint que permite actualizar un estadio, el contenido del campo photo debe ser un string vacio si la foto no se va a modificar, `es necesario que se envíe el token`.

- Metodo: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "name": "string",
  "fundation_date": "string",
  "capacity": number,
  "id_country": number,
  "address": "string",
  "state": "string", // Pendiente de definición
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

- Metodo: `DELETE`
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

### Crear

EndPoint que permite crear un directo técnico , `es necesario que se envíe el token`.

- Metodo: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "name": "string",
  "lastname": "string",
  "birth_date": "string",
  "status": "string", // Pendiente de definición
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

- Metodo: `GET`
- Query Params: `?id=number`
- Prefijo: `/<<Query Params>>`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener director(es) tecnico(s).",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Director(es) tecnico(s) obtenido(s) con éxito.",
  "data": [
    {
      "id": number,
      "name": "string",
      "lastname": "string",
      "birth_date": "string",
      "status": "string", // Pendiente de definición
      "photo": "string base64",
      "id_country": number,
      "country": "string"
    },
    ...
  ]
}
```

### Actualizar

Endpoint que permite actualizar un director técnico, el contenido del campo photo debe ser un string vacio si la foto no se va a modificar, `es necesario que se envíe el token`.

- Metodo: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "name": "string",
  "lastname": "string",
  "birth_date": "string",
  "status": "string", // Pendiente de definición
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

- Metodo: `DELETE`
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

### Crear

EndPoint que permite crear un jugador, `es necesario que se envíe el token`.

- Metodo: `POST`
- Prefijo: `/`
- Entrada

```json
{
  "name": "string",
  "lastname": "string",
  "birth_date": "string",
  "nationality": number,
  "position": number,
  "status": "string",  // Pendiente de definición
  "id_team": number,
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

- Metodo: `GET`
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
      "nationality": number,
      "position": number,
      "status": "string",  // Pendiente de definición
      "id_team": number,
      "name_team" : string,
      "photo": "string",
    },
    ...
  ]
}
```

### Actualizar

Endpoint que permite actualizar un jugador, el contenido del campo photo debe ser un string vacio si la foto no se va a modificar, `es necesario que se envíe el token`.

- Metodo: `PUT`
- Prefijo: `/`
- Entrada

```json
{
  "id": number,
  "name": "string",
  "lastname": "string",
  "birth_date": "string",
  "nationality": number,
  "position": number,
  "status": "string",  // Pendiente de definición
  "id_team": number,
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

- Metodo: `DELETE`
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

- Metodo: `POST`
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

- Metodo: `GET`
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

Endopoint para el inicio de sesion de todos los usuarios, generar token segun especificaciones descritas.

- Metodo: `POST`
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
    "id_status": number
  }
}
```

### Validar Cuenta

Endpoint para verificación de correo al crear la cuenta de cliente. Cambiar el estado de la cuenta de congelada a activa para poder iniciar sesión.

- Metodo: `GET`
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

- Metodo: `POST`
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

- Metodo: `POST`
- Prefijo: `/reset-password`
- Entrada

```json
{
  "id_client": number,
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

### Usuarios Suscritos a X equipo

### Usuario Con o Sin Membresía

### Usuarios que Mas membresías han adquirido

### Usuarios que más dinero han gastado

### Usuarios de X País

### Usuarios de X genero

### Usuarios con al menos X años de edad

### Empleados que MAS/MENOS noticias han publicado

### Empleados que MAS/MENOS noticias han publicado de X Equipo

### Bitácoras de los administradores

### Crear usuario empleado/administrador

### Dar de alta/baja o activar/congelar un usuario

Endpoint para que un administrador actualice el estado de un usuario, la eliminación se simulará por medio de un cambio de estado (Estado 3), `es necesario que se envíe el token`.

- Metodo: `PUT`
- Prefijo: `/user/status`
- Entrada

```json
{
  "id": number,
  "id_status": number
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

EndPoint que permite ver o listar usuarios , `es necesario que se envíe el token`.

- Metodo: `GET`
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
    },
    ...
  ]
}
```

## Actualizar usuario

EndPoint que permite actualizar un usuario, el contenido del campo photo debe ser un string vacio si la foto no se va a modificar, `es necesario que se envíe el token`.

- Metodo: `PUT`
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

## Empleado

### Consideraciones especificas

- Puerto: `5012`
- Ruta específica: `/employee/<<prefijo>>`

### Transferir Jugador

Endpoint para que un empleado transfiera un jugador a otro equipo, `es necesario que se envíe el token`.

- Metodo: `POST`
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

Endpoint que permite ver o listar el registro de tranferencias de los jugadores, es necesario enviar el query params si se desea ver el log de transferencias de un jugador específico de lo contrario no es necesario.

- Metodo: `GET`
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

- Metodo: `POST`
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

Endpoint que permite ver o listar el registro de tranferencias de los directores técnicos, es necesario enviar el query params si se desea ver el log de transferencias de un director técnico específico de lo contrario no es necesario.

- Metodo: `GET`
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

### Cambiar Estado Jugador/Director Técnico [¿Objetivo Principal?]

## País

### Consideraciones especificas

- Puerto: `5013`
- Ruta específica: `/country/<<prefijo>>`

### Listar paises

Endpoint para listar todos los paises.

- Metodo: `Get`
- Prefijo: `/`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al obtener los paises.",
  "data": []
}
```

OK:

```json
{
  "status": 200,
  "msg": "Se han obtenido los paises.",
  "data": [
    {
      "id": number,
      "name": "string"
    },
    ...
  ]
}
```
