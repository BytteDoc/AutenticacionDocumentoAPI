# Autenticacion Documento API - Documento Referencia

![N|Solid](https://www.bytte.com.co/pagweb/wp-content/uploads/2019/05/by-casb-logo.png)

**CASB API** es un API que provee la funcionalidad para realizar diferentes procesos de autenticacion sincronicos y asincronicos dependiendo del caso de uso a evaluar

### Operaciones

 - [Authorization - Login](#ApiGarewayAutorization).
 - [ValidacionDocumento](#ValidacionDocumento).
 - [ConsultaProcesoValidacion](#ConsultaProcesoValidacion).
 - [ConsultaUsuarioDocumento](#ConsultaUsuarioDocumento).
 
### Tablas de Equivalencia
 - [Tipos de Documento](#TiposDocumento).
 - [Codigos ByKeeper (Validacion Manual)](#CodigosByKeeper). 

**CASB API**  Es un servicio comercial, para poder hacer uso de este, contactese con info@bytte.com.co para recibir instrucciones de como poder realizar uso de estos servicios

**CASB** is a trademark registered by Bytte S.A.S 
 
## Operaciones:
## <a name="ApiGarewayAutorization"></a>Api Gateway Autorization
Para hacer uso de esta API, debe realizarlo con un usuario/clave provisto por Bytte para este fin

Url - Login User - POST
{{baseUrl}}/login/authenticate/

```json
{
  "Username": "ClientUserName",
  "Password": "**SecurePassword*"
}
```
Si la combinacion usuario/clave es valido, el API retornara un Token de la siguiente forma

```json
"eyJhbGciOiJIUzI1NiIsInR5cC6Ip..."
```

Este token sera requerido para hacer uso de las demas operaciones expuestas en la API

## <a name="ValidacionDocumento"></a> Validacion Documento

Esta Operacion realiza el proceso de autenticacion del usuario presentado contra el documento capturado
las capturas deben realizarse con el SDK provisto por Bytte para este fin

* Algunos valores son obtenidos de la operacion **ApiGarewayAutorization** 

Url - ValidacionDocumento - POST
> {{baseUrl}}/CASB/ValidacionDocumento

Body Type
* **Form/Data**

Form Variables

* **face** : Archivo Binario - Imagen Selfie capturada
* **fingerX** : Archivo Binario - con la captura de la huella 
  X - Este valor puede ser desde 1 a 10 o 20-21 dependiendo de la mano a procesar  
  Se debe enviar un registro del Form/Data por cada huella capturada 

* **documentoreverso** :  Binary file with a back document capture
* **documentofrente** :  Binary file with a front document capture
* **identificadorproceso** : Identificador de la Transaccion en el Sistema Cliente, es la llave que correlacionara y con la cual se podran realizar busquedas
* **identificadorconsumidor** : Identificador del sistema/aplicacion cliente que hace uso del API
* **identificadororigen** : Identificador del origen de las capturas IOS/ANDROID
* **barcodebase64mrz**: String - Codigo de barras PDF417 o MRZ segun el tipo de documento capturado
* **tipodocumento** : String - Tipo de Documento a procesar 
* **numerodocumento** : String - Numero de Documento a Procesar
* **enviobykeeper** : String - booleano "true"/"false", si el proceso debe ser enviado a validacion manual


In your project or system, the first step is generate a new Token In this token, We correlationate your transaction Id with MiiD system

Variables to Send:

* **enterpriseClientId** = Is a Identifier provided by MiiD Team. This Identifier is unique by client 
* **externalId** = Is a current client Identifier (in the client information system)
* **documentType** = Is a document type from user to authenticate 
* **documentNumber** = Is a document number from user to authenticate
* **returnUrl** = Is a URL/URI. When MiiD Process Ends, MiiD system redirect to this Url to continue the client process

Url - GenetateProcessToken - POST
> https://services.miid.bio/Gateway/GenetateProcessToken/

Example CURL - Form/Data - Token Bearer
```sh
curl --location --request POST 'https://CASB/ValidacionDocumento' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiI...' \
--form 'documentoreverso=@"/insImDocReverso.jpg"' \
--form 'documentofrente=@"/frenteCO.png"' \
--form 'face=@"/ImageSelfie.jpg"' \
--form 'finger2=@"/insHuella2.jpg"' \
--form 'finger3=@"/insHuella3.jpg"' \
--form 'finger4=@"/insHuella4.jpg"' \
--form 'finger5=@"/insHuella5.jpg"' \
--form 'tipodocumento="1"' \
--form 'numerodocumento="80208223"' \
--form 'identificadorproceso="PApertura120"' \
--form 'identificadorconsumidor="APPPersonas"' \
--form 'identificadororigen="IOS"' \
--form 'enviobykeeper="true"' \
--form 'barcodebase64mrz="MDIwMTYyOTAxMTcxQS..."'
```

<a name="ProcesoValidacionDocumentoResponse"></a>
```json
Response: JSON
{
    "Id": "9c4c623e-9147-44b3-801c-355453f70946",
    "IdProceso": "PApertura120",
    "NumeroDocumento": "80208223",
    "TipoDocumento": 1,
    "FechaProceso": "2021-12-01T00:00:00",
    "ScoreDactilar": 60,
    "ScoreDactilarValor": 3500,
    "ScoreANIOCR": 10,
    "ScoreANIBarCode": 7,
    "ScoreOCRBarCode": 20,
    "ScoreFace": 60,
    "ScoreFaceValor": 100,
    "ScoreDocumento": 97,
    "StatusDactilar": 0,
    "StatusANIOCR": 1,
    "StatusANIBarCode": 1,
    "StatusOCRBarCode": 1,
    "StatusFace": 1,
    "StatusDocumento": 2,
    "AutenticadoDocumento": false,
    "Async": true,
    "Finalizado": true,
    "ByKeeperData": {
        "Id": "90b2dcd5-daf2-45b6-b48a-276e6dbd755d",
        "Status": 1,
        "VerificationId": "1c3267ee-936e-4d19-a8cd-6d9ad5001c3c",
        "VerificationDate": "2021-03-04T15:29:17.353",
        "ByKeeperStatus": 83,
        "ByKeeperValue": false,
        "FechaInicio": "2021-03-04T15:29:13.873",
        "FechaFin": "2021-03-04T15:29:43.56",
        "ByKeeperMensaje": "Operación Ejecutada Correctamente",
        "Intentos": 22
    },
    "OperacionExitosa": true,
    "Mensaje": "Registro Encontrado"
}
```
 
Descripcion de la respuesta
Objeto **ProcesoValidacionDocumentoResponse**

* **id** = CASB Identificador Interno
* **IdProceso** = Identificador de la transaccion (Enviado por en el Request)
* **NumeroDocumento** = Numero de documento del cliente a procesar
* **TipoDocumento** = Tipo de documento del cliente a procesar
* **FechaProceso** = Fecha de la transaccion
* **ScoreDactilar** = Porcentaje Valor / Porcentaje obtenido con factor Dactilar
* **ScoreDactilarValor** = Valor Autenticacion Dactilar - Valor entre 1 y 15000
* **ScoreANIOCR** = Score obtenido entre la validacion del ANI (Cuando aplica) y el OCR obtenido del documento
* **ScoreANIBarCode** = Score obtenido entre la validacion del ANI (Cuando aplica) y el Codigo de barras Capturado del documento
* **ScoreOCRBarCode** = Score obtenido entre la validacion del OCR obtenido del documento y el Codigo de barras Capturado del documento
* **ScoreFace** = Porcentaje Valor / Porcentaje obtenido con el Factor Facial
* **ScoreFaceValor** = Valor Autenticacion Facial - Valor entre 1 y 100
* **ScoreDocumento** = Porcentaje Valor / Porcentaje Total del documento - Valor entre 1 y 100
 Este Valor se calcula con **ScoreANIOCR + ScoreOCRBarCode + ScoreANIBarCode + ScoreDactilar + ScoreFace**

Los Status presentados tienen 3 posibles valores:
* **0** : No se ha ejecutado
* **1** : Ejecutado y ha sido autenticado
* **2** : Ejecutado y NO ha sido autenticado

* **StatusDactilar** = Status del proceso Dactilar
* **StatusFace** = Status del proceso Facial
* **StatusANIOCR** = Status del proceso Comparacion ANI VS OCR
* **StatusANIBarCode** = Status del proceso Comparacion ANI VS el Codigo de barras obtenido del documento
* **StatusOCRBarCode** = Status del proceso Comparacion OCR VS el Codigo de barras obtenido del documento

Los Siguientes valores definen la validacion o no del documento enviado
* **StatusDocumento** : (0,1) 1 - Documento Autenticado - Otro, documento NO autenticado
* **AutenticadoDocumento** : true/false - true - Documento Autenticado - false documento NO autenticado
* **Async** : true/false Si el proceso se esta procesando de forma asincronica y debe realizarse una consulta posterior para obtener el resultado
* **Finalizado** : true/false Si el proceso ya ha finalizado o esta en proceso

El Elemento **ByKeeperData** representa el objeto de respuesta de validacion Maual asincronico
* **Id** : Identificador interno de la validacion ByKeeper
* **Status** : (1,0) 1 Asignado para proceso - 0 Proceso sin ByKeeper 
* **VerificationId** : Identificador Interno de la verificacion
* **VerificationDate** : Fecha de la Verificacion efectiva
* **ByKeeperStatus** : Identificador del Status de la respuesta
* **ByKeeperValue** : (true/false) **Valor a evaluar si se debe o no aceptar la validacion Manual**
* **FechaInicio** : Fecha de Inicio de la Asignacion a operador
* **FechaFin** : Fecha de Finalizacion del operador

Control del mensaje:
* **OperacionExitosa** : true/false, si el api pudo realizar correctamente el proceso tecnico (Backend calls)
* **Mensaje** : Mensaje del proceso tecnico general
 
---
## <a name="ConsultaProcesoValidacion"></a>ConsultaProcesoValidacion
Esta operacion retorna el estado actual del proceso, incluido si ya finalizo el proceso manual y los diferentes Scores

Url - ConsultaProcesoValidacion - POST

Content-Type: application/json

{{baseUrl}}/CASB/ConsultaProcesoValidacion

```json
{
  "IdentificadorProceso": "PApertura120"
}
```
Ejemplo de llamado usando CURL
```cURL
curl --location --request POST 'https://CASB/ConsultaProcesoValidacion' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiI...' \
--header 'Content-Type: application/json' \
--data-raw '{
  "IdentificadorProceso": "PApertura120"
}'
```

Variables a Enviar:

* **IdentificadorProceso** = Es el identificador de proceso a consultar - Enviado como parametro en la operacion **ValidacionDocumento**

Respuesta
* Objeto [ProcesoValidacionDocumentoResponse](#ProcesoValidacionDocumentoResponse).
 
-------
## <a name="ConsultaUsuarioDocumento"></a>Consulta Usuario Documento
Esta informacion retorna si el usuario se encuentra o no registrado en el sistema y sus factores biometricos asociados

Url - ConsultaUsuarioDocumento - POST

Content-Type: application/json

{{baseUrl}}/CASB/ConsultaUsuarioDocumento

```json
{
  "TipoDocumento": 3,
  "NumeroDocumento": "AW847309"
}
```
Ejemplo de llamado usando CURL
```cURL
curl --location --request POST 'https://CASB/ConsultaUsuarioDocumento' \
--header 'Authorization: Bearer eyJhbGciO' \
--header 'Content-Type: application/json' \
--data-raw '{
  "TipoDocumento": 3,
  "NumeroDocumento": "AW847309"
}'
```

Variables a Enviar:

* **TipoDocumento** = Es el tipo de documento del cliente a consultar
* **NumeroDocumento** = Es el Numero de documento del cliente a consultar

Ejemplo de la respuesta

```json
{
    "Usuario": {
        "EnroladoDactilar": true,
        "EnroladoFacial": false,
        "IdCliente": "13302870-5bec-420e-9354-e6e426bd9205",
        "TipoDocumento": 1,
        "NumeroDocumento": "80208223",
        "PrimerApellido": "DIAZ",
        "SegundoApellido": "BALLESTEROS",
        "PrimerNombre": "ALLAN",
        "SegundoNombre": "YEISSON",
        "NombreCompleto": "ALLAN YEISSON DIAZ BALLESTEROS",
        "Sexo": "M",
        "FechaNacimiento": "19821129",
        "RH": "B+",
        "VersionCedula": "02",
        "TipoDedo1": "2",
        "TipoDedo2": "7",
        "NumeroTarjeta": "0162901171A",
        "FechaCreacion": "2021-12-15T19:08:36.163"
    },
    "OperacionExitosa": true
}
``` 


Descripcion de la respuesta
Objeto **UsuarioResponse**

* **EnroladoDactilar** = (true/false) Retorna si el usuario esta o no enrolado con factor Dactilar
* **EnroladoFacial** = (true/false) Retorna si el usuario esta o no enrolado con factor Facial
* **IdCliente** = Identificador interno del Cliente
* **NumeroDocumento** = Numero de documento del cliente a consultar
* **TipoDocumento** = Tipo de documento del cliente a consultar
* **FechaProceso** = Fecha de la transaccion
* **PrimerApellido** = Primer Apellido del Cliente
* **SegundoApellido** = Segundo Apellido del Cliente
* **PrimerNombre** = Primer Nombre del Cliente
* **SegundoNombre** = Segundo Nombre del Cliente
* **NombreCompleto** = Nombre completo del Cliente
* **Sexo** = Genero del Cliente 
* **FechaNacimiento** = Fecha de Nacimiento del Cliente
* **RH** = RH del Cliente

-------
## <a name="TiposDocumento"></a>Tipos de Documento

| Identificador  | Valor  |  Observaciones |
|---|---|---|
| Cedula Colombia             | 1 | Incluye la Cedula Hologramas y Digital |
| Tarjeta Identidad Colombia  | 2 | N/A |
| Pasaporte                   | 3 | N/A |
| Cedula Digital              | 4 | Explicito Cedula Digital |


-------
## <a name="CodigosByKeeper"></a>Codigos ByKeeper (Validacion Manual)

| Identificador  | Descripcion  | Autorizado |
|---|---|---|
| 110 | Foto Sobrepuesta Documento | NO |
| 111 | Foto Dispositivo           | NO |
| 112 | Documento No Cumple con Dispositivos | NO |
| **113** | **Documento Cumple con Requerimientos** | **SI** |
 
 
