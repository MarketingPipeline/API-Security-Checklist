# API Security Checklist

Checklist of the most important security countermeasures when designing, testing, and releasing your API.

---
## Design

- [ ] Make sure that all the RESTful APIs have Swagger definitions (e.g. `format`, `maximum`, `minimum`, `minItems`, `maxItems`) [See Swagger documentation for more information](https://swagger.io/docs/specification/describing-parameters/)
- [ ] Make sure that the Swagger definitions contain input validation and all expected header parameters
- [ ] Make sure that the definitions contains [authentication and authorisaiton requirements](https://swagger.io/docs/specification/authentication/)
- [ ] Make sure that the API definitions contain the expected `Content-Type`
- [ ] Use an API gateway to expose APIs 

## Input Validation

- [ ] Validate all user-supplied input in the headers and in the body before they are processed
- [ ] Validate the type (e.g. integer, string, floating point number), size (e.g. minimum string lengths, minimum abd maximum values for numbers) and format the composition of expected inputs
- [ ] Validate the sizes of the JSON arrays and number of child elements in XML requests
- [ ] Use built-in libraries or annotations for input validation as much as possible, build custom validators if built-in functionality is inadequate 
- [ ] Validate the size of the request body and request headers (at preferably at the API gateway)
- [ ] Validate that the `content-type` in the request header matches the expected content type
- [ ] Do not attempt to sanitise input (e.g. remove certain bad characters or strings) 
- [ ] Make sure that if input validation fail, the request is rejected with an appropriate error HTTP response

## Access Control

### Rate Limiting
- [ ] Make sure that rate limiting/throttling is applied to each API based on either per-session or per-IP or based on other properties that's relevant

### Authentication and Authorisation 
- [ ] Avoid developing custom authentication protocols as much as possible
- [ ] Use an identity provider (IDP) and use open standards and frameworks such as OpenID Connect for user identity and OAuth to delegate authorisation to API resources
- [ ] Avoid [HTTP basic authentication](https://tools.ietf.org/html/rfc7617) or [OAuth 2.0 Client Credentials Grant](https://oauth.net/2/grant-types/client-credentials/) for user authorisaion
- [ ] Use [Authorizaion Code grant](https://oauth.net/2/grant-types/authorization-code/) with [PKCE (Proof Key for Code Exchange)](https://oauth.net/2/pkce/) instead of the [Implicit Grant](https://oauth.net/2/grant-types/implicit/) for Single Page Applications (SPAs) 


## Security Configuration
- [ ] Make sure that APIs are exposed through secure channels such as TLS
- [ ] Make sure that debug logging or error messages are disabled in production deployments
- [ ] Make sure that monitoring and diagnostic endpoints provided by frameworks (e.g. [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints)) are either disabled or secured (HTTPS) and the exposure is controlled

## Logging 
- [ ] Do not log entire the HTTP request or the HTTP headers or the entire request body as they can potentially contain sensitive information
- [ ] Do not log user and system credentials 
- [ ] Do not log user session information (Cookies, JWT tokens, etc)

## Build
- [ ] Use third-party components that do not have vulnerabilities
- [ ] Make sure to incorporate security testing into CI/CD processes

## Security Testing
- [ ] Make sure that static application security testing (SAST) is performed
- [ ] Make sure that software composition analysis (SCA) scanning is performed 
- [ ] Make sure that dynamic application security testing (DAST) is performed

---

# Changes To Add



---

## Authentication
- [ ] Don't use `Basic Auth`. Use standard authentication instead (e.g. [JWT](https://jwt.io/), [OAuth](https://oauth.net/)).
- [ ] Don't reinvent the wheel in `Authentication`, `token generation`, `password storage`. Use the standards.
- [ ] Use `Max Retry` and jail features in Login.
- [ ] Use encryption on all sensitive data.

### JWT (JSON Web Token)
- [ ] Use a random complicated key (`JWT Secret`) to make brute forcing the token very hard.
- [ ] Don't extract the algorithm from the header. Force the algorithm in the backend (`HS256` or `RS256`).
- [ ] Make token expiration (`TTL`, `RTTL`) as short as possible.
- [ ] Don't store sensitive data in the JWT payload, it can be decoded [easily](https://jwt.io/#debugger-io).

### OAuth
- [ ] Always validate `redirect_uri` server-side to allow only whitelisted URLs.
- [ ] Always try to exchange for code and not tokens (don't allow `response_type=token`).
- [ ] Use `state` parameter with a random hash to prevent CSRF on the OAuth authentication process.
- [ ] Define the default scope, and validate scope parameters for each application.

## Access
- [ ] Limit requests (Throttling) to avoid DDoS / brute-force attacks.
- [ ] Use HTTPS on server side to avoid MITM (Man in the Middle Attack).
- [ ] Use `HSTS` header with SSL to avoid SSL Strip attack.
- [ ] For private APIs, only allow access from whitelisted IPs/hosts.

## Input
- [ ] Use the proper HTTP method according to the operation: `GET (read)`, `POST (create)`, `PUT/PATCH (replace/update)`, and `DELETE (to delete a record)`, and respond with `405 Method Not Allowed` if the requested method isn't appropriate for the requested resource.
- [ ] Validate `content-type` on request Accept header (Content Negotiation) to allow only your supported format (e.g. `application/xml`, `application/json`, etc.) and respond with `406 Not Acceptable` response if not matched.
- [ ] Validate `content-type` of posted data as you accept (e.g. `application/x-www-form-urlencoded`, `multipart/form-data`, `application/json`, etc.).
- [ ] Validate user input to avoid common vulnerabilities (e.g. `XSS`, `SQL-Injection`, `Remote Code Execution`, etc.).
- [ ] Don't use any sensitive data (`credentials`, `Passwords`, `security tokens`, or `API keys`) in the URL, but use standard Authorization header.
- [ ] Use an API Gateway service to enable caching, Rate Limit policies (e.g. `Quota`, `Spike Arrest`, or `Concurrent Rate Limit`) and deploy APIs resources dynamically.

## Processing
- [ ] Check if all the endpoints are protected behind authentication to avoid broken authentication process.
- [ ] User own resource ID should be avoided. Use `/me/orders` instead of `/user/654321/orders`.
- [ ] Don't auto-increment IDs. Use `UUID` instead.
- [ ] If you are parsing XML files, make sure entity parsing is not enabled to avoid `XXE` (XML external entity attack).
- [ ] If you are parsing XML files, make sure entity expansion is not enabled to avoid `Billion Laughs/XML bomb` via exponential entity expansion attack.
- [ ] Use a CDN for file uploads.
- [ ] If you are dealing with huge amount of data, use Workers and Queues to process as much as possible in background and return response fast to avoid HTTP Blocking.
- [ ] Do not forget to turn the DEBUG mode OFF.

## Output
- [ ] Send `X-Content-Type-Options: nosniff` header.
- [ ] Send `X-Frame-Options: deny` header.
- [ ] Send `Content-Security-Policy: default-src 'none'` header.
- [ ] Remove fingerprinting headers - `X-Powered-By`, `Server`, `X-AspNet-Version`, etc.
- [ ] Force `content-type` for your response. If you return `application/json`, then your `content-type` response is `application/json`.
- [ ] Don't return sensitive data like `credentials`, `Passwords`, or `security tokens`.
- [ ] Return the proper status code according to the operation completed. (e.g. `200 OK`, `400 Bad Request`, `401 Unauthorized`, `405 Method Not Allowed`, etc.).

## CI & CD
- [ ] Audit your design and implementation with unit/integration tests coverage.
- [ ] Use a code review process and disregard self-approval.
- [ ] Ensure that all components of your services are statically scanned by AV software before pushing to production, including vendor libraries and other dependencies.
- [ ] Design a rollback solution for deployments.
