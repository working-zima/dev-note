# 1. 개발하기 전 준비

## 용어

- Product: 상품
  - Summary: 상품에 대한 요약 정보
  - Detail: 상품에 대한 상세 정보
  - Image: 상품 이미지
  - Option: 상품에 대한 상세 옵션 종류 (색상, 크기 등)
    - OptionItem: 옵션에 대한 상세 옵션 값 (옵션이 색상이라면 이건 Blue, Red 같은 걸 의미함)
- Category: 상품에 대한 분류
- Cart: 장바구니
  - LineItem: 장바구니에 담긴 것 (상품, 옵션, 수량 등을 동시에 다룸)
    - 여기서도 Option과 OptionItem을 사용한다.
    - 용어는 동일하지만 Product와 다른 구성을 갖기 때문에, 여기서는 Product와 Order라는 접두어를 활용한다.
    - 시스템을 분리할 수 있다면, 근본적으로 나누는 걸 추천(상품 정보 확인 / 장바구니 / 주문).
- Order: 주문
  - 여기서도 동일한 LineItem 활용.
  - Detail: 주문에 대한 상세 정보
  - Cart와 동일한 LineItem을 활용한다(오히려 Cart가 Order의 LineItem을 활용하는 것에 가깝다).
- User: 사용자

## 기능

1. 상품 목록 확인 ✅
2. 상품 상세 정보 확인 ✅
3. 장바구니에 상품 담기 ✅
4. 로그인 ✅
5. 회원 가입 ✅
6. 주문 목록 확인 ✅
7. 주문 상세 확인 ✅
8. 주문하기 → 배송지 입력, 결제

## 화면

- 홈 페이지 - `/` ✅
- 상품 목록 페이지 - `/products` ✅
- 상품 상세 페이지 - `/products/{id}` ✅
- 장바구니 페이지 - `/cart` ✅
- 로그인 페이지 - `/login` ✅
- 회원 가입 페이지 - `/signup` ✅
- 회원 가입 완료 페이지 - `/signup/complete` ✅
- 주문 목록 페이지 - `/orders` ✅
- 주문 상세 페이지 - `/orders/{id}` ✅
- 주문 페이지 - `/order`
- 주문 완료 페이지 - `/order/complete`

## REST API

[온라인 쇼핑몰 REST API](../../appendix/rest-api/rest-api.md)
[주문 기능이 추가된 API Base URL](https://shop-demo-api-03.fly.dev)
