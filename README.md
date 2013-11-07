# API Design Standards

## 1 - URI




### 1.1 - Nouns are good; Verbs are bad

The naming of resources should always be nouns. Collections should be plural. The first path segment should always be a collection.

##### Examples:

| Path | Description |
|---|---|
| /users | List of users |
| /users/1234/friends | List of friends for user |
| /users/1234/documents | List of documents for user |

##### Bad Examples:

| Path | Explanation |
|---|---|
| /users/create | Do not use create (a verb) in the path. |
| /users/1234/delete | Do not use delete (a verb) in the path. |
| /user/1234 | Collections should be plural. |
| /user/1234/getmessages | Do not use get (a verb) in the path. |




### 1.2 - Use HTTP verbs to operate on data

Tell the server what you are trying to do to the resource through standard HTTP verbs - POST, GET, PUT and DELETE.

##### Examples:

| Path | GET | POST | PUT | DELETE |
|---|---|---|---|---|
| /users | List users | Create a new user | Error | Error |
| /users/1234 | Retrieve the details of the user | Error | Update attributes of the user. Error if it does not exist. | Delete the specified user |
| /users/1234/messages | List messages owned by user | Create a new message owned by user | Error | Error |



### 1.3 - Resource assosciations


##### Examples:

| Path | Description |
|---|---|
| /users/1234/friends | List of friends for the user |
| /users/1234/messages | List of messages owned by the user  |
| /messages/5678/users  | List of users involved in the message |
| /users/1234/messages/5678  | Return the message details |




### 1.4 - Querystring

The data to be retrieved, updated, or deleted will be passed in the path. The data to be created in the request body. Any other parameters will be passed in as a querystring. Querystring parameters must be camelcase.




### 1.5 - Standard querystring parameters

| Parameter | Description | Value | Default |
|---|---|---|---|
| format | Preferred payload format | json, xml, csv, excel, ... | json |
| start | Start offset for results | 0-* | 0 |
| limit | Limit on number of results | 1-* | 100 |
| view | Level of payload detail | all, summary, ... | all |


## 2 - Payload 




### 2.1 - Required fields

| Field | Description | Value |
|---|---|---|
| total | Total possible records | 0-* |
| start | Start offset from querystring | 0-* |
| limit | Limit from querystring | 1-* |
| items | Data elements | Array of objects |

##### Example:

```javascript
{
  "total": 20,
  "start": 0,
  "limit": 2,
  "items": [
    {
      "id": "BB",
      "name": "BSII"
    },
    {
      "id": "BI",
      "name": "BINFRA"
    }
  ]
}
```




### 2.2 - Error response

Respect the format querystring in your error response.

| Field | Description |
|---|---|
| code | HTTP error code |
| error | Error message |


##### Example:

```javascript
{
  "code": 403,
  "error": "You can't edit this!"
}
```

##### Bad example:

```
Exception Details: System.Web.Hosting.HostingEnvironmentException: Failed to access IIS metabase.
```

### 2.3 - Field names

Use lower camel case on field names. Do not use _ or - or all uppercase.

### 2.4 - Data types

Use valid JSON types. Do not represent everything as a string.

```"Y"``` and ```"N"``` are not valid. Use ```true``` or ```false```.

```"Yes"``` and ```"No"``` are not valid. Use ```true``` or ```false```.

```"230"``` and ```"250"``` are not valid. Use ```230``` and ```250```.

```[{"Key":"myKey","Value":"myValue"}]``` is not valid. Use ```{"myKey":"myValue"}```

Use ISO strings for dates.


##### Example:

```javascript
{
  "total": 20,
  "start": 0,
  "limit": 2,
  "items": [
    {
      "id": "BB",
      "name": "BSII",
      "camelCaseValue": 230,
      "someCoolDate": "2012-12-12T23:30:11.678Z"
    },
    {
      "id": "BI",
      "name": "BINFRA",
      "camelCaseValue": 105,
      "someCoolDate": "2012-12-12T23:30:11.678Z"
    }
  ]
}
```

##### Bad Example:

```javascript
{
  "total": 20,
  "start": 0,
  "limit": 2,
  "Items": [
    {
      "ID": "BB",
      "Name": "BSII",
      "Bad_Value": "230",
      "ALLUPPERCASE": "/Date(1355029200000-0500)/"
    },
    {
      "ID": "BI",
      "Name": "BINFRA",
      "Bad_Value": "105",
      "ALLUPPERCASE": "/Date(1355029200000-0500)/"
    },
  ]
}
```

### 2.5 - Status codes

Always return a valid HTTP response code. View a list [here.](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes) 
