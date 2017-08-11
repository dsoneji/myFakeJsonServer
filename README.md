# myJsonServer

https://jsonplaceholder.typicode.com/

<h2>Resources</h2>
<p>
Inspired by common use cases.
</p>
<table>
<tbody><tr><td><a href="https://jsonplaceholder.typicode.com/posts">/posts</a></td><td>100 items</td></tr>
<tr><td><a href="https://jsonplaceholder.typicode.com/comments">/comments</a></td><td>500 items</td></tr>
<tr><td><a href="https://jsonplaceholder.typicode.com/albums">/albums</a></td><td>100 items</td></tr>
<tr><td><a href="https://jsonplaceholder.typicode.com/photos">/photos</a></td><td>5000 items</td></tr>
<tr><td><a href="https://jsonplaceholder.typicode.com/todos">/todos</a></td><td>200 items</td></tr>
<tr><td><a href="https://jsonplaceholder.typicode.com/users">/users</a></td><td>10 items</td></tr>
</tbody></table>
<h2>Routes</h2>
<p>
All HTTP verbs are supported.<br>
View usage <a href="https://github.com/typicode/jsonplaceholder#how-to">examples</a>.
</p>
<table>
<tbody><tr><td class="verb">GET</td><td><a href="https://jsonplaceholder.typicode.com/posts">/posts</a></td></tr>
<tr><td class="verb">GET</td><td><a href="https://jsonplaceholder.typicode.com/posts/1">/posts/1</a></td></tr>
<tr><td class="verb">GET</td><td><a href="https://jsonplaceholder.typicode.com/posts/1/comments">/posts/1/comments</a></td></tr>
<tr><td class="verb">GET</td><td><a href="https://jsonplaceholder.typicode.com/comments?postId=1">/comments?postId=1</a></td></tr>
<tr><td class="verb">GET</td><td><a href="https://jsonplaceholder.typicode.com/posts?userId=1">/posts?userId=1</a></td></tr>
<tr><td class="verb">POST</td><td>/posts</td></tr>
<tr><td class="verb">PUT</td><td>/posts/1</td></tr>
<tr><td class="verb">PATCH</td><td>/posts/1</td></tr>
<tr><td class="verb">DELETE</td><td>/posts/1</td></tr>

</tbody></table>
---------------------------------------------------------------------------------------------------------------------------------------

## Table of contents

<details>

<!-- toc -->

- [Sponsorship](#sponsorship)
- [Example](#example)
- [Install](#install)
- [Routes](#routes)
  * [Plural routes](#plural-routes)
  * [Singular routes](#singular-routes)
  * [Filter](#filter)
  * [Paginate](#paginate)
  * [Sort](#sort)
  * [Slice](#slice)
  * [Operators](#operators)
  * [Full-text search](#full-text-search)
  * [Relationships](#relationships)
  * [Database](#database)
  * [Homepage](#homepage)
- [Extras](#extras)
  * [Static file server](#static-file-server)
  * [Alternative port](#alternative-port)
  * [Access from anywhere](#access-from-anywhere)
  * [Remote schema](#remote-schema)
  * [Generate random data](#generate-random-data)
  * [HTTPS](#https)
  * [Add custom routes](#add-custom-routes)
  * [Add middlewares](#add-middlewares)
  * [CLI usage](#cli-usage)
  * [Module](#module)
    + [Simple example](#simple-example)
    + [Custom routes example](#custom-routes-example)
    + [Access control example](#access-control-example)
    + [Custom output example](#custom-output-example)
    + [Rewriter example](#rewriter-example)
    + [Mounting JSON Server on another endpoint example](#mounting-json-server-on-another-endpoint-example)
    + [API](#api)
  * [Deployment](#deployment)
- [Links](#links)
  * [Video](#video)
  * [Articles](#articles)
  * [Third-party tools](#third-party-tools)
- [License](#license)

<!-- tocstop -->

</details>

## Sponsorship

Development of JSON Server and my other projects is generously supported by contributions from people. If you are benefiting from my projects and would like to help keep them financially sustainable, please visit [Patreon](https://patreon.com/typicode).

If you're a company, you can have your logo here.

## Example

Create a `db.json` file

```json
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

Start JSON Server

```bash
$ json-server --watch db.json
```

Now if you go to [http://localhost:3000/posts/1](), you'll get

```json
{ "id": 1, "title": "json-server", "author": "typicode" }
```

Also when doing requests, it's good to know that:

- If you make POST, PUT, PATCH or DELETE requests, changes will be automatically and safely saved to `db.json` using [lowdb](https://github.com/typicode/lowdb).
- Your request body JSON should be object enclosed, just like the GET output. (for example `{"name": "Foobar"}`)
- Id values are not mutable. Any `id` value in the body of your PUT or PATCH request wil be ignored. Only a value set in a POST request wil be respected, but only if not already taken.
- A POST, PUT or PATCH request should include a `Content-Type: application/json` header to use the JSON in the request body. Otherwise it will result in a 200 OK but without changes being made to the data.

## Install

```bash
$ npm install -g json-server
```

## Routes

Based on the previous `db.json` file, here are all the default routes. You can also add [other routes](#add-custom-routes) using `--routes`.

### Plural routes

```
GET    /posts
GET    /posts/1
POST   /posts
PUT    /posts/1
PATCH  /posts/1
DELETE /posts/1
```

### Singular routes

```
GET    /profile
POST   /profile
PUT    /profile
PATCH  /profile
```

### Filter

Use `.` to access deep properties

```
GET /posts?title=json-server&author=typicode
GET /posts?id=1&id=2
GET /comments?author.name=typicode
```

### Paginate

Use `_page` and optionally `_limit` to paginate returned data.

In the `Link` header you'll get `first`, `prev`, `next` and `last` links.


```
GET /posts?_page=7
GET /posts?_page=7&_limit=20
```

_10 items are returned by default_

### Sort

Add `_sort` and `_order` (ascending order by default)

```
GET /posts?_sort=views&_order=asc
GET /posts/1/comments?_sort=votes&_order=asc
```

For multiple fields, use the following format:

```
GET /posts?_sort=user,views&_order=desc,asc
```

### Slice

Add `_start` and `_end` or `_limit` (an `X-Total-Count` header is included in the response)

```
GET /posts?_start=20&_end=30
GET /posts/1/comments?_start=20&_end=30
GET /posts/1/comments?_start=20&_limit=10
```

_Works exactly as [Array.slice](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/slice) (i.e. `_start` is inclusive and `_end` exclusive)_

### Operators

Add `_gte` or `_lte` for getting a range

```
GET /posts?views_gte=10&views_lte=20
```

Add `_ne` to exclude a value

```
GET /posts?id_ne=1
```

Add `_like` to filter (RegExp supported)

```
GET /posts?title_like=server
```

### Full-text search

Add `q`

```
GET /posts?q=internet
```

### Relationships

To include children resources, add `_embed`

```
GET /posts?_embed=comments
GET /posts/1?_embed=comments
```

To include parent resource, add `_expand`

```
GET /comments?_expand=post
GET /comments/1?_expand=post
```

To get or create nested resources (by default one level, [add custom routes](#add-custom-routes) for more)

```
GET  /posts/1/comments
POST /posts/1/comments
```

### Database

```
GET /db
```

### Homepage

Returns default index file or serves `./public` directory

```
GET /
```
