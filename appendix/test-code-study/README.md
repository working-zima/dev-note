# 5주차 과제 테스트 코드 분석

## 프로젝트 폴더 구조

├─/fixtures/
│  ├─foods.ts
│  ├─index.ts
│  ├─receipt.ts
│  └─restaurants.ts
│
├─/src/
│  ├─/components/
│  │  ├─Cart.tsx
│  │  ├─CartItem.tsx
│  │  ├─Categories.tsx
│  │  ├─Category.tsx
│  │  ├─FilterableRestaurantTable.tsx
│  │  ├─Menu.tsx
│  │  ├─MenuItem.tsx
│  │  ├─OrderButton.tsx
│  │  ├─ReceiptPrinter.tsx
│  │  ├─RestaurantRow.tsx
│  │  ├─RestaurantTable.tsx
│  │  ├─SearchBar.tsx
│  │  └─TextField.tsx
│  │
│  ├─/hooks/
│  │  ├─useCreateOrder.ts
│  │  └─useFetchRestaurants.ts
│  │
│  ├─/mocks/
│  │  ├─handlers.ts
│  │  └─server.ts
│  │
│  ├─/types/
│  │  ├─Food.ts
│  │  ├─Receipt.ts
│  │  └─Restaurant.ts
│  │
│  ├─/utils/
│  │  ├─calculateTotalPrice.ts
│  │  ├─extractCategories.ts
│  │  └─filterRestaurants.ts
│  │
│  ├─App.tsx
│  ├─Main.tsx
│  └─setupTests.ts

## 목차

- [App](./app.md)
  - [Cart](./cart.md)
    - [OrderButton](./orderButton.md)
    - [CartItem](./cartItem.md)
      - [MenuItem](./menuItem.md)
  - [FilterableRestaurantTable](./filterableRestaurantTable.md)
    - [SearchBar](./searchBar.md)
      - [TextField](./textField.md)
      - [Categories](./categories.md)
        - [Category](./cartegory.md)
    - [RestaurantTable](./restaurantTable.md)
      - [RestaurantRow](./restaurantRow.md)
        - [Menu](./menu.md)
          - [MenuItem](./menuItem.md)
  - [ReceiptPrinter](./receiptPrinter.md)
    - [MenuItem](./menuItem.md)
- [extractCategories](./extractCategories.md)
- [filterRestaurants](./filterRestaurants.md)
