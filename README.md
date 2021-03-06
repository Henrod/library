Library
=============

Simple library implementation to put in practice the [Google API design guide](https://cloud.google.com/apis/design).

# Examples

Start the API:

`make run/api`

## Create Book

### Success

#### Request

```sh
curl localhost:8081/v1/shelves/shelf1/books -d'{
  "name": "book1",
  "author": "Henrod",
}'
```

or

```sh
grpcurl -d '{
        "parent": "shelves/shelf1", 
        "book": {
            "name": "shelves/shelf1/books/book1", 
            "author": "Henrod"
        }
    }' \
    -plaintext localhost:8080 api.v1.LibraryService/CreateBook
```

#### Response
```json
{
  "name": "shelves/shelf1/books/book1",
  "author": "Henrod",
  "createTime": "2022-01-21T00:02:34.045823Z",
  "updateTime": "2022-01-21T00:02:34.045823Z"
}
```

### Already exists

#### Response

```json
{
  "code": 6,
  "message": "resource already exists",
  "details": [
    {
      "@type": "type.googleapis.com/google.rpc.ResourceInfo",
      "resourceType": "book",
      "resourceName": "book1",
      "owner": "shelves/shelf1",
      "description": "the book already exists in shelf"
    }
  ]
}
```

## Get Book

### Success

#### Request

```sh
curl localhost:8081/v1/shelves/shelf1/books/book1
```

or

```sh
grpcurl -d '{"name": "shelves/shelf1/books/book1"}' \
    api.v1.LibraryService/ListBooks
```

#### Response
```json
{
  "books": [
    {
      "name": "shelves/shelf1/books/book1",
      "author": "Henrod",
      "createTime": "2022-01-20T11:01:42.327988Z",
      "updateTime": "2022-01-20T11:01:42.327988Z"
    }
  ],
  "nextPageToken": ""
}
```

### Not found

```json
{
  "code": 5,
  "message": "resource not found",
  "details": [
    {
      "@type": "type.googleapis.com/google.rpc.ResourceInfo",
      "resourceType": "book",
      "resourceName": "shelves/shelf1/books/book2",
      "owner": "shelf1",
      "description": "the book does not exist in shelf"
    }
  ]
}
```

## List Books

### Success

#### Request

```sh
curl localhost:8081/v1/shelves/shelf1/books
```

or 

```sh
grpcurl -d '{"parent": "shelves/shelf1"}' \
    -plaintext localhost:8080 api.v1.LibraryService/ListBooks
```

#### Response

```json
{
  "books": [
    {
      "name": "shelves/shelf1/books/book1",
      "author": "Henrod",
      "createTime": "2022-01-20T11:01:42.327988Z",
      "updateTime": "2022-01-22T21:57:56.011468Z"
    },
    {
      "name": "shelves/shelf1/books/book2",
      "author": "Henrod",
      "createTime": "2022-01-21T00:02:34.045823Z",
      "updateTime": "2022-01-22T21:59:41.508003Z"
    }
  ],
  "nextPageToken": ""
}
```

### Success - Page 1

#### Request

```sh
curl localhost:8081/v1/shelves/shelf1/books\?page_size=1
```

or

```sh
grpcurl -d '{"parent": "shelves/shelf1", "page_size": 1}' \
    -plaintext localhost:8080 api.v1.LibraryService/ListBooks
```

#### Response
```json
{
  "books": [
    {
      "name": "shelves/shelf1/books/book1",
      "author": "Henrod",
      "createTime": "2022-01-20T11:01:42.327988Z",
      "updateTime": "2022-01-20T11:01:42.327988Z"
    }
  ],
  "nextPageToken": "MQ=="
}
```

### Success - Page 2

#### Request

```sh
curl localhost:8081/v1/shelves/shelf1/books\?page_size=1\&page_token=MQ==
```

or

```sh
grpcurl -d '{
        "parent": "shelves/shelf1",
        "page_size": 1,
        "page_token": "MQ=="
    }' \
    -plaintext localhost:8080 api.v1.LibraryService/ListBooks
```

#### Response
```json
{
  "books": [
    {
      "name": "shelves/shelf1/books/book2",
      "author": "Henrod",
      "createTime": "2022-01-21T00:02:29.938371Z",
      "updateTime": "2022-01-21T00:02:29.938371Z"
    }
  ],
  "nextPageToken": ""
}
```

### List all books

#### Request

```sh
curl localhost:8081/v1/shelves/-/books
```

or

```sh
grpcurl -d '{"parent": "shelves/-"}' \
    -plaintext localhost:8080 api.v1.LibraryService/ListBooks
```

#### Response

```json
{
  "books": [
    {
      "name": "shelves/shelf1/books/book1",
      "author": "Henrod",
      "createTime": "2022-01-20T11:01:42.327988Z",
      "updateTime": "2022-01-22T21:57:56.011468Z"
    },
    {
      "name": "shelves/shelf2/books/book1",
      "author": "Henrod",
      "createTime": "2022-01-21T00:02:34.045823Z",
      "updateTime": "2022-01-22T21:59:41.508003Z"
    }
  ],
  "nextPageToken": ""
}
```

## Update Book

### Success

#### Request

```sh
curl localhost:8081/v1/shelves/shelf1/books/book1 -XPATCH -d'{
        "author": "Henrique Rodrigues"
}'
```

or

```sh
grpcurl -d '{
        "book": {
                "name": "shelves/shelf1/books/book1",
                "author": "Henrique Rodrigues"
        },
        "update_mask": {
                "paths": ["author"]
        }
    }' \
    -plaintext localhost:8080 api.v1.LibraryService/UpdateBook
```

#### Response
```json
{
  "name": "shelves/shelf1/books/book1",
  "author": "Henrique Rodrigues",
  "createTime": "2022-01-20T11:01:42.327988Z",
  "updateTime": "2022-01-22T21:57:56.011468Z"
}
```

### Not found

#### Response

```json
{
  "code": 5,
  "message": "resource not found",
  "details": [
    {
      "@type": "type.googleapis.com/google.rpc.ResourceInfo",
      "resourceType": "book",
      "resourceName": "shelves/shelf1/books/book3",
      "owner": "shelves/shelf1",
      "description": "book not found in shelf"
    }
  ]
}
```

### Invalid field_mask

#### Request

```sh
curl localhost:8081/v1/shelves/shelf1/books/book1 -XPATCH -d'{
        "invalid_field": "anything"
}'
```

or

```sh
grpcurl -d '{
        "book": {
                "name": "shelves/shelf1/books/book1",
                "invalid_field": "anything"
        },
        "update_mask": {
                "paths": ["invalid_field"]
        }
    }' \
    -plaintext localhost:8080 api.v1.LibraryService/UpdateBook
```

#### Response

HTTP
```json
{
  "code": 3,
  "message": "could not find field \"invalid_field\" in \"api.v1.Book\"",
  "details": []
}
```

gRPC
```text
Error invoking method "api.v1.LibraryService/UpdateBook": error getting request data: message type api.v1.Book has no known field named invalid_field
```

## Delete Book

### Success

#### Request

```sh
curl localhost:8081/v1/shelves/shelf1/books/book1 -XDELETE
```

or

```sh
grpcurl -d '{ "name": "shelves/shelf1/books/book1" }' \
    -plaintext localhost:8080 api.v1.LibraryService/DeleteBook
```

#### Response
```json
{}
```

### Not found

#### Response

```json
{
  "code": 5,
  "message": "resource not found",
  "details": [
    {
      "@type": "type.googleapis.com/google.rpc.ResourceInfo",
      "resourceType": "book",
      "resourceName": "shelves/shelf1/books/book2",
      "owner": "shelves/shelf1",
      "description": "book not found in shelf"
    }
  ]
}
```

## Create Shelf

Asynchronous operation. Returns a long-running operation resource, which is used by the client to poll the status.

### Success

#### Request

```sh
curl localhost:8081/v1/shelves -d'{"name": "shelf1"}'
```

or

```sh
grpcurl -d '{ "name": "shelf1" }' \
    -plaintext localhost:8080 api.v1.LibraryService/CreateShelf
```

#### Response
```json
{
    "done": false,
    "metadata": {
        "@type": "type.googleapis.com/api.v1.Operation",
        "name": "CreateShelf",
        "percentage": 0,
        "stage": "PLANTING_TREE"
    },
    "name": "operations/shelves/shelf1"
}
```

### Already Exists

#### Response

```json
{
  "code": 6,
  "message": "resource already exists",
  "details": [
    {
      "@type": "type.googleapis.com/google.rpc.ResourceInfo",
      "resourceType": "operation",
      "resourceName": "operations/shelves/shelf3",
      "owner": "library",
      "description": "the create shelf operation already exists"
    }
  ]
}
```


# Get Operation

Get the status of a long-running operation. Currently, only supports:
- CreateShelf

### Success

#### Request

```sh
curl localhost:8081/v1/operations/shelves/shelf1
```

or

```sh
grpcurl -d '{ "name": "operations/shelves/shelf1" }' \
    -plaintext localhost:8080 api.v1.LibraryService/GetOperation
```

#### Response
```json
{
    "done": false,
    "metadata": {
        "@type": "type.googleapis.com/api.v1.Operation",
        "name": "CreateShelf",
        "percentage": 0,
        "stage": "PLANTING_TREE"
    },
    "name": "operations/shelves/shelf1"
}
```

### Finished successfully

#### Response
```json
{
  "name": "operations/shelves/shelf1",
  "metadata": {
    "@type": "type.googleapis.com/api.v1.Operation",
    "name": "CreateShelf",
    "stage": "FINISHED_SHELF",
    "percentage": 100
  },
  "done": true,
  "response": {
    "@type": "type.googleapis.com/api.v1.Shelf",
    "name": "shelves/shelf1",
    "createTime": "2022-02-13T21:25:09.034864Z",
    "updateTime": "2022-02-13T21:25:09.034864Z"
  }
}
```

### Finished with failure

#### Response
```json
{
  "name": "operations/shelves/shelf1",
  "metadata": {
    "@type": "type.googleapis.com/api.v1.Operation",
    "name": "CreateShelf",
    "stage": "FINISHED_SHELF",
    "percentage": 100
  },
  "done": true,
  "error": {
    "code": 6,
    "message": "resource already exists",
    "details": [
      {
        "@type": "type.googleapis.com/google.rpc.ResourceInfo",
        "resourceType": "shelf",
        "resourceName": "shelves/shelf1",
        "owner": "library",
        "description": "the shelf already exists in the library"
      }
    ]
  }
}
```

### Not Found

#### Response

```json
{
  "code": 5,
  "message": "resource not found",
  "details": [
    {
      "@type": "type.googleapis.com/google.rpc.ResourceInfo",
      "resourceType": "operation",
      "resourceName": "operations/shelves/shelf1",
      "owner": "library",
      "description": "the operation doesn't exist; is not running nor completed"
    }
  ]
}
```