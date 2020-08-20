---
title: API Facturabilidad

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - php
  - csharp


toc_footers:
  - <a>Código Fuente:</a>
  - <a href='https://github.com/cfdis/php' target="_blank">PHP</a>
  - <a href='https://github.com/cfdis/.net' target="_blank">.NET</a>
  - <a href='https://github.com/cfdis/netcore' target="_blank">.NET Core</a>
search: true

code_clipboard: true
---

# Introducción

Conecta cualquier sistema (TPV, ERP, eCommerce, etc) con nuestra API REST para emitir Comprobantes Fiscales Digitales en México.

Tenemos ejemplos de implementación en diferentes lenguajes de programación como: Shell, PHP, y .NET Core. Puedes ver los ejemplos de código en el área obscura a la derecha, y puedes cambiar el lenguaje de programación de los ejemplos con las pestañas en la esquina superior derecha.

Para generar tus credenciales de API ve a https://app.facturabilidad.com/ elige el RFC a conectar, ve al menú Ajustes->Api y da click en el botón *Generar nueva credencial*.

# Autenticación

> Para usar el API se requiere Autenticación HTTP básica.


```shell
# Agrega el Header Authorization en cada petición
api_id='API_ID'
api_secret='API_SECRET'
token=$(echo -ne "$api_id:$api_secret" | base64 --wrap 0)

curl "http://backend.demo.facturabilidad.com/api"\
  -H "Authorization: Basic $token"

```

```php
use Facturabilidad\Cfdi33Client;
#crea una instancia de la clase Cfdi33Client
#Con los parametros API_ID, API_SECRET
#El tercer parámetro indica si se usará el ambiente productivo.
#Si no se indica este parámetro se queda en false(ambiente de pruebas).
$cliente = new Cfdi33Client("API_ID", "API_SECRET", [false]);
```

```csharp
using Cfdis.App.Api.Client;
//crea una instancia de la clase Cfdi33Client
//Con los parámetros API_ID, API_SECRET
//El tercer parámetro indica si se usará el ambiente productivo.
//Si no se indica este parámetro se queda en false(ambiente de pruebas).
Cfdi33Client cliente;
cliente = new Cfdi33Client("API_ID", "API_SECRET", [false]);  
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

</aside>

# CFDI

## Timbrado

> Envía una petición POST con el CFDI en formato JSON.


```shell
# Enviar el cfdi en formato JSON, 
# puedes usar -d @ para indicar un archivo que contenga el JSON
# o directamente pasar la cadena JSON con -d '$json_cfdi'
curl "http://backend.demo.facturabilidad.com/api"\
    -H "Authorization: Basic $token"\
    -d @cfdi.json # -d '{"Emisor":{ ... } ... }'

```

```php
use Facturabilidad\Cfdi33Client;
$cliente = new Cfdi33Client("API_ID", "API_SECRET");
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
Cfdi33Client cliente;
cliente = new Cfdi33Client("API_ID", "API_SECRET");  
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

`POST http://backend.demo.facturabilidad.com/api/Cfdi33/timbrar`


Éste método regresa el código HTTP 200 en caso de éxito y una respuesta JSON.

Y regresará el código HTTP 400 si falla alguna validación y la respuesta String indicando lo que se debe corregir.

###HTTP Response

HTTP Code | Ejemplo
--------- | -----------
200 | `{`<br>`"success":true`<br>`,"factura":{`<br>`"factura_id":"378961"`<br>`,"xml":"<cfdi:Comprobante>..."`<br>`,"xmlUrl":"http://..."`<br>`,"pdf":"http://..."`<br>`,"pdfUrl":"http://..."`<br>`,"notas":"Facturageneradaconu00e9xito!"`<br>`}`<br>`}`
400|`"CFDI33101:cfdi:Comprobante:Fecha - El rango de la fecha de generación es mayor a 72 horas"`
