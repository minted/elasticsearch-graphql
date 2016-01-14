# Elastic Search GraphQL
Schema and query builder for creating GraphQL server from existing Elastic Search index

[![js-standard-style](https://cdn.rawgit.com/feross/standard/master/badge.svg)](https://github.com/feross/standard)

## Usage
```javascript
var graphql = require('graphql');
var hitsSchema = require('./schemas/myGraphQLSchema')

esGraphQL({
  graphql: graphql,
  name: 'ordersSearch',
  elastic: {
    host: 'localhost:9200',
    index: 'orders',
    type: 'order'
  },
  hitsSchema: hitsSchema
},function(err, schema) {

  var rootSchema = new graphql.GraphQLSchema({
    query: new graphql.GraphQLObjectType({
      name: 'RootQueryType',
      fields: {
        mySearchData: {
          type: schema.type,
          args: schema.args,
          resolve: schema.resolve
        }
      }
    })
  })
})
```



### Query Builder
It will fetch the current mapping from elasticsearch and create a static typed schema for you. Add the schema to you graphql server and the type helper will lead you. The hits field will resolve to whatever schema you send in. So you can use elasticsearch for searching data and then easily get your real data from anywhere. See full example in in /examples

###### Example query
```graphql
{
  ordersSearch(query: "New Order",
    filters: {
      images: {
        createdBy: {
          values: ["Simon Nord", "James Kyburz"],
          operator: OR
        }
      }
    }
  ) {
    aggregations {
      status {
        timestamp(limit: 5) {
          unhandledDocs
          buckets {
            key,
            count
          }
        }
      }
    }
    hits {
      id
    }
  }
}
```

### TODO
- [ ] Add tests
- [ ] Allow more aggregation types
- [ ] Allow more complex filters
