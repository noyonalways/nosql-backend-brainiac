<h1 align='center'>Building PH University Management System Part-7</h1>

## Topics:

1. Authentication, Authorization, Create interface, controller, validation service
2. Implement business logic validation in auth service.
3. Implement statics for validation
4. Introduction to JWT, create an accessToken.
5. Create auth middleware
6. Verify token using auth
7. Implement authorization using auth
8. Fix sensitive password bugs
9. Implement password updating and track timestamp
10. Refactoring auth and add validations more validations
11. Add password changed validation into auth
12. Create refresh token when login and sent it to client
13. Implement refresh token route to get new access token every time

## Table of Contents:

- [Intro to JWT , create an access Token.](#intro-to-jwt--create-an-access-token)
  - [How JWT Token Looks Like:](#how-jwt-token-looks-like)
  - [JWT Payload](#jwt-payload)
  - [JWT Signature](#jwt-signature)
  - [How JWT Token is created](#how-jwt-token-is-created)
  - [How JWT Token is Verified](#how-jwt-token-is-verified)
  - [How JWT Token is Decoded](#how-jwt-token-is-decoded)
- [Create refresh token when login and sent it to client](#create-refresh-token-when-login-and-sent-it-to-client)
  - [Why Cookie?](#why-cookie)

# Intro to JWT , create an access Token.

### How JWT Token Looks Like:

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c`

- 1st part → Header
- 2nd part → Payload
- 3rd part → Signature

### JWT Payload

Includes user information or any custom data. E.g. `email` `userId` `issued at` `expire at` etc.

### JWT Signature

Ensures the integrity of the token, Created by signing the header and payload.

### How JWT Token is created

`Payload` & `secretKey` is needed to create a JWT token. You can give `token expiration time` additionally

### How JWT Token is Verified

- `secretKey` is needed to verify a JWT token
- `Token verification` checks if the `secret key` provided ha been used to sign this token
- It also checks if the token has expired.
- If the token is verified, then the decoded token is returned with the `payload data`

### How JWT Token is Decoded

- no `secretKey` is needed to decode a JWT token.
- It returns the decoded token with payload but never checks if token is verified or if the token has expired

# Create refresh token when login and sent it to client

### Why Cookie?

Because cookie is secure than local storage. We can `modify` / `access` local storage using JavaScript! But Cookie can not be `accessed` / `modified!`

If we use Cooke, browser automatically sends the cookies to the server with every request.
