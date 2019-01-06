# GraphQL Rate Limit

`graphql-rate-limit` is a directive allows you to add basic but granular rate limiting to your GraphQL API.

- 💂‍♀️ Add rate limits to queries or mutations 
- 🔑 Add filters to rate limits based on the query or mutation args
- ❌ Custom error messaging
- ⏰ Configure using a simple `max` per `window` arguments
- 💼 Custom stores, use Redis, Postgres, Mongo... it defaults to in-memory
- 💪 Written in TypeScript


## Install

```sh
yarn add graphql-rate-limit
```

### Example

```graphql
directive @rateLimit(
    max: Int, 
    window: Int,
    message: String, 
    identityArgs: [String], 
) on FIELD_DEFINITION

type Query {
  # Rate limit to 5 per second
  getItems: [Item] @rateLimit(window: 1000, max: 5)

  # Rate limit access per item ID
  getItem(id: ID!): Item @rateLimit(identityArgs: ["id"])
}

type Mutation {
  # Rate limit with a custom error message
  createItem(title: String!): Item @rateLimit(message: "You are doing that too often.")
}
```

### Usage

##### Step 1. 

Create a configured GraphQLRateLimit class.

```js
const { createRateLimitDirective } = require('graphql-rate-limit');
// OR
import { createRateLimitDirective } from 'graphql-rate-limit';

const GraphQLRateLimit = createRateLimitDirective({
    /**
     * `identifyContext` is required and used to identify the user/client. The most likely cases
     * are either using the context's request.ip, or the user ID on the context.
     * A function that accepts the context and returns a string that is used to identify the user.
     */
    identifyContext: (ctx) => ctx.user.id, // Or could be something like: return ctx.req.ip;
    /**
     * `store` is optional as it defaults to an InMemoryStore. See the implementation of InMemoryStore if 
     * you'd like to implement your own with your own database.
     */
    store: new MyCustomStore(),
});
```

