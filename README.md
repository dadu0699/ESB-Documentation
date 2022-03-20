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

### Comprar Membresia

## Equipo

### Consideraciones especificas

- Puerto: `5001`
- Ruta específica: `/team/<<prefijo>>`

## Partido

### Consideraciones especificas

- Puerto: `5002`
- Ruta específica: `/match/<<prefijo>>`

## Competencia

### Consideraciones especificas

- Puerto: `5003`
- Ruta específica: `/competition/<<prefijo>>`

## Estadio

### Consideraciones especificas

- Puerto: `5004`
- Ruta específica: `/stadium/<<prefijo>>`

## Director Técnico

### Consideraciones especificas

- Puerto: `5005`
- Ruta específica: `/technical-director/<<prefijo>>`

## Jugador

### Consideraciones especificas

- Puerto: `5006`
- Ruta específica: `/player/<<prefijo>>`

## Noticia

### Consideraciones especificas

- Puerto: `5007`
- Ruta específica: `/post/<<prefijo>>`

## Arbitro

### Consideraciones especificas

- Puerto: `5008`
- Ruta específica: `/referee/<<prefijo>>`

## Predicción

### Consideraciones especificas

- Puerto: `5009`
- Ruta específica: `/prediction/<<prefijo>>`

## Autenticación

### Consideraciones especificas

- Puerto: `5010`
- Ruta específica: `/auth/<<prefijo>>`

### Envío de contraseña temporal

## Administrador

### Consideraciones especificas

- Puerto: `5011`
- Ruta específica: `/administrator/<<prefijo>>`

## Empleado

### Consideraciones especificas

- Puerto: `5012`
- Ruta específica: `/employee/<<prefijo>>`
