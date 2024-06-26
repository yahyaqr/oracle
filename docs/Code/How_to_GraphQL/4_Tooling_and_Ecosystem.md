As you probably realized already, the GraphQL ecosystem is growing at an amazing speed right now. One of the reasons that this is happening is because GraphQL makes it really easy for us to develop great tools. In this section, we will see why this is the case, and a few amazing tools we already have in the ecosystem.

If you are familiar with GraphQL basics, you probably know how GraphQL’s Type System allows us to quickly define the surface area of our APIs. It allows developers to clearly define the capabilities of an API, but also to validate incoming queries against a schema.

An amazing thing with GraphQL is that these capabilities are not only known to the server. GraphQL allows clients to ask a server for information about its schema. GraphQL calls this **introspection**.

## Introspection

The designers of the schema already know what the schema looks like but how can clients discover what is accessible through a GraphQL API? We can ask GraphQL for this information by querying the `__schema` meta-field, which is always available on the root type of a Query per the spec.

```go
query {
  __schema {
    types {
      name
    }
  }
}
```

Take this schema definition for example:

```go
type Query {
  author(id: ID!): Author
}

type Author {
  posts: [Post!]!
}

type Post {
  title: String!
}
```

If we were to send the introspection query mentioned above, we would get the following result:

```go
{
  "data": {
    "__schema": {
      "types": [
        {
          "name": "Query"
        },
        {
          "name": "Author"
        },
        {
          "name": "Post"
        },
        {
          "name": "ID"
        },
        {
          "name": "String"
        },
        {
          "name": "__Schema"
        },
        {
          "name": "__Type"
        },
        {
          "name": "__TypeKind"
        },
        {
          "name": "__Field"
        },
        {
          "name": "__InputValue"
        },
        {
          "name": "__EnumValue"
        },
        {
          "name": "__Directive"
        },
        {
          "name": "__DirectiveLocation"
        }
      ]
    }
  }
}
```

As you can see, we queried for all types on the schema. We get both the object types we defined and scalar types. We can even introspect the introspection types!

There’s much more than name available on introspection types. Here’s another example:

```go
{
  __type(name: "Author") {
    name
    description
  }
}
```

In this example, we query a single type using the `__type` meta-field and we ask for its name and description. Here’s the result for this query:

```go
{
  "data": {
    "__type": {
      "name": "Author",
      "description": "The author of a post.",
    }
  }
}
```

As you can see, introspection is an extremely powerful feature of GraphQL, and we’ve only scratched the surface. The specification goes into much more detail about what fields and types are available in the introspection schema.

A lot of tools available in the GraphQL ecosystem use the introspection system to provide amazing features. Think of documentation browsers, autocomplete, code generation, everything is possible! One of the most useful tools you will need as you build and use GraphQL APIs uses introspection heavily. It is called **GraphiQL**.

## GraphQL Playground

[GraphQL Playground](https://github.com/graphcool/graphql-playground) is a powerful “GraphQL IDE” for interactively working with a GraphQL API. It features an editor for GraphQL queries, mutations and subscriptions, equipped with autocompletion and validation as well as a documentation explorer to quickly visualize the structure of a schema (powered by introspection). It also can display your query history or lets you work with multiple GraphQL APIs side-by-side. It also seamlessly integrates with [`graphql-config`](https://github.com/graphcool/graphql-config).

It is an incredibly powerful tool for development. It allows you to debug and try queries on a GraphQL server without having to write plain GraphQL queries over `curl`, for example.

## Prisma Pulse

[Prisma Pulse](https://www.prisma.io/data-platform/pulse?utm_source=htg&utm_medium=content&utm_campaign=tooling-and-ecosystem) enables you to easily implement GraphQL subscriptions or live queries with real-time updates from the database in a robust, scalable and type-safe way.

It is compatible with all popular libraries from the GraphQL ecosystem, such as Apollo Server and GraphQL Yoga.

## Prisma Accelerate

[Prisma Accelerate](https://www.prisma.io/data-platform/accelerate?utm_source=htg&utm_medium=content&utm_campaign=tooling-and-ecosystem) is a global database cache with a scalable connection pool that can help speed up your database queries.

It’s especially useful when you’re deploying your GraphQL API in a serverless environment where a lot of traffic may quickly exhaust your database connection limit. Accelerate’s edge caching further ensures that your resolvers can return data faster because they don’t need to fetch the data all the way from the database.