# Thinkific OAuth VueJS Starter

This is an example of how to setup a Vue.JS application to request access_token from a Thinkific Site.

## Prerequisites

You will need the credentials for your app created in Thinkific Platform.

- client_id: App's client id;
- redirect_uri: App's registered callback uri. For this project will be your app's url + `/callback`.

## Description

The project uses OAuth authorization code flow with Proof of Key for Code Exchange (PKCE) to retrieve the `access_token`.

This flow is recommended when storing app's `client_secret` is not safe.

The application generates a cryptographically random string called `code_verifier`.
The `code_challenge` is created from the `code_verifier` using `SHA256` and converting to base64 urlsafe.

First the `Authorize` link will redirect the user to the Thinkific Site with:

- client_id: app's client_id
- response_type: `code` (informs that the flow to exchange the access_token will be authorization_code flow)
- redirect_uri: app's url + `/callback`
- code_challenge: `base64urlsafe(sha256(codeVerifier))`
- code_challenge_method: `S256` (method used to generate the challenge)
- state: state parameter - *optional*

Once the user gives their consent to the application, Thinkific will redirect the authorization `code` to the `redirect_uri`, in our case, the `/callback` route.

The application then retrieves the code and executes the token request sending the `code_verifier`, `code` and `grant_type`.
Thinkific validates the `code` and `code_verifier` and respond with the `access_token` which then the application can use to invoke Thinkific's API.

## Project setup

Create `.env` file

```
cp .env-example .env
```

and populate the `VUE_APP_CLIENT_ID` with your app's client_id 

```
VUE_APP_CLIENT_ID=
```

Install dependencies:
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```


