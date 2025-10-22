
The `.well-known` standard, defined in `RFC 8615`, specifies a standardized directory within a website’s root domain, typically accessible via the `/.well-known/` path. It centralizes critical `metadata` such as `configuration files` and details about `services`, `protocols`, and `security mechanisms`.

By creating a consistent location for such data, `.well-known` simplifies `discovery` and `access` for `web browsers`, `applications`, and `security tools`. Clients can automatically retrieve configuration data by requesting the appropriate `URL` — for example, `https://example.com/.well-known/security.txt` for a site’s `security policy`.

The `Internet Assigned Numbers Authority (IANA)` maintains a registry of `.well-known URIs`, each defined by its own specification.

|URI Suffix|Description|Status|Reference|
|---|---|---|---|
|`security.txt`|Contains contact details for `security researchers` to report `vulnerabilities`.|Permanent|RFC 9116|
|`/.well-known/change-password`|Standard URL for directing users to a password change page.|Provisional|W3C Draft|
|`openid-configuration`|Defines details for `OpenID Connect`, an identity layer on `OAuth 2.0`.|Permanent|OpenID Specification|
|`assetlinks.json`|Verifies ownership of `digital assets` (e.g., apps) tied to a domain.|Permanent|Google Digital Asset Links|
|`mta-sts.txt`|Defines policy for `SMTP MTA Strict Transport Security (MTA-STS)`.|Permanent|RFC 8461|

This list is only a small subset of `.well-known URIs` registered with `IANA`. Each defines how clients should interpret and use the data stored there, ensuring interoperability across `web services` and `security frameworks`.

# Web Reconnaissance and .well-known`

In `web reconnaissance`, `.well-known` URIs can expose useful information about `endpoints` and `configurations` that assist during `penetration testing`. A particularly valuable URI is `openid-configuration`.

The `openid-configuration` URI belongs to the `OpenID Connect Discovery` protocol, built on top of `OAuth 2.0`. When a client application uses `OpenID Connect` for authentication, it retrieves configuration data from `https://example.com/.well-known/openid-configuration`. The response is a JSON document describing the `authorization endpoints`, `token issuance`, `user information endpoints`, `cryptographic keys`, and more.

Example output:

```bash
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

Information from this endpoint provides key exploration points:

`Endpoint Discovery`

- `Authorization Endpoint`: Identifying the URL for user authorization requests.
    
- `Token Endpoint`: Finding the URL where tokens are issued.
    
- `Userinfo Endpoint`: Locating the endpoint that provides user information.
    

`JWKS URI`: The `jwks_uri` reveals the `JSON Web Key Set (JWKS)`, detailing the cryptographic keys used by the server.

`Supported Scopes` and `Response Types`: Understanding which scopes and response types are supported helps in mapping out the functionality and limitations of the `OpenID Connect` implementation.

`Algorithm Details`: Information about supported signing algorithms can be crucial for understanding the security measures in place.

