El estándar `.well-known`, definido en [RFC 8615](https://datatracker.ietf.org/doc/html/rfc8615) , sirve como un directorio estandarizado dentro del dominio raíz de un sitio web. Esta ubicación designada, generalmente accesible en , centraliza los metadatos críticos de un sitio web, incluidos los archivos de configuración y la información relacionada con sus servicios, protocolos y mecanismos de seguridad. Al establecer una ubicación consistente para dichos datos, `.well-known`simplifica el proceso de descubrimiento y acceso para diversas partes interesadas, incluidos navegadores web, aplicaciones y herramientas de seguridad. Este enfoque simplificado permite a los clientes localizar y recuperar automáticamente archivos de configuración específicos mediante la construcción de la URL adecuada. Por ejemplo, para acceder a la política de seguridad de un sitio web, un cliente solicitaría `https://example.com/.well-known/security.txt`. El `Internet Assigned Numbers Authority` ( `IANA`) mantiene un [registro](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml) de `.well-known`URI, cada uno de los cuales tiene un propósito específico definido por varias especificaciones y estándares. A continuación se muestra una tabla que destaca algunos ejemplos notables:

| Sufijo URI                     | Descripción                                                                                                                     | Estado      | Referencia                                                                              |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ----------- | --------------------------------------------------------------------------------------- |
| `security.txt`                 | Contiene información de contacto para que los investigadores de seguridad informen vulnerabilidades.                            | Permanente  | RFC 9116                                                                                |
| `/.well-known/change-password` | Proporciona una URL estándar para dirigir a los usuarios a una página de cambio de contraseña.                                  | Provisional | https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri |
| `openid-configuration`         | Define los detalles de configuración para OpenID Connect, una capa de identidad además del protocolo OAuth 2.0.                 | Permanente  | http://openid.net/specs/openid-connect-discovery-1_0.html                               |
| `assetlinks.json`              | Se utiliza para verificar la propiedad de activos digitales (por ejemplo, aplicaciones) asociados con un dominio.               | Permanente  | https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md     |
| `mta-sts.txt`                  | Especifica la política de seguridad de transporte estricta SMTP MTA (MTA-STS) para mejorar la seguridad del correo electrónico. | Permanente  | RFC 8461                                                                                |
## Web Recon and .well-known
En reconocimiento web, el `.well-known`Los URI pueden ser invaluables para descubrir puntos finales y detalles de configuración que se pueden probar más durante una prueba de penetración. Un URI particularmente útil es `openid-configuration`.
El `openid-configuration`URI es parte del protocolo OpenID Connect Discovery, una capa de identidad construida sobre el protocolo OAuth 2.0. Cuando una aplicación cliente desea utilizar OpenID Connect para la autenticación, puede recuperar la configuración del proveedor de OpenID Connect accediendo a `https://example.com/.well-known/openid-configuration`punto final. Este punto final devuelve un documento JSON que contiene metadatos sobre los puntos finales del proveedor, métodos de autenticación admitidos, emisión de tokens y más:
```json
{
  "issuer": "https://example.com",
  "authorization_endpoint": "https://example.com/oauth2/authorize",
  "token_endpoint": "https://example.com/oauth2/token",
  "userinfo_endpoint": "https://example.com/oauth2/userinfo",
  "jwks_uri": "https://example.com/oauth2/jwks",
  "response_types_supported": ["code", "token", "id_token"],
  "subject_types_supported": ["public"],
  "id_token_signing_alg_values_supported": ["RS256"],
  "scopes_supported": ["openid", "profile", "email"]
}
```
Para el reconocimiento web, la información obtenida del `openid-configuration` endpoint ofrece múltiples oportunidades de exploración:

1. `Endpoint Discovery`:
    - `Authorization Endpoint`: Identificar la URL para las solicitudes de autorización de usuarios.
    - `Token Endpoint`: Encontrar la URL donde se emiten los tokens.
    - `Userinfo Endpoint`: Localización del punto final que proporciona información del usuario.
2. `JWKS URI`: El `jwks_uri` revela el `JSON Web Key Set` ( `JWKS`), detallando las claves criptográficas utilizadas por el servidor.
3. `Supported Scopes and Response Types`: Comprender qué alcances y tipos de respuesta se admiten ayuda a mapear la funcionalidad y las limitaciones de la implementación de OpenID Connect.
4. `Algorithm Details`: La información sobre los algoritmos de firma admitidos puede ser crucial para comprender las medidas de seguridad implementadas.

https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml