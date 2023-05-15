# JWT: secure your api

## CREATE TOKEN

1. Client: After user login, send user basic info to create a token.

2. On the server side, install `npm i jsonwebtoken`.

3. Import `jsonwebtoken`.

4. `jwt.sign(payload, secret, {expires})`.

5. Return the token to the client side.

6. After receiving the token, store it either in HTTP-only cookies or localStorage (second-best solution).

7. Use a general space onAuthStateChange > AuthProvider.

## SEND TOKEN TO SERVER

1. For sensitive API calls, send authorization headers:
   `{ authorization: 'Bearer token'}`.

## VERIFY TOKEN

1. Create a function called `verifyJWT` (middleware).

2. This function will have three parameters: `req`, `res`, `next`.

3. First, check whether the authorization headers exist.

4. If not, send a 401 response.

5. Get the token out of the authorization header.

6. Call `jwt.verify(token, secret, (err, decoded))`.

7. If there is an error, send a 401 response.

8. Set `decoded` to the `req` object so that we can retrieve it later.

9. Call `next()` to go to the next function.

1. Check whether the token has the email that matches the request email.
