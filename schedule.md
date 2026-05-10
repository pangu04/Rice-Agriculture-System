# Schedule 2 tuần (Project-based) — Website bán thuốc cho cây lúa (Mekong Delta)

## 0) Cách dùng schedule này

- **Thời lượng gợi ý**: 14 ngày × 2 buổi/ngày × ~2.5–3 giờ/buổi (có thể co giãn).
- **Nguyên tắc**:
  - Làm **MVP chạy được end-to-end sớm** (FE ↔ BE ↔ DB ↔ Upload ảnh).
  - Mỗi ngày đều có “**mini-demo**” (tự test UI + Postman).
  - Không học lan man: học tới đâu **gắn trực tiếp vào task** tới đó.

### Definition of Done (DoD) tối thiểu cho mỗi ngày
- Backend build + chạy OK, không lỗi runtime cơ bản.
- Có **Postman** test cho endpoint mới.
- Frontend có UI tối thiểu (loading/error/empty state).
- Commit theo chuẩn (gợi ý **Conventional Commits**).

---

## 1) Kiến trúc hệ thống (tổng quan)

### 1.1 Luồng chính
- **React (Vite + Ant Design)** → gọi API qua **Axios**
- **Spring Boot REST API** → xử lý nghiệp vụ + phân quyền (**Spring Security + JWT**)
- **PostgreSQL** chạy trong **Docker** (Docker Compose)
- **Cloudinary** dùng để lưu ảnh sản phẩm:
  - React upload ảnh → Spring Boot nhận file → upload lên Cloudinary → nhận `secure_url` → lưu vào DB → React hiển thị.

### 1.2 Module nghiệp vụ (MVP)
- Auth (register/login) + JWT
- RBAC: `ADMIN`, `STAFF`, `CUSTOMER`
- Category + Product (CRUD admin), Product listing/search/pagination (public)
- Upload ảnh sản phẩm (Cloudinary)
- Cart (giỏ hàng theo user trong DB)
- Checkout tạo Order
- Order management (customer/admin)
- Dashboard: thống kê + biểu đồ doanh thu

### 1.3 Chuẩn RESTful + chuẩn response (khuyến nghị)
- URL theo tài nguyên: `/api/products`, `/api/categories`, `/api/orders`...
- Phân trang: `page`, `size`, `sort`.
- Response format thống nhất:
  - Thành công: `{ data, meta, message }`
  - Lỗi: `{ errorCode, message, details, timestamp }`

### 1.4 Thứ tự học công nghệ (và vì sao)

1) **Docker + PostgreSQL (Compose)**
- Vì DB là nền tảng dữ liệu. Có DB ổn định thì BE mới chạy đúng và demo được mỗi ngày.

2) **Spring Boot căn bản (Web/JPA/Validation) → REST API**
- Học cách dựng endpoint, DTO/validation, truy vấn DB, response chuẩn.

3) **React (Vite) + Ant Design + React Router**
- Dựng UI nhanh, có routing, có layout customer/admin.

4) **Axios + interceptor + chuẩn error handling**
- Đây là “xương sống” giao tiếp FE↔BE; làm sớm để các module sau không bị rối.

5) **Spring Security + JWT**
- Làm sớm để mọi API sau được bảo vệ đúng từ đầu.

6) **RBAC (role-based) ở BE trước, FE sau**
- BE là nơi enforce security; FE chỉ hỗ trợ UX (ẩn/hiện menu, redirect).

7) **Catalog (Category/Product) → Cloudinary upload**
- Có sản phẩm ổn định rồi mới upload ảnh để tránh đổi data model liên tục.

8) **Cart → Checkout → Orders → Dashboard**
- Theo đúng phụ thuộc nghiệp vụ: đơn hàng cần cart; dashboard cần dữ liệu orders.

---

## 2) Workflow làm việc “giống công ty” (tối giản nhưng thực tế)

> Tài liệu đi kèm trong repo:
> - Backlog/ticket: `TODO.md`
> - Quy ước branch/commit/PR: `CONTRIBUTING.md`
> - Cấu trúc thư mục đề xuất FE/BE: `PROJECT_STRUCTURE.md`

### 2.1 Git workflow (đơn giản, dễ áp dụng)
- Branch chính: `main` (release), `develop` (tích hợp)
- Làm theo nhánh tính năng: `feature/auth-jwt`, `feature/product-crud`...
- Mỗi tính năng:
  1) Tạo ticket nhỏ (trong `TODO.md` hoặc GitHub Issues)
  2) Tạo branch → code → tự test → commit → merge về `develop`

### 2.2 Checklist code review tự làm (tự review)
- Có validate input? Có xử lý lỗi? Có log cần thiết?
- Có test nhanh bằng Postman?
- Có làm hỏng route/role guard?
- UI có loading/error/empty state?

---

## 3) Roadmap 14 ngày (2 tuần) — chia theo từng ngày/từng buổi

> Gợi ý ký hiệu: ✅ Done / ⬜ Todo

---

### Ngày 1 — Kickoff dự án + Docker DB + chạy end-to-end

#### Buổi 1 (2.5–3h): Khởi tạo nền tảng FE/BE/DB
- **Mục tiêu**: chạy được 1 luồng React gọi Spring Boot, Spring Boot kết nối PostgreSQL Docker.
- **Kiến thức cần học**:
  - Docker Compose cho PostgreSQL
  - Spring Boot cấu hình datasource
  - Vite project + cấu hình môi trường
- **Task cần làm**:
  1) Tạo cấu trúc repo (có thể mono-repo: `frontend/`, `backend/`, `infra/`).
  2) Tạo `docker-compose.yml` chạy PostgreSQL.
  3) Tạo Spring Boot project (Web, Security, Validation, JPA, PostgreSQL driver).
  4) Tạo Vite + React + Ant Design.
  5) Tạo endpoint `/api/health` trả về OK.
  6) FE gọi `/api/health` và hiển thị kết quả.
- **Kết quả đạt được**:
  - DB chạy được
  - BE chạy được
  - FE gọi API được
- **Checklist**:
  - ⬜ `docker compose up` chạy Postgres OK
  - ⬜ Spring Boot connect DB OK
  - ⬜ FE gọi API hiển thị “OK”

#### Buổi 2 (2.5–3h): Thiết kế domain + ERD + seed data
- **Mục tiêu**: chốt entity MVP và quan hệ để tránh đổi schema nhiều.
- **Kiến thức cần học**:
  - ERD cơ bản
  - JPA mapping (OneToMany/ManyToOne)
  - Seed data (data mẫu)
- **Task cần làm**:
  1) Vẽ ERD tối thiểu: `users`, `roles`, `user_roles`, `categories`, `products`, `carts`, `cart_items`, `orders`, `order_items`.
  2) Tạo entities JPA + migrations (khuyến nghị Flyway/Liquibase; nếu chưa kịp, dùng `ddl-auto=update` tạm thời và chốt Flyway sau).
  3) Seed: admin + vài category + vài product.
- **Kết quả đạt được**: DB có dữ liệu mẫu để dev nhanh.
- **Checklist**:
  - ⬜ Có admin user seed
  - ⬜ Có ít nhất 10 sản phẩm mẫu
  - ⬜ Query list product trả dữ liệu từ DB

---

### Ngày 2 — Auth/JWT backend

#### Buổi 1 (2.5–3h): Register/Login + phát JWT
- **Mục tiêu**: có đăng ký/đăng nhập, trả token.
- **Kiến thức cần học**:
  - JWT là gì, access token dùng để làm gì
  - Password hashing (BCrypt)
  - Spring Security cơ bản
- **Task cần làm**:
  1) Endpoint `POST /api/auth/register`
  2) Endpoint `POST /api/auth/login`
  3) Hash password + verify password
  4) Generate JWT + trả response
  5) Chuẩn hóa error response (401/400)
- **Kết quả đạt được**: Postman login thành công, lấy token.
- **Checklist**:
  - ⬜ Register OK
  - ⬜ Login OK, nhận JWT
  - ⬜ Login sai mật khẩu trả 401 + message rõ

#### Buổi 2 (2.5–3h): JWT filter + bảo vệ endpoint
- **Mục tiêu**: gọi endpoint cần auth phải có Bearer token.
- **Kiến thức cần học**:
  - Filter chain
  - `Authorization: Bearer <token>`
- **Task cần làm**:
  1) Tạo JWT filter đọc header, validate token.
  2) Tạo endpoint `/api/me` trả thông tin user hiện tại.
  3) Cấu hình security: `/api/auth/**` public, `/api/**` còn lại cần auth (tạm thời).
- **Kết quả đạt được**: `/api/me` chỉ truy cập được khi có token.
- **Checklist**:
  - ⬜ Không token → 401
  - ⬜ Token hợp lệ → trả user info

---

### Ngày 3 — RBAC backend + Auth frontend

#### Buổi 1 (2.5–3h): RBAC backend (ADMIN/STAFF/CUSTOMER)
- **Mục tiêu**: phân quyền API theo role.
- **Kiến thức cần học**:
  - RBAC là gì
  - `@PreAuthorize` / endpoint authorization
- **Task cần làm**:
  1) Seed roles + gán role khi register (mặc định CUSTOMER).
  2) Bảo vệ các endpoint admin (ví dụ `/api/admin/**`).
  3) Viết Postman test: customer gọi admin endpoint phải bị chặn.
- **Kết quả đạt được**: Role hoạt động đúng.
- **Checklist**:
  - ⬜ CUSTOMER bị chặn admin API
  - ⬜ ADMIN gọi admin API OK

#### Buổi 2 (2.5–3h): FE Auth (UI + token storage + interceptor)
- **Mục tiêu**: FE đăng nhập, tự đính kèm token khi gọi API.
- **Kiến thức cần học**:
  - Antd Form
  - Axios interceptors
  - React Router basics
- **Task cần làm**:
  1) Trang `/login`, `/register`.
  2) Lưu token (localStorage) + trạng thái đăng nhập (Context hoặc store đơn giản).
  3) Axios instance tự gắn `Authorization` header.
  4) Logout.
- **Kết quả đạt được**: FE login xong gọi `/api/me` OK.
- **Checklist**:
  - ⬜ Login UI hoạt động
  - ⬜ Refresh trang vẫn giữ trạng thái (nếu token còn)
  - ⬜ Token tự gắn vào request

---

### Ngày 4 — Product/Category backend (CRUD + search + pagination)

#### Buổi 1 (2.5–3h): Category CRUD (admin)
- **Mục tiêu**: có API quản lý danh mục.
- **Kiến thức cần học**:
  - RESTful CRUD
  - Validation DTO
- **Task cần làm**:
  1) `POST /api/admin/categories`
  2) `PUT /api/admin/categories/{id}`
  3) `DELETE /api/admin/categories/{id}`
  4) `GET /api/categories` (public list)
- **Kết quả đạt được**: quản lý danh mục xong.
- **Checklist**:
  - ⬜ Validation name not blank
  - ⬜ Không cho xóa category nếu còn product (tối thiểu trả lỗi rõ)

#### Buổi 2 (2.5–3h): Product CRUD + search + pagination
- **Mục tiêu**: xương sống catalog.
- **Kiến thức cần học**:
  - Pagination (Spring Data)
  - Search theo keyword/category
- **Task cần làm**:
  1) Admin CRUD product (chưa cần ảnh, ảnh làm ngày 5).
  2) Public endpoints: list + detail.
  3) Query: `keyword`, `categoryId`, `minPrice`, `maxPrice` (tối thiểu keyword + category).
- **Kết quả đạt được**: FE có thể list sản phẩm bằng API.
- **Checklist**:
  - ⬜ `GET /api/products?page=&size=` hoạt động
  - ⬜ `GET /api/products/{id}` hoạt động
  - ⬜ Search keyword trả đúng

---

### Ngày 5 — Upload ảnh Cloudinary + Admin UI Product

#### Buổi 1 (2.5–3h): Cloudinary upload backend
- **Mục tiêu**: upload ảnh lấy URL và lưu vào product.
- **Kiến thức cần học**:
  - Cloud storage hoạt động thế nào
  - Upload file trong Spring
- **Task cần làm**:
  1) Tạo cấu hình Cloudinary (env vars).
  2) Endpoint upload (vd: `POST /api/admin/uploads/images`).
  3) Khi tạo/cập nhật product: nhận `imageUrl`.
- **Kết quả đạt được**: upload ảnh xong hiển thị được.
- **Checklist**:
  - ⬜ Upload trả `secure_url`
  - ⬜ Lưu `imageUrl` trong DB
  - ⬜ Lỗi thiếu key/config trả message rõ

#### Buổi 2 (2.5–3h): Admin UI quản lý sản phẩm (Ant Design)
- **Mục tiêu**: admin CRUD product từ UI.
- **Kiến thức cần học**:
  - Antd Table/Modal/Form/Upload
- **Task cần làm**:
  1) Trang Admin Products: table + pagination.
  2) Modal create/edit product.
  3) Upload ảnh (gọi API upload) và set vào form.
- **Kết quả đạt được**: end-to-end admin quản lý catalog.
- **Checklist**:
  - ⬜ Tạo product từ UI OK
  - ⬜ Sửa product OK
  - ⬜ Upload ảnh OK

---

### Ngày 6 — UI khách hàng: browse sản phẩm + responsive

#### Buổi 1 (2.5–3h): Trang Home + Product listing
- **Mục tiêu**: khách hàng xem list sản phẩm, filter cơ bản.
- **Kiến thức cần học**:
  - Responsive layout (Antd Grid)
  - State management tối giản
- **Task cần làm**:
  1) Trang `/` (home) hiển thị list sản phẩm.
  2) Filter theo category.
  3) Search keyword.
  4) Pagination UI.
- **Kết quả đạt được**: duyệt sản phẩm mượt.
- **Checklist**:
  - ⬜ Mobile layout không vỡ
  - ⬜ Pagination hoạt động

#### Buổi 2 (2.5–3h): Trang Product detail + chuẩn bị contract giỏ hàng
- **Mục tiêu**: product detail + thống nhất endpoints cart.
- **Kiến thức cần học**:
  - Route params
  - Thiết kế API contract
- **Task cần làm**:
  1) Trang `/products/:id`.
  2) Nút “Add to cart” (tạm show UI, API làm ngày 7).
  3) Chốt contract cart endpoints:
     - `GET /api/cart`
     - `POST /api/cart/items` (productId, quantity)
     - `PUT /api/cart/items/{itemId}`
     - `DELETE /api/cart/items/{itemId}`
- **Kết quả đạt được**: sẵn sàng làm cart nhanh.
- **Checklist**:
  - ⬜ Product detail hiển thị đủ info
  - ⬜ Contract cart ghi lại trong README/notes

---

### Ngày 7 — Cart end-to-end

#### Buổi 1 (2.5–3h): Cart backend (DB-based)
- **Mục tiêu**: giỏ hàng lưu theo user.
- **Kiến thức cần học**:
  - Transaction
  - Quy tắc quantity
- **Task cần làm**:
  1) Entity cart/cart_item.
  2) Endpoints cart theo contract ngày 6.
  3) Tính subtotal/total.
- **Kết quả đạt được**: giỏ hàng hoạt động bằng Postman.
- **Checklist**:
  - ⬜ Add item OK
  - ⬜ Update quantity OK
  - ⬜ Remove item OK

#### Buổi 2 (2.5–3h): Cart UI
- **Mục tiêu**: trang giỏ hàng hoàn chỉnh.
- **Kiến thức cần học**:
  - UI state + optimistic update (tối thiểu)
- **Task cần làm**:
  1) Trang `/cart`.
  2) Tăng/giảm số lượng.
  3) Tính tổng tiền.
  4) CTA “Checkout”.
- **Kết quả đạt được**: cart end-to-end.
- **Checklist**:
  - ⬜ Reload vẫn còn cart (do DB)
  - ⬜ Empty state rõ ràng

---

## Tuần 2

### Ngày 8 — Checkout tạo Order

#### Buổi 1 (2.5–3h): Create Order backend
- **Mục tiêu**: tạo order snapshot từ cart.
- **Kiến thức cần học**:
  - Order snapshot vs cart (tại sao phải snapshot)
  - Order status
- **Task cần làm**:
  1) `POST /api/orders` tạo order từ cart.
  2) Tạo `order_items` từ `cart_items`.
  3) Clear cart.
  4) Status ban đầu: `PENDING`.
- **Kết quả đạt được**: tạo order OK.
- **Checklist**:
  - ⬜ Order total đúng
  - ⬜ Cart rỗng sau khi tạo order

#### Buổi 2 (2.5–3h): Checkout UI
- **Mục tiêu**: user đặt hàng từ giỏ.
- **Kiến thức cần học**:
  - Form checkout
- **Task cần làm**:
  1) Trang `/checkout`.
  2) Form: tên người nhận, SĐT, địa chỉ, ghi chú (tối thiểu).
  3) Confirm → tạo order → chuyển trang orders.
- **Kết quả đạt được**: luồng mua hàng cơ bản.
- **Checklist**:
  - ⬜ Submit tạo order OK
  - ⬜ Có thông báo success/fail

---

### Ngày 9 — Payment (MVP) + Order API (customer)

#### Buổi 1 (2.5–3h): Payment method (MVP nhanh)
- **Mục tiêu**: có phương thức thanh toán tối thiểu để hoàn thiện flow.
- **Khuyến nghị MVP**: **COD (Thanh toán khi nhận hàng)** + trạng thái thanh toán.
- **Task cần làm**:
  1) Thêm fields: `paymentMethod`, `paymentStatus`.
  2) Checkout cho chọn COD.
  3) Nếu có thời gian: tạo stub `ONLINE` để mở rộng sau.
- **Kết quả đạt được**: order lưu thông tin thanh toán.
- **Checklist**:
  - ⬜ Order có paymentMethod
  - ⬜ PaymentStatus hợp lý (UNPAID/PAID)

#### Buổi 2 (2.5–3h): Order APIs cho khách hàng
- **Mục tiêu**: khách xem đơn của mình.
- **Kiến thức cần học**:
  - Authorization theo userId hiện tại
- **Task cần làm**:
  1) `GET /api/orders` (chỉ của user).
  2) `GET /api/orders/{id}` (chỉ của user).
  3) `POST /api/orders/{id}/cancel` (rule: chỉ cancel khi PENDING).
- **Kết quả đạt được**: user quản lý đơn.
- **Checklist**:
  - ⬜ User không xem được order người khác
  - ⬜ Cancel rule hoạt động

---

### Ngày 10 — Order management (admin/staff) + UI

#### Buổi 1 (2.5–3h): Admin/staff quản lý đơn hàng backend
- **Mục tiêu**: admin/staff xử lý trạng thái.
- **Kiến thức cần học**:
  - State machine đơn hàng (đơn giản)
- **Task cần làm**:
  1) `GET /api/admin/orders` list + filter status.
  2) `PATCH /api/admin/orders/{id}/status`.
  3) Status flow đề xuất: `PENDING → CONFIRMED → SHIPPED → DONE` (hoặc `CANCELLED`).
- **Kết quả đạt được**: admin quản lý order.
- **Checklist**:
  - ⬜ CUSTOMER bị chặn admin order APIs
  - ⬜ Status update có validate (không nhảy bừa)

#### Buổi 2 (2.5–3h): Admin UI quản lý đơn hàng
- **Mục tiêu**: thao tác quản lý đơn từ UI.
- **Task cần làm**:
  1) Trang Admin Orders: table + filter.
  2) Drawer/Modal xem chi tiết.
  3) Dropdown cập nhật status.
- **Kết quả đạt được**: admin flow hoàn chỉnh.
- **Checklist**:
  - ⬜ Update status từ UI OK
  - ⬜ Có loading + error state

---

### Ngày 11 — Dashboard thống kê + biểu đồ doanh thu

#### Buổi 1 (2.5–3h): Dashboard API (aggregate)
- **Mục tiêu**: trả số liệu thống kê.
- **Kiến thức cần học**:
  - Aggregate queries (SUM/COUNT/GROUP BY)
  - Time range
- **Task cần làm**:
  1) `GET /api/admin/dashboard/summary?from=&to=`:
     - totalRevenue
     - totalOrders
     - ordersByStatus
  2) `GET /api/admin/dashboard/revenue-series?from=&to=&unit=day`.
  3) `GET /api/admin/dashboard/top-products`.
- **Kết quả đạt được**: API trả data cho chart.
- **Checklist**:
  - ⬜ Query chạy nhanh với seed data
  - ⬜ Trả meta rõ ràng (from/to/unit)

#### Buổi 2 (2.5–3h): Dashboard UI + charts
- **Mục tiêu**: dashboard hiển thị KPI và biểu đồ.
- **Kiến thức cần học**:
  - Thư viện chart (Recharts / AntV G2Plot)
- **Task cần làm**:
  1) KPI cards.
  2) Line chart doanh thu theo ngày.
  3) Pie/Bar chart đơn theo trạng thái.
- **Kết quả đạt được**: admin dashboard usable.
- **Checklist**:
  - ⬜ Charts render đúng
  - ⬜ Có date range filter

---

### Ngày 12 — Hardening (backend + frontend)

#### Buổi 1 (2.5–3h): Hardening backend
- **Mục tiêu**: hệ thống “chịu lỗi” và phản hồi rõ ràng.
- **Kiến thức cần học**:
  - Global exception handler
  - Validation nâng cao
  - CORS
- **Task cần làm**:
  1) Global exception handler chuẩn.
  2) Validate DTO đầy đủ.
  3) CORS config theo env.
  4) Logging cơ bản (request id nếu có thời gian).
- **Kết quả đạt được**: API lỗi có format thống nhất.
- **Checklist**:
  - ⬜ Không trả stacktrace ra client
  - ⬜ Lỗi 400/401/403/404/500 rõ

#### Buổi 2 (2.5–3h): Hardening frontend
- **Mục tiêu**: UI ổn định khi API lỗi.
- **Task cần làm**:
  1) Unified error handler (toast/notification).
  2) Loading skeleton.
  3) Empty states.
  4) Route guard hoàn thiện (role-based menus).
- **Kết quả đạt được**: UX tốt hơn.
- **Checklist**:
  - ⬜ Không còn “trắng trang” khi API fail
  - ⬜ Error hiển thị hành động tiếp theo

---

### Ngày 13 — Tối ưu dữ liệu + tài liệu hoá + Postman

#### Buổi 1 (2.5–3h): Data integrity + transactional rules
- **Mục tiêu**: giảm bug nghiệp vụ.
- **Kiến thức cần học**:
  - DB constraints + indexes
  - Transaction boundaries
- **Task cần làm**:
  1) Constraints: unique email, not null fields.
  2) Index: products(category_id), orders(user_id, created_at).
  3) Rule tồn kho tối thiểu (nếu có `stockQuantity`): không cho đặt vượt tồn.
- **Kết quả đạt được**: hệ thống chắc hơn.
- **Checklist**:
  - ⬜ Không tạo order vượt tồn
  - ⬜ Query list nhanh hơn (cảm nhận)

#### Buổi 2 (2.5–3h): Tài liệu hoá + Postman collection
- **Mục tiêu**: người khác clone chạy được.
- **Task cần làm**:
  1) `README.md`: cách chạy FE/BE/DB.
  2) Env mẫu: `.env.example`.
  3) Postman collection export.
  4) Ghi chú API endpoints chính.
- **Kết quả đạt được**: project “handover-ready”.
- **Checklist**:
  - ⬜ Clone + run theo README OK
  - ⬜ Postman collection chạy được

---

### Ngày 14 — Deploy-ready + buffer fix bug

#### Buổi 1 (2.5–3h): Chuẩn bị deploy-ready (cấu hình production cơ bản)
- **Mục tiêu**: build artifacts và tách config rõ ràng.
- **Kiến thức cần học**:
  - Build FE
  - Spring profiles (dev/prod)
- **Task cần làm**:
  1) FE build `vite build`.
  2) BE build jar.
  3) Tách cấu hình `application-dev.yml`, `application-prod.yml`.
  4) Notes deploy: nơi host FE/BE, cấu hình env.
- **Kết quả đạt được**: sẵn sàng deploy khi muốn.
- **Checklist**:
  - ⬜ FE build OK
  - ⬜ BE build OK

#### Buổi 2 (2.5–3h): Buffer + tổng kết MVP
- **Mục tiêu**: rà lại toàn bộ flow và chốt MVP.
- **Task cần làm**:
  1) Test luồng chính: register/login → browse → add cart → checkout → orders → admin manage → dashboard.
  2) Fix bug.
  3) Dọn UI nhỏ.
- **Kết quả đạt được**: MVP hoàn thiện.
- **Checklist**:
  - ⬜ Flow end-to-end chạy trơn
  - ⬜ Không còn lỗi “đập vào mặt”

---

## 4) Vì sao roadmap sắp xếp theo thứ tự này?

1) **Làm xương sống trước** (Docker DB + base FE/BE + gọi API): nếu hạ tầng không ổn, mọi tính năng sau sẽ bị kẹt.
2) **Auth + RBAC làm sớm**: tránh làm xong admin UI rồi mới phát hiện thiếu phân quyền/hỏng security.
3) **Catalog trước cart/checkout**: giỏ hàng & đơn hàng phụ thuộc sản phẩm/giá/ảnh/search/pagination.
4) **Order trước dashboard**: dashboard cần dữ liệu orders thật để aggregate đúng.
5) **Hardening & deploy-ready cuối**: chỉ tối ưu khi luồng chính đã chạy, tránh tối ưu sớm.

---

## 5) Giải thích nhanh các khái niệm cốt lõi (đúng format)

### 5.1 REST API
1. **Khái niệm là gì**: Kiểu thiết kế API dựa trên tài nguyên (resources) và HTTP methods.
2. **Dùng để làm gì**: Cho FE/app khác gọi BE theo chuẩn thống nhất.
3. **Vì sao cần dùng**: Dễ mở rộng, dễ test (Postman), dễ tích hợp nhiều client.
4. **Ví dụ thực tế**: `GET /api/products?page=0&size=12`, `POST /api/orders`.
5. **Lỗi thường gặp**: Nhồi quá nhiều logic vào URL; trả response không thống nhất.
6. **Best practice**: chuẩn status code; DTO; pagination; versioning nếu cần.

### 5.2 JWT Authentication
1. **Khái niệm là gì**: Token chứa claims, được ký để xác thực người dùng.
2. **Dùng để làm gì**: FE gửi token để BE biết “ai đang gọi”.
3. **Vì sao cần dùng**: Stateless, dễ scale, hợp REST.
4. **Ví dụ thực tế**: Header `Authorization: Bearer <jwt>`.
5. **Lỗi thường gặp**: Lưu token sai chỗ; thời hạn token quá dài; lộ secret.
6. **Best practice**: expire ngắn; refresh token (nâng cao); không log token.

### 5.3 RBAC (Role-Based Access Control)
1. **Khái niệm là gì**: Kiểm soát quyền dựa trên vai trò.
2. **Dùng để làm gì**: Chặn customer truy cập admin API.
3. **Vì sao cần dùng**: Tính năng quản trị rất nhạy cảm.
4. **Ví dụ thực tế**: `@PreAuthorize("hasRole('ADMIN')")` cho `/api/admin/**`.
5. **Lỗi thường gặp**: Chỉ chặn ở FE mà quên chặn ở BE.
6. **Best practice**: Luôn enforce ở BE; FE chỉ là UX.

### 5.4 Docker Compose (PostgreSQL)
1. **Khái niệm là gì**: Công cụ chạy nhiều container theo 1 file cấu hình.
2. **Dùng để làm gì**: Chạy PostgreSQL ổn định, giống môi trường công ty.
3. **Vì sao cần dùng**: Tránh “máy anh chạy, máy em không chạy”.
4. **Ví dụ thực tế**: `docker compose up -d` bật DB.
5. **Lỗi thường gặp**: Quên volume → mất dữ liệu; port conflict.
6. **Best practice**: dùng `.env`; đặt volume; đặt user/password rõ.

### 5.5 Cloudinary (Cloud Storage cho ảnh)
1. **Khái niệm là gì**: Dịch vụ lưu trữ + xử lý ảnh trên cloud.
2. **Dùng để làm gì**: Lưu ảnh sản phẩm, trả URL CDN.
3. **Vì sao cần dùng**: Không nên lưu ảnh trực tiếp vào DB; server đỡ gánh băng thông.
4. **Ví dụ thực tế**: Spring upload file → nhận `secure_url` → lưu DB.
5. **Lỗi thường gặp**: Lộ API key/secret; upload file quá lớn không validate.
6. **Best practice**: cấu hình env; giới hạn size/type; lưu URL, không lưu file.

---

## 6) Giai đoạn sau (AI / RAG / Computer Vision) — gợi ý tích hợp

- **RAG Chatbot**: sau khi có Orders/Products ổn định, xây pipeline ingest (product info, FAQs) + endpoint chat.
- **Nhận diện bệnh lúa**: thêm service ML tách riêng (Python/FastAPI) hoặc module riêng; FE upload ảnh lá → service dự đoán → gợi ý sản phẩm.

---

## 6.1 Deploy cơ bản (sau khi local ổn định)

### A) Frontend (React Vite)
1. **Khái niệm là gì**: Deploy FE là đưa static files (HTML/CSS/JS) lên hosting.
2. **Dùng để làm gì**: Người dùng truy cập bằng domain, không cần chạy local.
3. **Vì sao cần dùng**: Demo nhanh, share cho người khác test.
4. **Ví dụ thực tế**:
   - Build: `npm run build` → tạo thư mục `dist/`
   - Host: Vercel/Netlify/Cloudflare Pages (dễ nhất cho FE).
5. **Lỗi thường gặp**: Sai base path; cấu hình rewrite cho SPA.
6. **Best practice**: Cấu hình redirect tất cả route về `index.html` (SPA).

### B) Backend (Spring Boot)
1. **Khái niệm là gì**: Deploy BE là chạy ứng dụng (jar) trên server/platform.
2. **Dùng để làm gì**: Cung cấp REST API public.
3. **Vì sao cần dùng**: FE cần API online; chuẩn bị môi trường production.
4. **Ví dụ thực tế**:
   - Build jar: `mvn clean package` (hoặc Gradle)
   - Host: Render/Railway/Fly.io (dễ); hoặc VPS (nâng cao).
5. **Lỗi thường gặp**: Thiếu env vars (DB/JWT/Cloudinary); CORS sai.
6. **Best practice**: Dùng `SPRING_PROFILES_ACTIVE=prod`; log level hợp lý; không commit secrets.

### C) Database (PostgreSQL)
- Giai đoạn đầu có thể dùng managed PostgreSQL (Render/Railway/Supabase/Neon) thay vì tự vận hành.

---

## 6.2 CI/CD tối thiểu (khi đã quen)
- **Mục tiêu**: mỗi lần push code → chạy build/test tự động.
- Gợi ý:
  - GitHub Actions workflow:
    - FE: install → lint/build
    - BE: build (mvn/gradle) → run tests (nếu có)
  - Sau đó mới mở rộng: auto deploy lên Vercel/Render.

---

## 7) Checklist tổng (MVP)
- ⬜ Auth: register/login + JWT
- ⬜ RBAC: ADMIN/STAFF/CUSTOMER
- ⬜ Category/Product CRUD (admin) + search + pagination
- ⬜ Upload ảnh Cloudinary
- ⬜ UI khách browse sản phẩm (responsive)
- ⬜ Cart DB-based
- ⬜ Checkout tạo order
- ⬜ Customer orders (list/detail/cancel)
- ⬜ Admin order management
- ⬜ Dashboard KPI + charts
- ⬜ README + Postman collection




