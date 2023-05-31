<h1 align="center">JWT: secure your api</h1>

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


#### Best Examples:)

1. Install the necessary dependencies:
   ```
   npm install jsonwebtoken
   ``` 
2. Import the `jsonwebtoken` module into your Node.js application:
   ```javascript
   const jwt = require('jsonwebtoken');
   ```
#### Commone Use Examples:

```javascript
  app.post('/jwt', (req, res) => {
      const user = req.body
      const token = jwt.sign(user, process.env.access_token_secreat_key, { expiresIn: '1h' })
      res.send({token})
    })
```
    
    
#### Create Token Client Side:


```javascript
  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, currentUser => {
      setUser(currentUser)
      // console.log('currentUser',currentUser);
      if (currentUser) {
        axios.post('http://localhost:4000/jwt', { email: currentUser.email })
          .then(data => {
            console.log(data.data.token);
            localStorage.setItem('jwt-token', data.data.token)
          }).catch(error => {
            console.log(`Error:`, error.message);
          })

      }
      else {
        localStorage.removeItem('jwt-token')
      }
      setLoading(false)
    })
    return () => {
      return unsubscribe()
    }
  }, [])
```

#### Server side middelware varifyjwt token:

```javascript
const varifyJwt = (req, res, next) => {
  const authorization = req.headers.authorization
  if (!authorization) {
    return res.status(401).send({error:true,message:'unauthorized access'})
  }
  //bearer token
  const token = authorization.split(' ')[1]
  jwt.verify(token, process.env.access_token_secreat_key, (err, decoded) => {
    if (err) {
      return res.status(403).send({error:true,message:'unauthorized access'})
    }
    req.decoded = decoded
    next()
  })
}
```

#### Chack User Is Valid Server Side:

```javascript
  app.get('/carts', varifyJwt, async (req, res) => {
      const email = req.query.email
      // console.log(email);
      if (!email) {
        res.send([])
      }
      const decodedEmail = req.decoded.email
      if (email !== decodedEmail) {
        return res.status(403).send({ error: true, message: 'forbidden access' })
      }
      const query = { email: email }
      const result = await cartsBossCollection.find(query).toArray()
      res.send(result)
    })
```

#### Jwt token call clien side:)

```javascript
  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, currentUser => {
      setUser(currentUser)
      // console.log('currentUser',currentUser);
      if (currentUser) {
        axios.post('http://localhost:4000/jwt', { email: currentUser.email })
          .then(data => {
            console.log(data.data.token);
            localStorage.setItem('access-token',data.data.token)
          }).catch(error => {
            console.log(`Error:`, error.message);
          })

      }
      else {
        localStorage.removeItem('access-token')
      }
      setLoading(false)
    })
    return () => {
      return unsubscribe()
    }
  }, [])
```


#### jwt token useAxiosSecure clien side:

```javascript
  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, currentUser => {
      setUser(currentUser)
      // console.log('currentUser',currentUser);
      if (currentUser) {
        axios.post('http://localhost:4000/jwt', { email: currentUser.email })
          .then(data => {
            console.log(data.data.token);
            localStorage.setItem('access-token',data.data.token)
          }).catch(error => {
            console.log(`Error:`, error.message);
          })

      }
      else {
        localStorage.removeItem('access-token')
      }
      setLoading(false)
    })
    return () => {
      return unsubscribe()
    }
  }, [])
  ```

#### few change cart part 

```javascript
import { useQuery } from '@tanstack/react-query';
import { useContext } from "react";
import { AuthContext } from '../AuthProvider/AuthProvider';
import UseAxiosSecure from './UseAxiosSecures';
const useCart = () => {
  const { user,loading } = useContext(AuthContext)
  // const token = localStorage.getItem('jwt-token')
  const [axiosSecure]=UseAxiosSecure()
  const {data:cart=[],refetch} = useQuery({
    queryKey: ['cart', user?.email],
    enabled: !loading && !!user?.email && !!localStorage.getItem("access-token"),
    queryFn: async () => {
      const response = await axiosSecure(`http://localhost:4000/carts?email=${user?.email}`)
      console.log("response from axios",response.data);
      return response.data
    },
  })

  return [cart,refetch]
}
export default useCart
```










    
3. Generate SecreatKey:

```javascript
node// write terminal node
```
```javascript
require('crypto').randomBytes(64).toString('hex')
```

4. Generating a JWT token:
   ```javascript
   const payload = { user: 'John Doe' };
   const secretKey = 'your_secret_key';

   const token = jwt.sign(payload, secretKey);
   ```

   In the above example, `payload` represents the data you want to include in the token, and `secretKey` is a secret key used to sign the token. Make sure to replace `'your_secret_key'` with your actual secret key.

5. Verifying a JWT token:
   ```javascript
   const token = 'your_token_here';

   jwt.verify(token, secretKey, (err, decoded) => {
     if (err) {
       // Token is invalid or has expired
       console.error('Token verification failed:', err);
     } else {
       // Token is valid
       console.log('Decoded token:', decoded);
     }
   });
   ```

   In the above example, `your_token_here` represents the token string you want to verify. The `verify` method takes the token, the secret key, and a callback function. If the token is valid, the decoded payload will be passed to the callback function. If the token is invalid or has expired, an error will be passed to the callback.

That's it! You now have a basic understanding of how to use JWT tokens in a Node.js application. Remember to store your secret key securely and avoid sharing it publicly.

