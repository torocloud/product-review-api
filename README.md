# Product Review API
A simple product review api with CRUD operations

### Prerequisites

  - Apache Maven 3+
  - [Martini Desktop](https://www.torocloud.com/martini/download)

### Building the Martini Package

```
$ mvn clean package
```
This will create a ZIP file named `product-review-api-bin.zip` containing all the files (services, configurations, etc.) needed under the `target` folder. This ZIP file is what we call a [Martini Package](https://docs.torocloud.com/martini/latest/developing/package/) which then you can import in Martini Desktop to get started. You can learn more how to import a Martini Package by visiting our [documentation](https://docs.torocloud.com/martini/latest/developing/package/importing/)

### Getting the Martini Package from TORO Marketplace

You can also get this package via TORO Marketplace. See our documentation on [How to import a Martini Package from Marketplace](https://docs.torocloud.com/martini/latest/developing/package/importing/#from-the-marketplace).

### Usage
This package exposes operations for a simple product review REST API that allows you to do CRUD operations. You can find the [Gloop REST API](https://docs.torocloud.com/martini/latest/developing/gloop/api/rest/) file at `/api/ProductReview` under the `code` folder after importing the package to your Martini Desktop application.

### Setup
This Martini Package automatically sets up the required resources for you. During the package startup, Martini will execute the configured _Startup Service_ that initializes the database to be used for storing the record that will be creating for using this notes api.

This package uses HSQL as the default datastore but you can change this to MySQL, Postgres and etc by updating the [package properties](https://docs.torocloud.com/martini/latest/developing/package/properties/) located at [conf](https://docs.torocloud.com/martini/latest/developing/package/directory/) folder. Below is what the contents of the properties file look like

```
# The database connection name to be used
database.name=product_review

# The database type to be used
database.type=hsql

# HSQL database configuration to be used
hsql.driver=org.hsqldb.jdbc.JDBCDriver
hsql.connection.url=jdbc:hsqldb:file:${toroesb.home}data/hsql/product_review.db;hsqldb.tx=MVCC;sql.syntax_mys=true
hsql.username=sa
hsql.password=

...

#product review rate limit property
rate.limit=5
```
* `database.type` sets the database provider the Martini package will use. If you will use the default hsql config, you don't need to change anything in the hsql configuration. **Note**: If you will use a different hsql database, make sure that you add `sql.syntax_mys=true` in the connection properties. This ensures that the SQL query from the SQL Services in this package will be compatible with hsql.
* `rate.limit` set the max value of the product review rating. This will also be used to properly get the average of all reviews.
### Operations

The base url is `<host>/api/ProductReview` where `host` is the location where the Martini is deployed. By default, it's `localhost:8080`.

#### Product Review

`GET /product/review`

Returns a list of all product review

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/ProductReview/product/review \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully get all product review",
    "warnings": [],
    "productReview": [
        {
            "id": "24b0f169-8854-4e84-b009-7665f40bc5b5",
            "product_guid": "715a9d18-c84b-4cc9-b077-bbfa41c68040",
            "rating": 4,
            "review": "A good product",
            "reviewer": "Mark Doe",
            "review_date": "2020-03-26T12:23:27+0800"
        },
        {
            "id": "63a8ba4a-7d5a-41f6-a8b7-7a262a410ed7",
            "product_guid": "02f89510-3ace-4a87-84e8-6f46b6644bd8",
            "rating": 5,
            "review": "Very nice product",
            "reviewer": "John Doe",
            "review_date": "2020-03-26T12:10:40+0800"
        }
    ]
}
```

`POST /product/review`

Creates a new product review. **Note**: The `product_guid` will be the reference to the product of the users `Product` table in the database.

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/ProductReview/product/review \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "product_guid": "715a9d18-c84b-4cc9-b077-bbfa41c68040",
    "rating": "4",
    "review": "A good product",
    "reviewer": "Mark Doe"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully create product review",
    "warnings": [],
    "productReview": {
        "id": "24b0f169-8854-4e84-b009-7665f40bc5b5",
        "product_guid": "715a9d18-c84b-4cc9-b077-bbfa41c68040",
        "rating": 4,
        "review": "A good product",
        "reviewer": "Mark Doe",
        "review_date": "2020-03-26T12:23:27+0800"
    }
}
```

`PUT /product/review`

Updates a product review

**Sample Request**

**curl**
```
curl -X PUT \
  http://localhost:8080/api/ProductReview/product/review \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "id": "24b0f169-8854-4e84-b009-7665f40bc5b5",
    "review": "This is a great product"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully updated product review",
    "warnings": []
}
```

`GET /product/review/<productReviewId>`

Returns a single product review that matches the given `productReviewId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/ProductReview/product/review/24b0f169-8854-4e84-b009-7665f40bc5b5
```

**Sample Response**

If the request is sucessful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully get Product Review with ID 24b0f169-8854-4e84-b009-7665f40bc5b5",
    "warnings": [],
    "productReview": {
        "id": "24b0f169-8854-4e84-b009-7665f40bc5b5",
        "product_guid": "715a9d18-c84b-4cc9-b077-bbfa41c68040",
        "rating": 4,
        "review": "This is a great product",
        "reviewer": "Mark Doe",
        "review_date": "2020-03-26T12:23:27+0800"
    }
}
```

`GET /product/reviews/<productGuid>`

Returns all product review that matches the given `productGuid`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/ProductReview/product/reviews/64e78423-d8d7-429e-b6b7-b202885282c0
```

**Sample Response**

If the request is sucessful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully get all product review",
    "warnings": [],
    "productReview": [
        {
            "id": "0c12b86b-773e-40d8-8531-b83eb8febc93",
            "product_guid": "64e78423-d8d7-429e-b6b7-b202885282c0",
            "rating": 10,
            "review": "Test",
            "reviewer": "John Doe",
            "review_date": "2020-03-31T09:43:17+0800"
        },
        {
            "id": "51224e02-cfad-47bf-9354-f65fe33e7482",
            "product_guid": "64e78423-d8d7-429e-b6b7-b202885282c0",
            "rating": 7,
            "review": "Test",
            "reviewer": "John Doe",
            "review_date": "2020-03-31T09:43:22+0800"
        }
    ]
}
```

`DELETE /product/review/<productReviewId>`

Deletes an product review that matches the given `productReviewId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/ProductReview/product/review/24b0f169-8854-4e84-b009-7665f40bc5b5
```

**Sample Response**

If the request is sucessful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully deleted product review",
    "warnings": []
}
```
`GET /product/average`

Get the average of the rating of all existing product

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/ProductReview/product/average
```

**Sample Response**

If the request is sucessful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully get all products average",
    "warnings": [],
    "productsAverage": [
        {
            "product_guid": "64e78423-d8d7-429e-b6b7-b202885282c0",
            "average": 6.0
        },
        {
            "product_guid": "f9ec303e-e75d-4e59-9710-44858024dce4",
            "average": 4.5
        }
    ]
}
```

`GET /product/reviews/<productGuid>/average`

Get the average of the rating of an product that matches the given `productGuid`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/ProductReview/product/reviews/64e78423-d8d7-429e-b6b7-b202885282c0/average
```

**Sample Response**

If the request is sucessful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully get product review average",
    "warnings": [],
    "average": 6.25
}
```

`GET /product/reviews/<productGuid>/count`

Get the total reviews of an product that matches the given `productGuid`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/ProductReview/product/reviews/64e78423-d8d7-429e-b6b7-b202885282c0/count
```

**Sample Response**

If the request is sucessful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully get total review",
    "warnings": [],
    "totalReview": 4
}
```

`GET /product/reviews/range`

Get all product reviews that matches the given `start_date` and `end_date` query

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/ProductReview/product/reviews/range?start_date=2020-01-01&end_date=now
```

**Sample Response**

If the request is sucessful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully get all product review",
    "warnings": [],
    "productReview": [
        {
            "id": "0c12b86b-773e-40d8-8531-b83eb8febc93",
            "product_guid": "64e78423-d8d7-429e-b6b7-b202885282c0",
            "rating": 10,
            "review": "Test",
            "reviewer": "John Doe",
            "review_date": "2020-03-31T09:43:17+0800"
        },
        {
            "id": "0e08f7a7-d369-4369-94fc-a2bd1427e17f",
            "product_guid": "f9ec303e-e75d-4e59-9710-44858024dce4",
            "rating": 5,
            "review": "Test",
            "reviewer": "test",
            "review_date": "2020-04-06T22:15:14+0800"
        }
    ]
}
```

`GET /product/reviews/<productGuid>/range`

Get all product reviews that matches the given `start_date` and `end_date` query and given `productGuid`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/ProductReview/product/reviews/64e78423-d8d7-429e-b6b7-b202885282c0/range?start_date=2020-01-01&end_date=now
```

**Sample Response**

If the request is sucessful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "OK",
    "message": "Successfully get all product review",
    "warnings": [],
    "productReview": [
        {
            "id": "0c12b86b-773e-40d8-8531-b83eb8febc93",
            "product_guid": "64e78423-d8d7-429e-b6b7-b202885282c0",
            "rating": 10,
            "review": "Test",
            "reviewer": "John Doe",
            "review_date": "2020-03-31T09:43:17+0800"
        },
        {
            "id": "51224e02-cfad-47bf-9354-f65fe33e7482",
            "product_guid": "64e78423-d8d7-429e-b6b7-b202885282c0",
            "rating": 7,
            "review": "Test",
            "reviewer": "John Doe",
            "review_date": "2020-03-31T09:43:22+0800"
        }
    ]
}```