# Keycloak

## OAuth 2.0

OAuth 2.0 defines four key roles:

- **Resource Owner**: The end user who owns the data.
- **Resource Server**: Hosts the protected resources (e.g., REST APIs).
- **Client**: The application accessing resources.
- **Authorization Server**: Issues access tokens (e.g., Keycloak).

OAuth 2.0 Flows:

- **Client Credentials Flow**: For machine-to-machine communication where the application accesses resources on its own
  behalf.
- **Device Flow**: For devices without browsers (e.g., smart TVs).
- **Authorization Code Flow**: Default for most web/mobile applications.

Client Types:

- **Confidential Clients**: Can securely store credentials (e.g., backend services).
- **Public Clients**: Cannot store credentials securely (e.g., single-page apps).

Safeguards for public clients:

- Use pre-configured redirect URIs.
- Use **PKCE** (Proof Key for Code Exchange) to prevent token interception.

Tokens:

- **Access Token**: Short-lived; passed in requests to access resources.
- **Refresh Token**: Long-lived; allows refreshing access tokens without re-authentication.
- **ID Token**: JWT containing user identity; unlike opaque OAuth tokens, it is inspectable.

## OpenID Connect

OpenID Connect is an authentication layer built on top of OAuth 2.0.

Key Roles:

- **End User**: The person authenticating.
- **Relying Party (RP)**: The application requesting user authentication.
- **OpenID Provider (OP)**: The identity provider (e.g., Keycloak).

OpenID Connect Flows:

- **Authorization Code Flow**: Returns ID token, access token, and refresh token.
- **Hybrid Flow**: Returns ID token and authorization code immediately.

OpenID Connect requires `scope=openid` to initiate authentication.

Additional Specs:

- **Discovery**: Dynamic provider configuration.
- **Dynamic Registration**: Clients can register themselves dynamically.
- **Session Management**, **Front-Channel Logout**, **Back-Channel Logout**: For logout and session lifecycle handling.

## Keycloak Overview

Keycloak acts as an identity and authorization server.

Endpoints:

- **Frontend**: User login, logout, and consent.
- **Backend**: Token issuance and validation.
- **Admin**: Realm and client management.

Roles:

- **Realm Roles**: Assigned at realm level (organization-wide).
- **Client Roles**: Specific to a client.
- **Composite Roles**: Combine multiple roles.

> Prefer **groups** over composite roles for assigning multiple roles to users.

Groups:

- Not included in tokens by default.
- Add a **protocol mapper** to include group info in tokens.

Authorization Services:

- Define policies and resource access rules.
- Manage via Keycloak Admin UI or REST API.

## Spring Boot Integration

### Spring Boot Client (Web App)

**Dependencies**:

```xml

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-client</artifactId>
</dependency>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-security</artifactId>
</dependency>
````

**Configuration (`application.yaml`)**:

```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          myfrontend:
            provider: keycloak
            client-id: mywebapp
            client-secret: CLIENT_SECRET
            authorization-grant-type: authorization_code
            redirect-uri: "{baseUrl}/login/oauth2/code/"
            scope: openid
        provider:
          keycloak:
            authorization-uri: http://localhost:8180/auth/realms/myrealm/protocol/openid-connect/auth
            token-uri: http://localhost:8180/auth/realms/myrealm/protocol/openid-connect/token
            jwk-set-uri: http://localhost:8180/auth/realms/myrealm/protocol/openid-connect/certs
```

Replace `CLIENT_SECRET` with the value from Keycloak.

### Spring Boot Resource Server (Backend API)

**Dependencies**:

```xml

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
</dependency>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

**Configuration (`application.yaml`)**:

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: http://localhost:8180/auth/realms/myrealm
```

**Get Access Token (Resource Owner Password Flow)**:

```bash
export access_token=$(curl -X POST \
  http://localhost:8180/auth/realms/myrealm/protocol/openid-connect/token \
  --user mybackend:CLIENT_SECRET \
  -H 'content-type: application/x-www-form-urlencoded' \
  -d 'username=alice&password=alice&grant_type=password' \
  | jq --raw-output '.access_token')
```

**Use Token to Access API**:

```bash
curl -X GET http://localhost:8080 \
  -H "Authorization: Bearer $access_token"
```

## Running Keycloak with Docker

**Run Keycloak Dev Mode**:

```bash
docker run -p 28080:8080 \
  -e KEYCLOAK_ADMIN=admin \
  -e KEYCLOAK_ADMIN_PASSWORD=admin \
  quay.io/keycloak/keycloak:18.0.0 start-dev
```

**Admin Console**:
[http://localhost:28080/admin/](http://localhost:28080/admin/)

**Get Started with Docker**:
[https://www.keycloak.org/getting-started/getting-started-docker](https://www.keycloak.org/getting-started/getting-started-docker)
