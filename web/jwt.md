# JSON Web Tokens
*[Source material](http://jwt.io/introduction/)*

JWT is a string on the form
```
header.payload.signature
```
It is meant to be *compact* enough to be sent through an *GET* or *POST* requests or inside an *HTTP header*, and *self-contained* to avoid additional querying. *JWT*s are useful for *authentication* and *information exchange*. Use http://jwt.io/ to test *JWT*s and browse implementations.

Once user has successfully logged in, server generates the *JWT* and sends to the client. The client can then store the *JWT* and send along with all future requests to *authenticate* and *authorize*, e.g.

```
Authorization: Bearer <JWT>
```

A *JWT* is secured even over HTTP.

### Header
*Base64URL* encoded information that typically consists of two parts, type of the token (*JWT*) and the hashing algorithm, e.g.
``` json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
Encryption algorithm is either HMAC (using a secret) or RSA (with a private/public key pair).
### Payload
Contains *Base64URL* encoded *claims*, i.e. statements about an entity, typically the user, and additional metadata. The types of claims are:
- **Reserved claims**: Set of recommended predefined claims. Examples are **iss** (issuer), **exp** (expiration time), **sub** (subject), and **aud** (audience).
- **Public claims**: Can be defined at will. Consult the [IANA JSON Web Token Registry](http://www.iana.org/assignments/jwt/jwt.xhtml) to avoid name collisions.
- **Private claims**: Custom claims.

Example:
``` json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```
### Signature
Encoded information in both header and payload with a secret. Use the algorithm specified in header to encode.
```js
HMACSHA256(
  base64UrlEncode(header) + '.' + base64UrlEncode(payload),
  secret
)
```
### Implementations
Node JWT: https://github.com/auth0/node-jsonwebtoken
Express JWT: https://github.com/auth0/express-jwt
