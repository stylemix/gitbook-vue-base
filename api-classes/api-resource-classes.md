# API resource classes

### Define API Resource

Assume we have a Posts resource under `https://cool.service.com/api/posts` Api endpoint:

{% code title="PostsResource.js" %}
```javascript
import { ApiResource } from 'stylemix-base' 

export default class PostsResource extends ApiResource {

  constructor() {
    // first argument is the base path followed by base API url
    super('posts')
  }
}
```
{% endcode %}

#### Available methods

<table>
  <thead>
    <tr>
      <th style="text-align:left">Method</th>
      <th style="text-align:left">HTTP request</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>get()</code>
      </td>
      <td style="text-align:left">GET /posts</td>
      <td style="text-align:left">List of items</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>find(id)</code>
      </td>
      <td style="text-align:left">GET /posts/{id}</td>
      <td style="text-align:left">Single item</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>create()</code>
      </td>
      <td style="text-align:left">GET /posts/create</td>
      <td style="text-align:left">Data for creation (fields structure)</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>store(data)</code>
      </td>
      <td style="text-align:left">
        <p>POST /posts</p>
        <p>{data}</p>
      </td>
      <td style="text-align:left">Store new item</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>edit(id)</code>
      </td>
      <td style="text-align:left">GET /posts/{id}/edit</td>
      <td style="text-align:left">Data for modification (item data, fields)</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>update(id, data)</code>
      </td>
      <td style="text-align:left">
        <p>PUT /posts/{id}</p>
        <p>{data}</p>
      </td>
      <td style="text-align:left">Update existing item</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>destroy(id)</code>
      </td>
      <td style="text-align:left">DELETE /posts/{id}</td>
      <td style="text-align:left">Delete existing item</td>
    </tr>
  </tbody>
</table>#### More methods

Your resource may have as much additional methods as you need:

{% code title="PostsResource.js" %}
```javascript
import { ApiResource } from 'stylemix-base' 

export default class PostsResource extends ApiResource {

  constructor() {
    // first argument is the base path followed by base API url
    super('posts')
  }

  /**
   * GET /posts/statuses
   */
  statuses() {
    return this.request('get', this.withBasePath('statuses'))
  }

  /**
   * PUT /posts/{id}/publish
   */
  publish(id) {
    return this.request('put', this.withBasePath(`${id}/publish`))
  }
}
```
{% endcode %}

`withBasePath(path)` will build URL path prefixed with basePath of your resource.

### Use API Resource

{% code title="PostsPage.vue" %}
```javascript
import PostsResource from './PostsResource.js'

export default {

  data() {
    return {
      posts: [],
      statuses: {},
    }
  },

  async mounted() {
    const { data } = await new PostsResource().get()
    this.posts = data

    // or use fetch mode
    this.posts = await new PostsResource().$get()
    
    // custom defined methods
    this.statuses = await new PostsResource().statuses()
  }
}
```
{% endcode %}

{% hint style="info" %}
Note that `$get()` method above is the fetch mode of `get()` method. That means the first one returns `response.data.data` instead of `response.data` object itself. See [fetch mode](api-resource-classes.md#fetch-mode) below.
{% endhint %}

### Fetch mode

Sometimes it is required to fetch only the data itself \(not the response body\). To retrieve data in fetch mode call above methods prefixed with `$`, i.e. `$get`, `$find`, etc.

{% tabs %}
{% tab title="Component.vue" %}
```javascript
const result = await new PostsResource().get()
/* result =
{
  data: [
    { item 1 },
    { item 2 },
  ]
}
*/

const result = await new PostsResource().$get()
/* result =
[
  { item 1 },
  { item 2 },
]
*/
```
{% endtab %}

{% tab title="HTTP Response" %}
```javascript
{
  data: [
    { /* item 1 */ },
    { /* item 2 */ },
  ]
}
```
{% endtab %}
{% endtabs %}

Since every APIs have its own response JSON structure Api resource classes can provide own implementations by overriding the methods listed below that will properly fetch data from response. Here is how data is fetched by default in base `ApiResource` class:

```javascript
class ApiResource {

  fetchGet(result) {
    return result.data
  }

  fetchFind(result) {
    return result.data
  }

  fetchCreate(result) {
    return result
  }

  fetchStore(result) {
    return result.data
  }

  fetchUpdate(result) {
    return result.data
  }

  fetchDestroy(result) {
    return result
  }
}
```

{% hint style="info" %}
The argument `result` is the response JSON body, NOT the response itself.
{% endhint %}

