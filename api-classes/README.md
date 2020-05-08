# API classes

### Introduction

API classes helps you to quickly build requests to specific resource via [HTTP endpoints](../http-endpoints.md). Move your logic and backend requests to dedicated classes. Keep your code clean and elegant.

### Define API class

To define API classes, extend your class from `ApiBase`class and declare your own methods.

A good example could be authentication methods:

{% code title="Auth.js" %}
```javascript
import { ApiBase } from 'stylemix-base' 

export default class Auth extends ApiBase {

  /**
   * POST /login
   */
  login(credentials) {
    return this.request('post', 'login', credentials)
  }

  /**
   * POST /register
   */
  register(data) {
    return this.request('post', 'register', data)
  }
}
```
{% endcode %}

### Different endpoint

In case if the resource should use different HTTP endpoint for HTTP requests \(not `$http`\), override `this.httpEndpoint`property with the name of desired one.

{% code title="config.js" %}
```javascript
import Base from 'stylemix-base'

Base.httpEndpoint('$auth', {
    baseUrl: 'https://api.cool-service.com',
})
```
{% endcode %}

{% code title="AuthApi.js" %}
```javascript
import { ApiBase } from 'stylemix-base' 

export default class AuthApi extends ApiBase {

  constructor() {
    this.httpEndpoint = '$auth'
  }
}
```
{% endcode %}

### Use API class

{% code title="Auth.vue" %}
```javascript
import AuthApi from './AuthApi.js'

export default {

  data() {
    return {
      credentials: {
        email: '',
        password: '',
      },
    }
  },

  methods: {
    login() {
      new AuthApi().login(this.credentials).then(result => {
        // user logged in actions...
      })
    }
  }
}
```
{% endcode %}

#### With full response

Sometimes you want to receive full response object:

{% code title="Auth.vue" %}
```javascript
import PostsResource from './PostsResource.js'

const response = await new AuthApi()
  .withResponse()
  .login(credentials)

// response.status
// response.statusText
// response.data
if (response.status === 422) {
  let errors = response.data.errors
}
```
{% endcode %}

See response object specification here: [https://github.com/axios/axios\#response-schema](https://github.com/axios/axios#response-schema)

