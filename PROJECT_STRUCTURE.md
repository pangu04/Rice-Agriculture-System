# Cấu trúc thư mục đề xuất (chuẩn hoá để code đúng ngay từ đầu)

Mục tiêu: tách rõ FE/BE/infra/docs theo kiểu mono-repo, dễ chạy local và dễ deploy sau này.

---

## 1) Mono-repo layout (khuyến nghị)

```
learning-schedule-project/
  frontend/
  backend/
  infra/
  docs/
  .gitignore
  README.md
```

### 1.1 `infra/`
- Chứa Docker Compose + file env mẫu

Gợi ý:
```
infra/
  docker-compose.yml
  .env.example
```

### 1.2 `docs/`
- Postman collection, ERD, hình minh hoạ

Gợi ý:
```
docs/
  postman/
    LearningSchedule.postman_collection.json
  erd/
    erd.png
```

---

## 2) Frontend structure (React + Vite + Ant Design)

### 2.1 Tree đề xuất
```
frontend/
  src/
    api/
      axiosClient.ts
      authApi.ts
      productApi.ts
      orderApi.ts
      cartApi.ts
    app/
      App.tsx
      main.tsx
    assets/
    components/
      common/
      forms/
    layouts/
      AdminLayout/
      ShopLayout/
    routes/
      index.tsx
      guards/
        RequireAuth.tsx
        RequireRole.tsx
    features/
      auth/
        pages/
          LoginPage.tsx
          RegisterPage.tsx
        auth.store.ts
      products/
        pages/
          ProductListPage.tsx
          ProductDetailPage.tsx
        components/
      cart/
        pages/
          CartPage.tsx
      checkout/
        pages/
          CheckoutPage.tsx
      orders/
        pages/
          MyOrdersPage.tsx
          OrderDetailPage.tsx
      admin/
        products/
          AdminProductPage.tsx
        orders/
          AdminOrderPage.tsx
        dashboard/
          DashboardPage.tsx
    styles/
    types/
    utils/
  index.html
  vite.config.ts
  package.json
```

### 2.2 Quy tắc tổ chức code
- **Feature-based**: code nằm theo tính năng, không theo “loại file” quá cứng.
- `api/` chỉ làm nhiệm vụ gọi API + map response.
- `routes/guards/` chỉ làm nhiệm vụ bảo vệ route (auth/role).

### 2.3 Lỗi thường gặp
- Để logic gọi API rải rác trong component → khó maintain.
- Không chuẩn hoá error handling (mỗi trang xử lý 1 kiểu).

### 2.4 Best practice
- 1 `axiosClient` duy nhất + interceptor attach token.
- Dùng `types/` cho DTO types (request/response).

---

## 3) Backend structure (Spring Boot REST API)

### 3.1 Tree đề xuất (package-by-feature hoặc layered)

#### Option A — Layered (dễ cho người mới)
```
backend/src/main/java/com/company/ricepesticide/
  config/
  controller/
  dto/
    request/
    response/
  entity/
  exception/
  mapper/
  repository/
  security/
    jwt/
    userdetails/
  service/
  util/
```

#### Option B — Package-by-feature (dễ scale)
```
backend/src/main/java/com/company/ricepesticide/
  common/
    exception/
    response/
  auth/
    controller/
    dto/
    service/
  product/
    controller/
    dto/
    entity/
    repository/
    service/
  order/
    ...
  dashboard/
    ...
```

> Khuyến nghị: **tuần 1 dùng Option A** (đỡ rối), khi ổn thì chuyển Option B.

### 3.2 Nguyên tắc kiến trúc
- **Controller**: nhận request, validate, gọi service, trả response.
- **Service**: business logic + transaction.
- **Repository**: query DB.
- **DTO**: request/response; không expose entity.
- **Security**: tách riêng JWT, config, filters.

### 3.3 Lỗi thường gặp
- Đưa toàn bộ logic vào Controller.
- Trả Entity trực tiếp → lộ field nhạy cảm (password), dính lazy loading.

### 3.4 Best practice
- Global exception handler.
- Response format thống nhất.
- Seed data cho admin/roles.

---

## 4) Naming conventions (đặt tên)

### 4.1 API endpoints
- Public: `/api/products`, `/api/categories`
- Auth: `/api/auth/login`, `/api/auth/register`
- Current user: `/api/me`
- Admin: `/api/admin/products`, `/api/admin/orders`, `/api/admin/dashboard/*`

### 4.2 Status enums (gợi ý)
- `OrderStatus`: `PENDING`, `CONFIRMED`, `SHIPPED`, `DONE`, `CANCELLED`
- `PaymentStatus`: `UNPAID`, `PAID`

---

## 5) Checklist “setup đúng ngay từ đầu”
- [ ] Có `.env.example` cho DB/JWT/Cloudinary
- [ ] Không commit secrets
- [ ] Có seed roles + admin
- [ ] Có Postman collection
- [ ] Có README hướng dẫn chạy local

