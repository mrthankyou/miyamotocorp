---
layout: post-light-feature
title: How to Retrieve a GraphQL Schema
categories: articles
date: 2019-10-22
---

### Pre-requisites
Understand GraphQL types, queries, and mutations. This is a decent primer if you’re just getting started with GraphQL.

### Intro
In this article, we will discuss what is a GraphQL schema and different ways to retrieve the GraphQL schema from a GraphQL server for the purposes of pentesting.

### Definition of a GraphQL Schema
A GraphQL schema is a document describing how a specific GraphQL server is structured. A GraphQL schema is like a SQL database design. Every GraphQL server has a schema.

Below is an example of a GraphQL schema...
type Query {
   users: [User]
}

type User {
   id: ID!
   email: String
}
In the schema above there is a GraphQL query called users which returns an array of users and a User type with an id and email field.
GraphQL schemas can be more complex than the example above. For now just remember that every GraphQL server has a schema.
### The Importance of the GraphQL Schema
The GraphQL schema is important for end users because it can provide the following info:
GraphQL queries we can make to the GraphQL server.
GraphQL mutations we can make to the GraphQL server.
A list of GraphQL types.
What GraphQL types are available to query.
What GraphQL types are available to mutate (aka create, update, or delete).
Fields within a GraphQL type. For example, a User type will have a name, email, and bio field.
Relationships between GraphQL types. For example, a User can have many Friends. A Post can belong to a User.
Business logic on how the application potentially works. For example, if the Post type has a field called “hidden,” we can implicitly assume that Posts can be hidden from other users. Following this rabbit hole will allow us to understand how features work in the application.
We know that a GraphQL schema is important because it provides us structure of the GraphQL server. The next question we must ask then is how do we retrieve this schema from the GraphQL server?
Below are two such ways: the GraphQL Explorer and Introspection Queries.

### How To Get The Schema — GraphQL Explorer
Some GraphQL servers provide a GraphQL Explorer (typically called GraphiQL). The GraphQL Explorer is a page where you can manually input GraphQL queries into the page (see the left pane in example below) and see how the GraphQL server responds (see the right pane in the example below).

Github’s GraphQL Explorer Page — An example of a GraphQL Explorer
You can typically find the link to the application’s GraphQL Explorer in the API documentation. If the API documentation does not make it obvious where the Explorer page is, try this Google dork strategy: site:URL_OF_API_PAGE.com explorer.
Some examples of GraphQL Explorers are below:
Countries GraphQL Explorer
Artsy GraphQL Explorer
Github GraphQL Explorer (requires Github account)

### How To Get The Schema — Introspection Queries
Some GraphQL servers don’t provide a convenient GraphQL API explorer. Instead, to get the schema we need to send a HTTP request to the GraphQL server endpoint asking for the GraphQL schema. This type of HTTP request is called a GraphQL introspection query.
The GraphQL introspection query’s body is made up of GraphQL syntax-text that represents what we are asking the GraphQL server to return.
For example, the following GraphQL syntax-text is asking the GraphQL server to return a list of all queries available to us from the schema:
{
  __schema {
    queryType {
      fields {
        name
      }
    }
  }
}
Below are two more GraphQL syntax-text requests you can make to a GraphQL server:
1. Returns all mutations available to us from the schema:
{
  __schema {
    mutationType {
      fields {
        name
      }
    }
  }
}
2. Returns the entire schema: queries, mutations, fields, etc.:
fragment FullType on __Type {
  kind
  name
  fields(includeDeprecated: true) {
    name
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
    isDeprecated
    deprecationReason
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues(includeDeprecated: true) {
    name
    isDeprecated
    deprecationReason
  }
  possibleTypes {
    ...TypeRef
  }
}
fragment InputValue on __InputValue {
  name
  type {
    ...TypeRef
  }
  defaultValue
}
fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}
query IntrospectionQuery {
  __schema {
    queryType {
      name
    }
    mutationType {
      name
    }
    types {
      ...FullType
    }
    directives {
      name
      locations
      args {
        ...InputValue
      }
    }
  }
}
Below is an example of a CURL request where we ask the GraphQL server to return us every query from the GraphQL schema:
curl 'https://countries.trevorblades.com/'                                              -H 'Content-Type: application/json'                                                -H 'Accept: application/json'                                                                                             --compressed                                                                                   --data-binary '{"query":"{\n\t__schema{\n queryType {\n fields{\n name\n }\n }\n }\n}"}'
By running the above CURL request, we get the following results:
{
   "data":{
      "__schema":{
         "queryType":{
            "fields":[
               {
                  "name":"continents"
               },
               {
                  "name":"continent"
               },
               {
                  "name":"countries"
               },
               {
                  "name":"country"
               },
               {
                  "name":"languages"
               },
               {
                  "name":"language"
               }
            ]
         }
      }
   }
}
Note: In the above GraphQL query syntax-text examples, I’m making an assumption that the GraphQL schemas have defined a name field. If the schema didn’t define a name field and we sent the following GraphQL query syntax-text:
{
  __schema {
    queryType {
      name
    }
  }
}
We will receive the following error:
{
  "errors": [
    {
      "message": "Field 'name' doesn't exist on type '__Type'",
      "locations": [
        {
          "line": 4,
          "column": 7
        }
      ],
      "fields": [
        "query",
        "__schema",
        "queryType",
        "name"
      ]
    }
  ]
}
To fix this query, we have to modify the query and replace the “name” field with a different field. What that different field is depends on what the application’s GraphQL documentation tells us is available to query. Typically though, most GraphQL servers should have at minimum a “name” field.

### Conclusion
A GraphQL schema is a document describing how a specific GraphQL server is structured. A GraphQL schema is important because it helps end-users understand how a GraphQL server works.
There are two ways to get the GraphQL schema: accessing an application’s GraphQL Explorer and Introspection Queries. Once we have the schema, we can begin work on further investigating the GraphQL server with the ultimate goal of finding vulnerabilities.

### Misc. Notes
Current State of Hiding the GraphQL Schema
The GraphQL schema by default exposes valuable information such as connections between types, accessible fields, etc. This put’s a smile on a hacker’s face. Therefore, hiding the GraphQL schema(whether that’s part of the schema or the entire schema) from certain users can be an advantage.
Unfortunately, as of Oct. 2019, not all GraphQL libraries support this feature. Below is a short list of some GraphQL libraries that do and don’t support schema hiding.
GraphQL Ruby (see Limiting Visibility)
GraphQL Java (see GraphqlFieldVisibility)
GraphQL JS (currently doesn’t support it but check the following ticket for more info)
GraphQL Go (unknown — check the following ticket for more info)

### Glossary (alphabetical order)
Google Dork Strategy: Google dorking is where a user can run a Google search utilizing Google’s advanced search options. Many pentesters and hackers use this to find hidden pages, caches, etc. for a particular application. More info can be found here.
GraphQL mutation: A type of request sent to the GraphQL server used to create, update, or delete records.
GraphQL query: A type of request sent to the GraphQL server used to retrieve records.
GraphQL schema: A document describing how a GraphQL server is structured.
GraphQL types: This is an object that we interact with in the GraphQL schema. An object can be a User, a Message, a Post, etc.