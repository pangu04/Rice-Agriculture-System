# TODO / Backlog (theo ticket)

> Mục tiêu: chia việc theo kiểu công ty (backlog → sprint 2 tuần → ticket nhỏ có DoD).
>
> Quy ước:
> - Ticket ID: `LS-XXX`
> - Trạng thái: `TODO` | `IN PROGRESS` | `BLOCKED` | `DONE`
> - Estimate (thô): `S` (≤2h) | `M` (2–4h) | `L` (4–8h)

---

## 1) Epics

- **E1 Foundation**: repo structure, Docker DB, base FE/BE, env
- **E2 Auth & Security**: register/login, JWT, RBAC
- **E3 Catalog**: category/product CRUD, search, pagination
- **E4 Media Upload**: Cloudinary upload
- **E5 Shopping**: cart, checkout
- **E6 Orders**: customer orders, admin order management
- **E7 Dashboard**: KPI + charts
- **E8 Hardening & Docs**: validation/error handling, README, Postman
- **E9 Deploy-ready**: build, profiles, notes deploy

---

## 2) Sprint 2 tuần (MVP)

### E1 — Foundation

#### LS-001: Khởi tạo repo structure (mono-repo)
- **Status**: TODO
- **Estimate**: S
- **Mô tả**: tạo folders `frontend/`, `backend/`, `infra/`, `docs/`.
- **Acceptance criteria**:
  - Có README ngắn về cấu trúc
  - Không commit secrets

#### LS-002: PostgreSQL bằng Docker Compose
- **Status**: TODO
- **Estimate**: S
- **Acceptance criteria**:
  - `docker compose up -d` chạy OK
  - Có volume để không mất data
  - Có `.env.example` cho DB

#### LS-003: Backend healthcheck + kết nối DB
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - `GET /api/health` trả OK
  - App connect Postgres OK
  - Có log lỗi rõ khi DB down

#### LS-004: Frontend gọi thử API
- **Status**: TODO
- **Estimate**: S
- **Acceptance criteria**:
  - FE hiển thị kết quả từ `/api/health`
  - Có config baseURL theo env

---

### E2 — Auth & Security

#### LS-010: Register API
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - `POST /api/auth/register` tạo user
  - Password được hash
  - Validate email/password (tối thiểu not blank)

#### LS-011: Login API + JWT
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - `POST /api/auth/login` trả JWT
  - Sai mật khẩu trả 401
  - Response format thống nhất

#### LS-012: JWT filter + endpoint /me
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - Không token → 401
  - Token hợp lệ → `/api/me` trả user

#### LS-013: RBAC (ADMIN/STAFF/CUSTOMER)
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - Customer không gọi được `/api/admin/**`
  - Admin gọi được
  - Có seed role + admin user

#### LS-014: FE Login/Register + token storage + interceptor
- **Status**: TODO
- **Estimate**: L
- **Acceptance criteria**:
  - Login UI hoạt động
  - Token gắn vào header tự động
  - Logout clear token

---

### E3 — Catalog

#### LS-020: Category CRUD (admin) + list public
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - Admin CRUD: create/update/delete
  - Public list: `GET /api/categories`
  - Validate name not blank

#### LS-021: Product CRUD (admin)
- **Status**: TODO
- **Estimate**: L
- **Dependencies**: LS-020
- **Acceptance criteria**:
  - Create/update/delete product
  - Product có categoryId
  - Validate price >= 0

#### LS-022: Public product list/detail + pagination/search
- **Status**: TODO
- **Estimate**: L
- **Acceptance criteria**:
  - `GET /api/products?page&size` trả page
  - Filter keyword/category
  - `GET /api/products/{id}` trả detail

#### LS-023: FE public catalog UI (home/list/detail)
- **Status**: TODO
- **Estimate**: L
- **Acceptance criteria**:
  - Responsive
  - Search + pagination
  - Detail page

---

### E4 — Media Upload (Cloudinary)

#### LS-030: Upload image API (admin)
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - Upload trả về URL
  - Giới hạn loại file (jpg/png/webp) + size
  - Secrets lấy từ env

#### LS-031: Admin UI upload ảnh + gán vào product
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - Upload từ UI OK
  - Lưu `imageUrl` vào product

---

### E5 — Shopping (Cart/Checkout)

#### LS-040: Cart APIs (DB-based)
- **Status**: TODO
- **Estimate**: L
- **Acceptance criteria**:
  - `GET /api/cart`
  - `POST /api/cart/items`
  - `PUT /api/cart/items/{itemId}`
  - `DELETE /api/cart/items/{itemId}`

#### LS-041: Cart UI
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - Update quantity
  - Remove item
  - Total/subtotal hiển thị đúng

#### LS-042: Checkout tạo order từ cart
- **Status**: TODO
- **Estimate**: L
- **Acceptance criteria**:
  - `POST /api/orders` snapshot items
  - Clear cart
  - Status PENDING

---

### E6 — Orders

#### LS-050: Customer orders list/detail/cancel
- **Status**: TODO
- **Estimate**: L
- **Acceptance criteria**:
  - User chỉ thấy orders của mình
  - Cancel chỉ khi PENDING

#### LS-051: Admin/staff manage orders (list/filter/update status)
- **Status**: TODO
- **Estimate**: L
- **Acceptance criteria**:
  - RBAC đúng
  - Validate status flow

---

### E7 — Dashboard

#### LS-060: Dashboard API summary + revenue series + top products
- **Status**: TODO
- **Estimate**: L
- **Acceptance criteria**:
  - API trả KPI + series theo time range
  - Query ổn với seed

#### LS-061: Dashboard UI + charts
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - KPI cards
  - Revenue chart
  - Orders-by-status chart

---

### E8 — Hardening & Docs

#### LS-070: Unified error format + global exception handler
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - 400/401/403/404/500 format thống nhất
  - Không lộ stacktrace

#### LS-071: Frontend loading/error/empty states
- **Status**: TODO
- **Estimate**: M

#### LS-072: README + Postman collection
- **Status**: TODO
- **Estimate**: M

---

### E9 — Deploy-ready

#### LS-080: Build artifacts + Spring profiles dev/prod
- **Status**: TODO
- **Estimate**: M
- **Acceptance criteria**:
  - FE build OK
  - BE build OK
  - Env vars tách riêng

