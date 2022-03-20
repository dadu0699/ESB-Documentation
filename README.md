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

- Utilizar la estructura y contenido siguiente para la tabla [Paises](https://gist.github.com/adhipg/1600028)
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
  1. Administrador.
  2. Empleado.
  3. Cliente.

**NOTA:** Verificar la coincidencia y la escritura correcta de cada uno de los campos utilizados como entrada o salida.

## Cliente

### Consideraciones especificas

- Puerto: `5000`
- Ruta específica: `/customer/<<prefijo>>`
- Roles (campo id_rol):
  1. Administrador.
  2. Empleado.
  3. Cliente.
- Estados (campo id_status):
  1. Activa.
  2. Congelada.
  3. Eliminada.
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
- Prefijo: `/?id`

- Salida

ERROR:

```json
{
  "status": 400,
  "msg": "Error al ob el usuario.",
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
    "password": "string",
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

### Ver o listar

### Actualizar

### Eliminar

## Partido

### Consideraciones especificas

- Puerto: `5002`
- Ruta específica: `/match/<<prefijo>>`

### Crear

### Ver o listar

### Actualizar

### Eliminar

## Competencia

### Consideraciones especificas

- Puerto: `5003`
- Ruta específica: `/competition/<<prefijo>>`

### Crear

### Ver o listar

### Actualizar

### Eliminar

## Estadio

### Consideraciones especificas

- Puerto: `5004`
- Ruta específica: `/stadium/<<prefijo>>`

### Crear

### Ver o listar

### Actualizar

### Eliminar

## Director Técnico

### Consideraciones especificas

- Puerto: `5005`
- Ruta específica: `/technical-director/<<prefijo>>`

### Crear

### Ver o listar

### Actualizar

### Eliminar

## Jugador

### Consideraciones especificas

- Puerto: `5006`
- Ruta específica: `/player/<<prefijo>>`

### Crear

### Ver o listar

### Actualizar

### Eliminar

## Noticia

### Consideraciones especificas

- Puerto: `5007`
- Ruta específica: `/post/<<prefijo>>`

### Crear

### Ver o listar

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
  1. Administrador.
  2. Empleado.
  3. Cliente.
- Estados (campo id_status):
  1. Activa.
  2. Congelada.
  3. Eliminada.
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
- Prefijo: `/?id`
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

### Ver o listar Usuarios

## Actualizar usuario

## Empleado

### Consideraciones especificas

- Puerto: `5012`
- Ruta específica: `/employee/<<prefijo>>`

### Transferir Jugador

### Log de Transferencia Jugador

### Transferir Director Técnico

### Log de Transferencia Director Técnico

### Agregar Incidencia

### Cambiar Estado Jugador/Director Técnico [¿Objetivo Principal?]

## País

### Consideraciones especificas

- Puerto: `5013`
- Ruta específica: `/country/<<prefijo>>`

### Listar paises
