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

`http://ip:puerto/esb/-ruta específica-`

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

### Registro

### Darse de baja

### Visualizar Perfil

### Actualizar Perfil

### Comprar Membresia

### Agregar a favoritos un equipo

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

### Validar Cuenta

### Envío de contraseña temporal

### Restablecer contraseña

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

### Cambiar Estado Jugador/Director Técnico [¿Objetivo Principa?]

## País

### Consideraciones especificas

- Puerto: `5013`
- Ruta específica: `/country/<<prefijo>>`

### Listar paises
