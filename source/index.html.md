---
title: API Facturabilidad

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - php
  - csharp


toc_footers:
  - <a href='https://github.com/cfdis/netcore' target="_blank">Ejemplo .NET</a>

search: true

code_clipboard: true
---

# Introducción

Bienbenido al API CFDIS de facturabilidad, Puedes suar esta API para emitir Comprobantes Fiscales Digitales en México.

Tenemos ejemplos de implementacion en diferentes lenguajes de programacion como: Shell, PHP, and .NET Core. Puedes ver los ejemplos de código en el area obscura a la derecha, y puedes cambiar el lenguaje de programacion de los ejemplos con las pestañas en la esquina superior derecha.

Para generar tus credenciales de API ve a https://app.facturabilidad.com/ elige el RFC a conectar, ve al menú Ajustes->Api y da click en el botón *Generar nueva credencial*.

# Authentication

> Para usar el API se requiere Autenticación básica HTTP.


```shell
# Agrega el Header Authorization en cada petición
api_id='API_ID'
api_secret='API_SECRET'
token=$(echo -ne "$api_id:$api_secret" | base64 --wrap 0)

curl "http://backend.demo.facturabilidad.com/api"\
  -H "Authorization: Basic . $token"

```

```php
use Facturabilidad\Cfdi33Client;
#crea una instancia de la clase Cfdi33Client
#Con los parametros API_ID, API_SECRET
#El tercer parametro indica si se usará el ambiente productivo.
#Si no se indica este parámetro se queda en false(ambiente de pruebas).
$cliente = new Cfdi33Client("API_ID", "API_SECRET", [false]);
```

```csharp
using Cfdis.App.Api.Client;
//crea una instancia de la clase Cfdi33Client
//Con los parametros API_ID, API_SECRET
//El tercer parametro indica si se usará el ambiente productivo.
//Si no se indica este parámetro se queda en false(ambiente de pruebas).
Cfdi33Client cliente;
cliente = new Cfdi33Client("API_ID", "API_SECRET", [false]);  
```

> Reemplaza `API_ID` y  `API_SECRET` con tus credenciales.

La cabecera de Autorización se construye como sigue:

1. API_ID y API_SECRET se combinan en una cadena "API_ID:API_SECRET".
1. La cadena resultante se codifica en Base64.
1. El método de autorización y un espacio, es decir, "Basic " se pone a continuación, antes de que la cadena codificada.
Por ejemplo, si el API_ID es '123456' y el API_SECRET es 'EWQ321'cabecera está formada de la siguiente manera:

`Authorization: Basic MTIzNDU2OkVXUTMyMQ==`

<aside class="notice">
Para generer tus credenciales 
<a href="https://app.facturabilidad.com/" target="_blank">ingresa a tu cuenta </a>, 
elige el RFC que conectarás por medio del API,
ve al menú Ajustes->Api y da click en el botón <b>Generar nueva credencial</b>.

</aside>

