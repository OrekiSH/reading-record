## Quick Start

```js
{
  "1": {
    "id": "1",
    "name": "Dan"
  },
  "2": {
    "id": "2",
    "name": "Marie"
  }
}
```

```js
var graphql = require('graphql');
var graphqlHTTP = require('express-graphql');
var express = require('express');

var data = require('./data.json');

var userType = new graphql.GraphQLObjectType({
  name: 'User',
  fields: {
    id: { type: graphql.GraphQLString },
    name: { type: graphql.GraphQLString },
  }
});

var schema = new graphql.GraphQLSchema({
  query: new graphql.GraphQLObjectType({
    name: 'Query',
    fields: {
      user: {
        type: userType,
        args: {
          id: { type: graphql.GraphQLString }
        },
        resolve (_, args) {
          return data[args.id];
        },
      },
    },
  }),
});

express()
  .use('/graphql', graphqlHTTP({ schema: schema, pretty: true }))
  .listen(3000);
```

```shell
curl http://localhost:3000/graphql?query={user(id:"1"){name}}
curl -XPOST -d '{__schema { queryType{ name, fields { name } }}}' http://localhost:3000/graphql
```

## Introspection

```js
type User {
  id: String
  name: String
  birthday: Date
}
```
```js
{
  __type(name: "User") {
    name
    fields {
      name
      type {
        name
      }
    }
  }
}
```
```js
{
  "__type": {
    "name": "User",
    "fields": [
      {
        "name": "id",
        "type": { "name": "String" }
      },
      {
        "name": "name",
        "type": { "name": "String" }
      },
      {
        "name": "birthday",
        "type": { "name": "Date" }
      },
    ]
  }
}
```
