# API Security Checklist
This is a simplified security checklist for anyone who's developing and deploying APIs

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
