# Auth-App

The auth-app service provides authentication for 3rd party apps.

## The `auth` Service Family

ocis uses serveral authentication services for different use cases. All services that start with `auth-` are part of the authentication service family. Each member authenticates requests with different scopes. As of now, these services exist:
  -   `auth-app` handles authentication of external 3rd party apps
  -   `auth-basic` handles basic authentication
  -   `auth-bearer` handles oidc authentication
  -   `auth-machine` handles interservice authentication when a user is impersonated
  -   `auth-service` handles interservice authentication when using service accounts

## Service Startup

Because this service is not started automatically, a manual start needs to be initiated which can be done in several ways. To configure the service usage, an environment variable for the proxy service needs to be set to allow app authentication.
```bash
OCIS_ADD_RUN_SERVICES=auth-app  # deployment specific. Add the service to the manual startup list, use with binary deployments. Alternatively you can start the service explicitly via the command line.
PROXY_ENABLE_APP_AUTH=true      # mandatory, allow app authentication. In case of a distributed environment, this envvar needs to be set in the proxy service.
```

## App Tokens

App Tokens are used to authenticate 3rd party access via https like when using curl (apps) to access an API endpoint. These apps need to authenticate themselves as no logged in user authenticates the request. To be able to use an app token, one must first create a token. There are different options of creating a token.

### Via CLI (dev only)

Replace the `user-name` with an existing user. For the `token-expiration`, you can use any time abbreviation from the following list: `h, m, s`. Examples: `72h` or `1h` or `1m` or `1s.` Default is `72h`.

```bash
ocis auth-app create --user-name={user-name} --expiration={token-expiration}
```

Once generated, these tokens can be used to authenticate requests to ocis. They are passed as part of the request as `Basic Auth` header.

### Via API

The `auth-app` service provides an API to create (POST), list (GET) and delete (DELETE) tokens at `/auth-app/tokens`.

### Via Impersonation API

When setting the environment variable `AUTH_APP_ENABLE_IMPERSONATION` to `true`, admins will be able to use the `/auth-app/tokens` endpoint to create tokens for other users. This is crucial for migration scenarios,
but should not be used on a productive system.
