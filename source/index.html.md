---
title: API Facturabilidad

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
#   - php
#   - csharp


toc_footers:
  - <a>Código Fuente:</a>
#   - <a href='https://github.com/cfdis/php' target="_blank">PHP</a>
#   - <a href='https://github.com/cfdis/.net' target="_blank">.NET</a>
#   - <a href='https://github.com/cfdis/netcore' target="_blank">.NET Core</a>
  - <a href='https://github.com/cfdis/shell' target="_blank">Linux shell</a>
search: true

code_clipboard: true
---

<iframe width="560" height="315" src="https://www.youtube.com/embed/SBSEr69PK9U" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

# Introducción

Conecta cualquier sistema (TPV, ERP, eCommerce, etc) con nuestra API REST para emitir Comprobantes Fiscales Digitales en México.

Tenemos ejemplos de implementación en diferentes lenguajes de programación como: Shell, PHP, y .NET Core. Puedes ver los ejemplos de código en el área obscura a la derecha, y puedes cambiar el lenguaje de programación de los ejemplos con las pestañas en la esquina superior derecha.

Para generar tus credenciales de API ve a 
<a href="https://app.facturabilidad.com/v3" target="_blank">app.facturabilidad.com/v3</a>
 elige el RFC a conectar, ve al menú Ajustes->Api y da click en el botón *Generar nueva credencial*.
<aside class="notice">
<a href="http://demo.facturabilidad.com/v3/pruebas" target="_blank">pruebas.facturabilidad.com/v3/pruebas</a> para pruebas.
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

```php
use Facturabilidad\CfdiClient;
#crea una instancia de la clase CfdiClient
#Con los parametros API_ID, API_SECRET
#El tercer parámetro indica si se usará el ambiente productivo.
#Si no se indica este parámetro se queda en false(ambiente de pruebas).
$cliente = new CfdiClient("API_ID", "API_SECRET", [false]);
```

```csharp
using Cfdis.App.Api.CfdiClient;
//crea una instancia de la clase CfdiClient
//Con los parámetros API_ID, API_SECRET
//El tercer parámetro indica si se usará el ambiente productivo.
//Si no se indica este parámetro se queda en false(ambiente de pruebas).
CfdiClient cliente;
cliente = new CfdiClient("API_ID", "API_SECRET", [false]);  
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

## Timbrado

> Envía una petición POST con el CFDI en formato JSON.


```shell
# Enviar el cfdi en formato JSON, 
# puedes usar -d @ para indicar un archivo que contenga el JSON
# o directamente pasar la cadena JSON con -d '$json_cfdi'
curl "http://backend.pruebas.facturabilidad.com/api/Cfdi/timbrar"\
    -H "Authorization: Basic $token"\
    -d @cfdi.json # -d '{"Emisor":{ ... } ... }'

```

```php
use Facturabilidad\CfdiClient;
$cliente = new CfdiClient("API_ID", "API_SECRET");
$jsonCfdi = file_get_contents('cfdi.json');
$cfdi = json_decode($jsonCfdi);
$response = $client->timbrar($cfdi);
try {
    $response = $client->timbrar($cfdi);
    if (is_string($response)) {
        echo 'Mensaje: ' . $response;
    } else {
        echo "Cfdi generado exitosamente"
        ." con id {$response->factura->factura_id} "
        ." Descarga el pdf desde {$response->factura->pdfUrl}";
    }
    var_dump($response);
} catch (\Exception $e) {
    echo $e->getMessage();
}
```

```csharp
using Cfdis.App.Api.Client;
CfdiClient cliente;
cliente = new CfdiClient("API_ID", "API_SECRET");  
Cfdi cfdi = new Cfdi();
cfdi.Moneda = "MXN";
cfdi.Receptor = new Receptor();
cfdi.Receptor.Rfc = "XAXX010101000";
cfdi.Receptor.Nombre = "Publico en general";
cfdi.Receptor.UsoCFDI = "G03";

try
{
    ApiResult response = cliente.timbrar(cfdi);

    Console.WriteLine("Cfdi generado exitosamente con id "
    	+ response.cfdiId
        + " Descarga el pdf desde " + response.pdfUrl);
}
catch (Exception e)
{
    Console.WriteLine(e.Message);
}
```

Envía el CFDI en formato JSON por POST

###HTTP Request

<p>Productivo:</p>
`POST https://backend.facturabilidad.com/api/Cfdi/gurdar`

<p>Pruebas:</p>
`POST http://backend.demo.facturabilidad.com/api/Cfdi/gurdar`


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
Para timbrar  un cfdi guardado previamente pude hacer una petición POST o GET a la ruta /api/Cfdi/timbrar/{id} donde id es el id del cfdi que se desea timbrar.
</p>
<p>
Para modificar un cfdi previamente guardado se debe enviar una peticion POST a la ruta /api/Cfdi/gurdar con el id del cfdi a modificar en la propiedad id en el objeto cfdi. Solo se pueden modificar los cfdi que no han sido timbrados.
</p>


###HTTP Response

HTTP Code | Ejemplo
--------- | -----------
200 | `{`<br>`"response":{`<br>&nbsp;&nbsp;&nbsp;&nbsp;`"cfdiId":"337"`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"xml":"<cfdi:Comprobante>..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"xmlUrl":"http://..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;`,"pdfUrl":"http://..."`<br>&nbsp;&nbsp;&nbsp;&nbsp;<br>`,"errorTimbrado":""`<br>`}`<br>`}`
400|`"CFDI33101:cfdi:Comprobante:Fecha - El rango de la fecha de generación es mayor a 72 horas"`
