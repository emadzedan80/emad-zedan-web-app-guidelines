## 9. API

<a name="api-design"></a>

### 9.1 API design

_Why:_

> Because we try to enforce development of sanely constructed RESTful interfaces, which team members and clients can consume simply and consistently.

_Why:_

> Lack of consistency and simplicity can massively increase integration and maintenance costs. Which is why `API design` is included in this document.

- We mostly follow resource-oriented design. It has three main factors: resources, collection, and URLs.

  - A resource has data, gets nested, and there are methods that operate against it.
  - A group of resources is called a collection.
  - URL identifies the online location of resource or collection.

  _Why:_

  > This is a very well-known design to developers (your main API consumers). Apart from readability and ease of use, it allows us to write generic libraries and connectors without even knowing what the API is about.

- use kebab-case for URLs.
- use camelCase for parameters in the query string or resource fields.
- use plural kebab-case for resource names in URLs.

- Always use a plural nouns for naming a url pointing to a collection: `/users`.

  _Why:_

  > Basically, it reads better and keeps URLs consistent. [read more...](https://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

- In the source code convert plurals to variables and properties with a List suffix.

  _Why_:

  > Plural is nice in the URL but in the source code, it’s just too subtle and error-prone.

- Always use a singular concept that starts with a collection and ends to an identifier:

  ```
  /students/245743
  /airports/kjfk
  ```

- Avoid URLs like this:

  ```
  GET /blogs/:blogId/posts/:postId/summary
  ```

  _Why:_

  > This is not pointing to a resource but to a property instead. You can pass the property as a parameter to trim your response.

- Keep verbs out of your resource URLs.

  _Why:_

  > Because if you use a verb for each resource operation you soon will have a huge list of URLs and no consistent pattern which makes it difficult for developers to learn. Plus we use verbs for something else.

- Use verbs for non-resources. In this case, your API doesn't return any resources. Instead, you execute an operation and return the result. These **are not** CRUD (create, retrieve, update, and delete) operations:

  ```
  /translate?text=Hallo
  ```

  _Why:_

  > Because for CRUD we use HTTP methods on `resource` or `collection` URLs. The verbs we were talking about are actually `Controllers`. You usually don't develop many of these. [read more...](https://github.com/byrondover/api-guidelines/blob/master/Guidelines.md#controller)

- The request body or response type is JSON then please follow `camelCase` for `JSON` property names to maintain the consistency.

  _Why:_

  > This is a JavaScript project guideline, where the programming language for generating and parsing JSON is assumed to be JavaScript.

- Even though a resource is a singular concept that is similar to an object instance or database record, you should not use your `table_name` for a resource name and `column_name` resource property.

  _Why:_

  > Because your intention is to expose Resources, not your database schema details.

- Again, only use nouns in your URL when naming your resources and don’t try to explain their functionality.

  _Why:_

  > Only use nouns in your resource URLs, avoid endpoints like `/addNewUser` or `/updateUser` . Also avoid sending resource operations as a parameter.

- Explain the CRUD functionalities using HTTP methods:

  _How:_

  > `GET`: To retrieve a representation of a resource.

  > `POST`: To create new resources and sub-resources.

  > `PUT`: To update existing resources.

  > `PATCH`: To update existing resources. It only updates the fields that were supplied, leaving the others alone.

  > `DELETE`: To delete existing resources.

- For nested resources, use the relation between them in the URL. For instance, using `id` to relate an employee to a company.

  _Why:_

  > This is a natural way to make resources explorable.

  _How:_

  > `GET /schools/2/students ` , should get the list of all students from school 2.

  > `GET /schools/2/students/31` , should get the details of student 31, which belongs to school 2.

  > `DELETE /schools/2/students/31` , should delete student 31, which belongs to school 2.

  > `PUT /schools/2/students/31` , should update info of student 31, Use PUT on resource-URL only, not collection.

  > `POST /schools` , should create a new school and return the details of the new school created. Use POST on collection-URLs.

- Use a simple ordinal number for a version with a `v` prefix (v1, v2). Move it all the way to the left in the URL so that it has the highest scope:

  ```
  http://api.domain.com/v1/schools/3/students
  ```

  _Why:_

  > When your APIs are public for other third parties, upgrading the APIs with some breaking change would also lead to breaking the existing products or services using your APIs. Using versions in your URL can prevent that from happening. [read more...](https://apigee.com/about/blog/technology/restful-api-design-tips-versioning)

- Response messages must be self-descriptive. A good error message response might look something like this:

  ```json
  {
    "code": 1234,
    "message": "Something bad happened",
    "description": "More details"
  }
  ```

  or for validation errors:

  ```json
  {
    "code": 2314,
    "message": "Validation Failed",
    "errors": [
      {
        "code": 1233,
        "field": "email",
        "message": "Invalid email"
      },
      {
        "code": 1234,
        "field": "password",
        "message": "No password provided"
      }
    ]
  }
  ```

  _Why:_

  > developers depend on well-designed errors at the critical times when they are troubleshooting and resolving issues after the applications they've built using your APIs are in the hands of their users.

  _Note: Keep security exception messages as generic as possible. For instance, Instead of saying ‘incorrect password’, you can reply back saying ‘invalid username or password’ so that we don’t unknowingly inform user that username was indeed correct and only the password was incorrect._

- Use these status codes to send with your response to describe whether **everything worked**,
  The **client app did something wrong** or The **API did something wrong**.

      _Which ones:_
      > `200 OK` response represents success for `GET`, `PUT` or `POST` requests.

      > `201 Created` for when a new instance is created. Creating a new instance, using `POST` method returns `201` status code.

      > `204 No Content` response represents success but there is no content to be sent in the response. Use it when `DELETE` operation succeeds.

      > `304 Not Modified` response is to minimize information transfer when the recipient already has cached representations.

      > `400 Bad Request` for when the request was not processed, as the server could not understand what the client is asking for.

      > `401 Unauthorized` for when the request lacks valid credentials and it should re-request with the required credentials.

      > `403 Forbidden` means the server understood the request but refuses to authorize it.

      > `404 Not Found` indicates that the requested resource was not found.

      > `500 Internal Server Error` indicates that the request is valid, but the server could not fulfill it due to some unexpected condition.

      _Why:_
      > Most API providers use a small subset HTTP status codes. For example, the Google GData API uses only 10 status codes, Netflix uses 9, and Digg, only 8. Of course, these responses contain a body with additional information. There are over 70 HTTP status codes. However, most developers don't have all 70 memorized. So if you choose status codes that are not very common you will force application developers away from building their apps and over to wikipedia to figure out what you're trying to tell them. [read more...](https://apigee.com/about/blog/technology/restful-api-design-what-about-errors)

- Provide total numbers of resources in your response.
- Accept `limit` and `offset` parameters.

- The amount of data the resource exposes should also be taken into account. The API consumer doesn't always need the full representation of a resource. Use a fields query parameter that takes a comma separated list of fields to include:
  ```
  GET /students?fields=id,name,age,class
  ```
- Pagination, filtering, and sorting don’t need to be supported from start for all resources. Document those resources that offer filtering and sorting.

<a name="api-security"></a>

### 9.2 API security

These are some basic security best practices:

- Don't use basic authentication unless over a secure connection (HTTPS). Authentication tokens must not be transmitted in the URL: `GET /users/123?token=asdf....`

  _Why:_

  > Because Token, or user ID and password are passed over the network as clear text (it is base64 encoded, but base64 is a reversible encoding), the basic authentication scheme is not secure. [read more...](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

- Tokens must be transmitted using the Authorization header on every request: `Authorization: Bearer xxxxxx, Extra yyyyy`.

- Authorization Code should be short-lived.

- Reject any non-TLS requests by not responding to any HTTP request to avoid any insecure data exchange. Respond to HTTP requests by `403 Forbidden`.

- Consider using Rate Limiting.

  _Why:_

  > To protect your APIs from bot threats that call your API thousands of times per hour. You should consider implementing rate limit early on.

- Setting HTTP headers appropriately can help to lock down and secure your web application. [read more...](https://github.com/helmetjs/helmet)

- Your API should convert the received data to their canonical form or reject them. Return 400 Bad Request with details about any errors from bad or missing data.

- All the data exchanged with the REST API must be validated by the API.

- Serialize your JSON.

  _Why:_

  > A key concern with JSON encoders is preventing arbitrary JavaScript remote code execution within the browser... or, if you're using node.js, on the server. It's vital that you use a proper JSON serializer to encode user-supplied data properly to prevent the execution of user-supplied input on the browser.

- Validate the content-type and mostly use `application/*json` (Content-Type header).

  _Why:_

  > For instance, accepting the `application/x-www-form-urlencoded` mime type allows the attacker to create a form and trigger a simple POST request. The server should never assume the Content-Type. A lack of Content-Type header or an unexpected Content-Type header should result in the server rejecting the content with a `4XX` response.

- Check the API Security Checklist Project. [read more...](https://github.com/shieldfy/API-Security-Checklist)

<a name="api-documentation"></a>

### 9.3 API documentation

- Fill the `API Reference` section in [README.md template](./README.sample.md) for API.
- Describe API authentication methods with a code sample.
- Explaining The URL Structure (path only, no root URL) including The request type (Method).

For each endpoint explain:

- URL Params If URL Params exist, specify them in accordance with name mentioned in URL section:

  ```
  Required: id=[integer]
  Optional: photo_id=[alphanumeric]
  ```

- If the request type is POST, provide working examples. URL Params rules apply here too. Separate the section into Optional and Required.

- Success Response, What should be the status code and is there any return data? This is useful when people need to know what their callbacks should expect:

  ```
  Code: 200
  Content: { id : 12 }
  ```

- Error Response, Most endpoints have many ways to fail. From unauthorized access to wrongful parameters etc. All of those should be listed here. It might seem repetitive, but it helps prevent assumptions from being made. For example

  ```json
  {
    "code": 401,
    "message": "Authentication failed",
    "description": "Invalid username or password"
  }
  ```

- Use API design tools, There are lots of open source tools for good documentation such as [API Blueprint](https://apiblueprint.org/) and [Swagger](https://swagger.io/).