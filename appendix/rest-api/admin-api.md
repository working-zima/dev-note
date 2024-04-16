# 쇼핑몰 관리자 API

## POST /admin/session

로그인.

### Request

```json
{
  "email": "admin@example.com",
  "password": "password"
}
```

### Response

#### HTTP Status Code

HTTP Status Code | 설명
:-: | :-:
201 Created | 로그인 성공
400 Bad Request | 로그인 실패 (관리자 권한이 없는 경우 포함)

#### 성공했을 때 Body (JSON)

```json
{
  "accessToken": "Header.Payload.Signature"
}
```

## DELETE /admin/session

Access Token 무효화.


## GET /admin/users/me

로그인한 회원 정보 얻기.

### Response

#### HTTP Status Code

HTTP Status Code | 설명
:-: | :-:
200 OK | Access Token이 올바름.
403 Forbidden | Access Token이 올바르지 않음(관리자 권한이 없는 경우 포함).


#### Body (JSON)

```json
{
  "id": "0BV000USR0001",
  "name": "관리자"
}
```

## GET /admin/users

회원 목록 얻기.

### Response

```json
{
  "users": [
    {
      "id": "0BV000USR0001",
      "name": "관리자",
      "email": "admin@example.com",
      "role": "ADMIN"
    }
  ]
}
```

## GET /admin/categories

카테고리 목록을 얻는다.

### Response

```json
{
  "categories": [
    {
      "id": "0BV000CAT0001",
      "name": "top",
      "hidden": false
    },
    {
      "id": "0BV000CAT0002",
      "name": "outer",
      "hidden": false
    }
  ]
}
```

## GET /admin/categories/{id}

카테고리 정보를 얻는다.

### Response

```json
{
    "id": "0BV000CAT0001",
    "name": "top",
    "hidden": false
}
```

## POST /admin/categories

카테고리 등록.

### Request

```json
{
  "name": "top"
}
```

## PATCH /admin/categories/{id}

카테고리 수정.

### Request

```json
{
  "name": "top",
  "hidden": false
}
```

## GET /admin/products

상품 목록을 얻는다.

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
      "price": 128000,
      "hidden": false
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
    },
    "hidden": false
  ]
}
```

## GET /admin/products/{id}

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
      "id": "0BV000IMG0001",
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
  "description": "편하게 입을 수 있는 맨투맨",
  "hidden": false
}
```

## POST /admin/products

상품 추가.

### Request

```json
{
  "categoryId": "0BV000CAT0001",
  "images": [
    {
      "url": "https://example.com/products/01.jpg"
    }
  ],
  "name": "맨투맨",
  "price": 128000,
  "options": [
    {
      "name": "컬러",
      "items": [
        {
          "name": "black"
        },
        {
          "name": "white"
        }
      ]
    },
    {
      "name": "사이즈",
      "items": [
        {
          "name": "S"
        },
        {
          "name": "M"
        }
      ]
    }
  ],
  "description": "편하게 입을 수 있는 맨투맨"
}
```

## PATCH /admin/products/{id}

상품 수정.

### Request

상품 정보를 수정하면서 추가 가능한 image, option, options의 item은 id를 따로 지정하지 않으면 “추가”로 처리된다.

```json
{
  "categoryId": "0BV000CAT0001",
  "images": [
    {
      // ID가 있으면 기존 이미지 수정, 없으면 새 이미지 추가
      "id": "0BV000IMG0001",
      "url": "https://example.com/products/01.jpg"
    }
  ],
  "name": "맨투맨",
  "price": 128000,
  "options": [
    {
      // ID가 있으면 기존 옵션 수정, 없으면 새 옵션 추가
      "id": "0BV000OPT0001",
      "name": "컬러",
      "items": [
        {
          // ID가 있으면 기존 아이템 수정, 없으면 새 아이템 추가
          "id": "0BV000ITEM001",
          "name": "black",
        },
        {
          "id": "0BV000ITEM002",
          "name": "white",
        }
      ]
    },
    {
      // ID가 있으면 기존 옵션 수정, 없으면 새 옵션 추가
      // → ID가 없으므로 새로 추가되는 옵션
      "name": "사이즈",
      "items": [
        {
          // ID가 있으면 기존 아이템 수정, 없으면 새 아이템 추가
          // → ID가 없으므로 새로 추가되는 아이템
          "name": "S"
        },
        {
          "name": "M"
        }
      ]
    }
  ],
  "description": "편하게 입을 수 있는 맨투맨",
  "hidden": false
}
```

## GET /admin/orders

주문 목록을 얻는다. 주문 목록에서는 주문 간소화된 정보만 포함된다.

### Response

```json
{
  "orders": [
    {
      "id": "0BV000ODR0001",
      "orderer": {
        "name": "Tester"
      },
      "title": "맨투맨",
      "totalPrice": 256000,
      "status": "paid",
      "orderedAt": "2023-01-01 00:00:00"
    }
  ]
}
```

## GET /admin/orders/{id}

주문 상세 정보를 얻는다.

### Response

```json
{
  "id": "0BV000ODR0001",
  "title": "맨투맨",
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
  "totalPrice": 256000,
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
  },
  "status": "paid",
  "orderedAt": "2023-01-01 00:00:00"
}
```

## PATCH /admin/orders/{id}

주문 상태를 변경한다.

상태 목록:

1. paid
2. ready
3. shipping
4. complete
5. cancel

### Request

```json
{
  "status": "ready"
}
```
