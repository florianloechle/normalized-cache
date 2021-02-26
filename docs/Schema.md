# Schema

A schema can be provided to the cache so it knows about the shape of the data.

It can be used to define the entities and their relationships, but also any data types.

## Structure

Types can be defined with factory functions on the `schema` object:

```js
import { schema } from "normalized-cache";

const Post = schema.object({
  name: "Post",
});
```

## Registration

Types can be registered when initializing the cache:

```js
const cache = new Cache({
  types: [Post],
});
```

All types referenced by `Post` will be automatically added.

## Entities

An entity is a type which has a `name` and an `id`. Data matching these types will be stored normalized.

```js
const Post = schema.object({
  name: "Post",
});

cache.write({ type: "Post", data: { id: "1" } });
```

By default the cache will look for an `id` property in the data but a custom `id` function can also be defined on the type.

```js
const Post = schema.object({
  name: "Post",
  id: (post) => post.uid,
});

cache.write({ type: "Post", data: { uid: "1" } });
```

## Relationships

Relationships are defined by refering to another type:

```js
const Author = schema.object({
  name: "Author",
});

const Comment = schema.object({
  name: "Comment",
});

const Post = schema.object({
  name: "Post",
  fields: {
    author: Author,
    comments: [Comment],
  },
});
```

## Types

### Strings

Defining a string:

```js
schema.string();
```

Defining a string constant:

```js
schema.string("DRAFT");
```

### Numbers

Defining a number:

```js
schema.number();
```

Defining a number constant:

```js
schema.number(0);
```

### Booleans

Defining a boolean:

```js
schema.boolean();
```

Defining a boolean constant:

```js
schema.boolean(true);
```

### Objects

```js
const Post = schema.object({
  fields: {
    title: schema.nonNullable(schema.string()),
    createdAt: schema.number(),
  },
});
```

### Arrays

Defining an array:

```js
const Posts = schema.array(Post);
```

### Unions

Defining a union:

```js
const PostOrComment = schema.union({
  types: [Post, Comment],
  resolveType: (value) => (value?.title ? Post : Comment),
});
```

The `isOfType` property can also be used:

```js
const Post = schema.object({
  name: "Post",
  isOfType: (value) => value?.title,
});

const Comment = schema.object({
  name: "Comment",
  isOfType: (value) => value?.text,
});

const SearchResults = schema.array({
  name: "SearchResults",
  ofType: schema.union([Post, Comment]),
});
```

### Null

Defining null values:

```js
schema.null();
```

### Non-nullable values

Defining non-nullable values:

```js
schema.nonNullable(schema.string());
```