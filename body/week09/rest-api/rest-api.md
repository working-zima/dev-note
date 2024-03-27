# 쇼핑몰 API

## POST /session

로그인.

### Request

```json
{
  "email": "tester@example.com",
  "password": "password"
}
```

### Response

#### HTTP Status Code

HTTP Status Code | 설명
:-: | :-:
201 Created | 로그인 성공
400 Bad Request | 로그인 실패

#### 성공했을 때 Body (JSON)

```json
{
  "accessToken": "Header.Payload.Signature"
}
```

## DELETE /session

Access Token 무효화.

## POST /users

회원 가입.

### Request

```json
{
  "email": "tester@example.com",
  "name": "테스터",
  "password": "password"
}
```

### Request

#### HTTP Status Code

HTTP Status Code | 설명
:-: | :-:
201 Created | 회원 가입 성공
400 Bad Request | 회원 정보가 잘못됨. (이메일 중복 등)
422 Unprocessable Content | 회원 정보가 올바르지 않음.

#### 성공했을 때 Body (JSON)

```json
{
  "accessToken": "Header.Payload.Signature"
}
```

## GET /users/me

로그인한 회원 정보 얻기.

### Response

#### HTTP Status Code

HTTP Status Code | 설명
:-: | :-:
200 OK | Access Token이 올바름.
403 Forbidden | Access Token이 올바르지 않음.

#### Body (JSON)

```json
{
  "id": "0BV000USR0001",
  "name": "테스터"
}
```

## GET /categories

카테고리 목록을 얻는다.

### Response

```json
{
  "categories": [
    {
      "id": "0BV000CAT0001",
      "name": "top"
    },
    {
      "id": "0BV000CAT0002",
      "name": "outer"
    }
  ]
}
```

## GET /products

상품 목록을 얻는다. 상품 목록에는 간소화된 정보만 포함된다.

특정 카테고리를 지정해서 해당 카테고리에 속한 상품 목록만 얻을 수도 있다.

### Request

#### Query Parameters

항목 | 필수 여부 | 설명
:-: | :-: | :-:
categoryId | Optional | 카테고리 ID <br> 카테고리 ID를 지정하면 해당 카테고리에 속한 상품 목록을 돌려준다. <br> 카테고리 ID를 지정하지 않으면 카테고리와 무관하게 모든 상품 목록을 돌려준다.

### Response

```json
{
  "products": [
    {
      "id": "0BV000PRO0001",
      "category": {
        "id": "0BV000CAT0001",
        "name": "top"
      },
      "thumbnail": {
        "url": "https://example.com/products/01.jpg"
      },
      "name": "맨투맨",
      "price": 128000
    },
    {
      "id": "0BV000PRO0002",
      "category": {
        "id": "0BV000CAT0001",
        "name": "top"
      },
      "thumbnail": {
        "url": "https://example.com/products/02.jpg"
      },
      "name": "셔츠",
      "price": 118000
    }
  ]
}
```

## GET /products/{id}

상품 상세 정보를 얻는다.

### Response

```json
{
  "id": "0BV000PRO0001",
  "category": {
    "id": "0BV000CAT0001",
    "name": "top"
  },
  "images": [
    {
      "url": "https://example.com/products/01.jpg"
    }
  ],
  "name": "맨투맨",
  "price": 128000,
  "options": [
    {
      "id": "0BV000OPT0001",
      "name": "컬러",
      "items": [
        {
          "id": "0BV000ITEM001",
          "name": "black"
        },
        {
          "id": "0BV000ITEM002",
          "name": "white"
        }
      ]
    },
    {
      "id": "0BV000OPT0002",
      "name": "사이즈",
      "items": [
        {
          "id": "0BV000ITEM003",
          "name": "S"
        },
        {
          "id": "0BV000ITEM004",
          "name": "M"
        }
      ]
    }
  ],
  "description": "편하게 입을 수 있는 맨투맨"
}
```

## GET /cart

장바구니 정보를 얻는다.

### Response

```json
{
  "lineItems": [
    {
      "id": "0BV000CLI0001",
      "product": {
        "id": "0BV000PRO0001",
        "thumbnail": {
          "url": "https://example.com/products/01.jpg"
        },
        "name": "맨투맨"
      },
      "options": [
        {
          "name": "컬러",
          "item": {
            "name": "black"
          }
        },
        {
          "name": "사이즈",
          "item": {
            "name": "M"
          }
        }
      ],
      "unitPrice": 128000,
      "quantity": 2,
      "totalPrice": 256000
    }
  ],
  "totalPrice": 256000
}
```

## POST /cart/line-items

장바구니에 상품을 담는다.

### Request

```json
{
  "productId": "0BV000PRO0001",
  "options": [
    {
      "id": "0BV000OPT0001",
      "itemId": "0BV000ITEM001"
    },
    {
      "id": "0BV000OPT0002",
      "itemId": "0BV000ITEM004"
    }
  ],
  "quantity": 1
}
```

## GET /orders

주문 목록을 얻는다. 주문 목록에서는 주문 간소화된 정보만 포함된다.

### Response

```json
{
  "orders": [
    {
      "id": "0BV000ODR0001",
      "title": "맨투맨",
      "totalPrice": 256000,
      "status": "paid",
      "orderedAt": "2023-01-01 00:00:00"
    }
  ]
}
```

## GET /orders/{id}

주문 상세 정보를 얻는다.

### Response

```json
{
  "id": "0BV000ODR0001",
  "title": "맨투맨",
  "lineItems": [
    {
      "id": "0BV000OLI0001",
      "product": {
        "id": "0BV000PRO0001",
        "thumbnail": {
          "url": "https://example.com/products/01.jpg"
        },
        "name": "맨투맨"
      },
      "options": [
        {
          "name": "컬러",
          "item": {
            "name": "black"
          }
        },
        {
          "name": "사이즈",
          "item": {
            "name": "M"
          }
        }
      ],
      "unitPrice": 128000,
      "quantity": 2,
      "totalPrice": 256000
    }
  ],
  "totalPrice": 256000,
  "status": "paid",
  "orderedAt": "2023-01-01 00:00:00"
}
```

## POST /orders

장바구니에 담긴 상품을 모두 주문한다.

### Request

```json
{
  "receiver": {
    "name": "홍길동",
    "address1": "서울특별시 성동구 상원12길 34",
    "address2": "ㅇㅇㅇ호",
    "postalCode": "04790",
    "phoneNumber": "01012345678"
  },
  "payment": {
    "merchantId": "ORDER-20230101-00000001",
    "transactionId": "123456789012"
  }
}
```
