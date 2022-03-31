# Requests - cURL

## Get Lessons 
```sh
curl --location --request GET 'http://localhost:5000/lessons' \
--data-raw ''
```

## Get User 
```sh
curl --location --request GET 'http://localhost:5000/user' \
--data-raw ''
```

## Checkout
```sh
curl --location --request POST 'http://localhost:5000/checkout' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name": "John",
    "phone": "085564654894894",
    "items": [
        {
            "_id": "620d0f19d898f946929546f2",
            "space": 1
        },
        {
            "_id": "620d0f19d898f946929546f3",
            "space": 1
        }
    ]
}'
```

## Update Lessons
```sh
curl --location --request PUT 'http://localhost:5000/lessons/620e1fab6349b2e2ba9802e3' \
--data-raw ''
```
