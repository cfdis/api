---
title: API Facturabilidad

language_tabs: # must be one of https://git.io/vQNgJ
  - shell


toc_footers:
  - <a>Código Fuente:</a>
  - <a href='https://github.com/cfdis/shell' target="_blank">Linux shell</a>
search: true

code_clipboard: true
---

<iframe width="560" height="315" src="https://www.youtube.com/embed/SBSEr69PK9U" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

# Introducción

Conecta cualquier sistema (TPV, ERP, eCommerce, etc) con nuestra API REST para emitir Comprobantes Fiscales Digitales en México.

Tenemos ejemplos de implementación en diferentes lenguajes de programación como: Shell.

Para generar tus credenciales de API ve a 
<a href="https://app.facturabilidad.com/v3" target="_blank">app.facturabilidad.com/v3</a>
 elige el RFC a conectar, ve al menú Ajustes->Api y da click en el botón *Generar nueva credencial*.
<aside class="notice">
<a href="https://pruebas.facturabilidad.com/v3/pruebas" target="_blank">pruebas.facturabilidad.com/v3/pruebas</a> para pruebas.
</aside>

# Autenticación

> Para usar el API se requiere Autenticación HTTP básica.


```shell
# Agrega el Header Authorization en cada petición
api_id='API_ID'
api_secret='API_SECRET'
token=$(echo -ne "$api_id:$api_secret" | base64 --wrap 0)

curl "http://backend.pruebas.facturabilidad.com/api"\
  -H "Authorization: Basic $token"

```

> Reemplaza `API_ID` y  `API_SECRET` con tus credenciales.

La cabecera de Autorización se construye como sigue:

1. API_ID y API_SECRET se combinan en una cadena "API_ID:API_SECRET".
1. La cadena resultante se codifica en Base64.
1. El método de autorización y un espacio, es decir, "Basic " se pone a continuación, antes de que la cadena sea codificada.
Por ejemplo, si el API_ID es '123456' y el API_SECRET es 'EWQ321', la cabecera estará formada de la siguiente manera:

`Authorization: Basic MTIzNDU2OkVXUTMyMQ==`

<aside class="notice">
Para generer tus credenciales 
<a href="https://app.facturabilidad.com/" target="_blank">ingresa a tu cuenta </a>, 
elige el RFC que conectarás por medio del API,
ve al menú Ajustes->Api y da click en el botón <b>Generar nueva credencial</b>.

    <p>
        Para <b>pruebas</b> puedes ingresar a 
        <a href="http://pruebas.facturabilidad.com/v3/pruebas" target="_blank">pruebas.facturabilidad.com</a> y utilizar cualquiera de los RFCs de pruebas que están precargados.
    </p>
    <small>Nota: las cuentas de prueba se generan con un correo aleatorio de yopmail y son desechables, la cuenta se pierde cuando se termina la sesión.</small>
</aside>

# CFDI

## Guardar CFDI

> Envía una petición POST con el CFDI en formato JSON. Opcionalmente se puede timbrar el CFDI al mismo tiempo.


```shell
# Enviar el cfdi en formato JSON, 
# puedes usar -d @ para indicar un archivo que contenga el JSON
# o directamente pasar la cadena JSON con -d '$json_cfdi'
curl "http://backend.pruebas.facturabilidad.com/api/Cfdi/guardar"\
    -H "Authorization: Basic $token"\
    -d @cfdi.json # -d '{"Emisor":{ ... } ... }'

```

Envía el CFDI en formato JSON por POST

###HTTP Request

<p>Productivo:</p>
`POST https://backend.facturabilidad.com/api/Cfdi/guardar`

<p>Pruebas:</p>
`POST http://backend.pruebas.facturabilidad.com/api/Cfdi/guardar`


Éste método regresa el código HTTP 200 en caso de éxito y una respuesta JSON.

Y regresará el código HTTP 400 si falla alguna validación y la respuesta String indicando lo que se debe corregir.

A continuacion se muestra un ejemplo del objeto json que se espera en la petición.
cfdi.json
<p style="white-space: pre;background-color: white;overflow: auto;">
{
    "timbrar": true
	"cfdi": {
        "id":null,
		"Comprobante": {
			"Complemento": {
				"ImpuestosLocales": {
					"__prefix__": "implocal:",
					"RetencionesLocales": [],
					"TotaldeRetenciones": 0,
					"TotaldeTraslados": 5,
					"TrasladosLocales": [
						{
							"ImpLocTrasladado": "5 al millar",
							"Importe": 5,
							"TasadeTraslado": 0.5
						}
					],
					"version": "1.0"
				}
			},
			"Conceptos": {
				"Concepto": [
					{
						"_Iva": false,
						"Cantidad": 1,
						"ClaveProdServ": "10214001",
						"ClaveUnidad": "H87",
						"CuentaPredial": [],
						"Descripcion": "cosa",
						"Descuento": "",
						"Importe": 120.89,
						"NoIdentificacion": "21",
						"ObjetoImp": "01",
						"Unidad": null,
						"ValorUnitario": "120.890000"
					}
				]
			},
			"CondicionesDePago": "",
			"Confirmacion": "",
			"Descuento": "",
			"Emisor": {
				"Nombre": "ESCUELA KEMPER URGATE",
				"RegimenFiscal": "626",
				"Rfc": "EKU9003173C9"
			},
			"Exportacion": "01",
			"Fecha": "2023-08-16T02:31:38",
			"FormaPago": "03",
			"LugarExpedicion": "43909",
			"MetodoPago": "PUE",
			"Moneda": "MXN",
			"Receptor": {
				"DomicilioFiscalReceptor": "43909",
				"Nombre": "Publico X",
				"NumRegIdTrib": null,
				"RegimenFiscalReceptor": "616",
				"ResidenciaFiscal": null,
				"Rfc": "XAXX010101000",
				"UsoCFDI": "S01"
			},
			"Sello": null,
			"Serie": "",
			"SubTotal": "120.89",
			"TipoCambio": null,
			"TipoDeComprobante": "I",
			"Total": "125.89",
			"Version": "4.0",
			"xmlns:implocal": "http://www.sat.gob.mx/implocal",
			"xmlns:xsi": "http://www.w3.org/2001/XMLSchema-instance",
			"xsi:schemaLocation": "http://www.sat.gob.mx/cfd/4 http://www.sat.gob.mx/sitio_internet/cfd/4/cfdv40.xsd http://www.sat.gob.mx/implocal http://www.sat.gob.mx/sitio_internet/cfd/implocal/implocal.xsd"
		}
	}
}
</p>

<p>
la propiedad timbrar indica si se desea timbrar el cfdi o solo guardarlo para obtener una vista previa del PDF. Una vez que un cfdi se ha guardado en la respuesta se regresa el id del cfdi generado.
</p>
<p>
Para modificar un cfdi previamente guardado se debe enviar una peticion POST a la ruta /api/Cfdi/guardar con el id del cfdi a modificar en la propiedad id en el objeto cfdi. Solo se pueden modificar los cfdi que no han sido timbrados.
</p>



###HTTP Response

HTTP Code | Ejemplo
--------- | -----------
200 | `{`<br>`"response":{`<br>&nbsp;&nbsp;&nbsp;&nbsp;`"cfdiId":"337"`<br>&nbsp;&nbsp;&nbsp;&nbsp;`"timbrado":true`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"xml":"<cfdi:Comprobante>..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"xmlUrl":"http://..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"pdfUrl":"http://..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;<br>`,"errorTimbrado":""`<br>`}`<br>`}`
200 | `{`<br>`"response":{`<br>&nbsp;&nbsp;&nbsp;&nbsp;`"cfdiId":"337"`<br>&nbsp;&nbsp;&nbsp;&nbsp;`"timbrado":false`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"xml":"<cfdi:Comprobante>..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"xmlUrl":"http://..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"pdfUrl":"http://..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;<br>`,"errorTimbrado":"401 - El rango de la fecha de generación no debe de ser mayor a 72 horas para la emisión del timbre."`<br>`}`<br>`}`
400|`{"response": "No se encontró el cfdi con id: 3457"}`
400|`{"response": "No se permite modificar facturas con estatus Timbrado"}`
500|`{"response": "Error inesperado, por favor verifique en el portal si los datos fueron guardados antes de hacer otro intento."}`


## Timbrar CFDI

> Permite timbrar  un cfdi previamente guardado. Pude hacer una petición POST o GET a la ruta /api/Cfdi/timbrar/{id} donde id es el id del cfdi que se desea timbrar.

```shell
# Envia solicitud GET para timbrar el cfdi con id 337
curl "http://backend.pruebas.facturabilidad.com/api/Cfdi/timbrar/337"\
    -H "Authorization: Basic $token"\

```

###HTTP Request

<p>Productivo:</p>
`POST https://backend.facturabilidad.com/api/Cfdi/timbra/{id}`

<p>Pruebas:</p>
`POST http://backend.pruebas.facturabilidad.com/api/Cfdi/timbra/{id}`



###HTTP Response

HTTP Code | Ejemplo
--------- | -----------
200 | `{`<br>`"response":{`<br>&nbsp;&nbsp;&nbsp;&nbsp;`"cfdiId":"337"`<br>&nbsp;&nbsp;&nbsp;&nbsp;`"timbrado":true`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"xml":"<cfdi:Comprobante>..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"xmlUrl":"http://..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"pdfUrl":"http://..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;<br>`,"errorTimbrado":""`<br>`}`<br>`}`
200 | `{`<br>`"response":{`<br>&nbsp;&nbsp;&nbsp;&nbsp;`"cfdiId":"337"`<br>&nbsp;&nbsp;&nbsp;&nbsp;`"timbrado":false`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"xml":"<cfdi:Comprobante>..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"xmlUrl":"http://..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"pdfUrl":"http://..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;<br>`,"errorTimbrado":"401 - El rango de la fecha de generación no debe de ser mayor a 72 horas para la emisión del timbre."`<br>`}`<br>`}`
400|`{"response": "No se encontró el cfdi con id: 3457"}`
400|`{"response": "No se permite modificar facturas con estatus Timbrado"}`


## Recuperar CFDI previamente guardado

> Permite recuperar un cfdi previamente guardado. Pude hacer una petición GET a la ruta /api/Cfdi//json/{id} donde id es el id del cfdi que se desea recuperar.

```shell
# Envia solicitud GET para recuperar el cfdi con id 337
curl "http://backend.pruebas.facturabilidad.com/api/Cfdi/json/337"\
    -H "Authorization: Basic $token"\

```


###HTTP Request

<p>Productivo:</p>
`GET https://backend.facturabilidad.com/api/Cfdi/json/{id}`
<p>Pruebas:</p>
`GET http://backend.pruebas.facturabilidad.com/api/Cfdi/json/{id}`

###HTTP Response

HTTP Code | Ejemplo
--------- | -----------
200 | `{    "Version": "4.0",    "Serie": null,    "Folio": 1,    "Fecha": "2023-09-07T03:29:22"...}`
400|`{    "response": "No se encontró el cfdi con id: 3445",    "output": ""}`


## Recuperar XML de un CFDI previamente guardado

> Permite recuperar el xml de un cfdi previamente guardado. Pude hacer una petición GET a la ruta /api/Cfdi/xml/{id} donde id es el id del cfdi que se desea recuperar.

```shell
# Envia solicitud GET para recuperar el cfdi con id 337
curl "http://backend.pruebas.facturabilidad.com/api/Cfdi/xml/337"\
    -H "Authorization: Basic $token"\
```

###HTTP Request

<p>Productivo:</p>
`GET https://backend.facturabilidad.com/api/Cfdi/xml/{id}`
<p>Pruebas:</p>
`GET http://backend.pruebas.facturabilidad.com/api/Cfdi/xml/{id}`

###HTTP Response

HTTP Code | Ejemplo
--------- | -----------
200 | `<?xml version="1.0" encoding="UTF-8"?> <cfdi:Comprobante...`
400|`{    "response": "No se encontró el cfdi con id: 3445",    "output": ""}`

## Recuperar PDF de un CFDI previamente guardado

> Permite recuperar el pdf de un cfdi previamente guardado. Pude hacer una petición GET a la ruta /api/Cfdi/pdf/{id} donde id es el id del cfdi que se desea recuperar.

```shell
# Envia solicitud GET para recuperar el cfdi con id 337
curl "http://backend.pruebas.facturabilidad.com/api/Cfdi/pdf/337"\
    -H "Authorization: Basic $token"\
```

###HTTP Request

<p>Productivo:</p>
`GET https://backend.facturabilidad.com/api/Cfdi/pdf/{id}`
<p>Pruebas:</p>
`GET http://backend.pruebas.facturabilidad.com/api/Cfdi/pdf/{id}`

###HTTP Response

HTTP Code | Ejemplo
--------- | -----------
200 | [BINARY_FILE]
400|`{    "response": "No se encontró el cfdi con id: 3445",    "output": ""}`
