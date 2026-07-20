*This project has been created as part of the 42 curriculum by luztorre, icatamba, ylima, adinis.*

# Karma Store

**Karma Store** is a full-stack e-commerce web application for Brazilian Jiu-Jitsu equipment — kimonos, belts, bags, and accessories — designed for the Angolan market.

---

## Description

Karma Store simulates a production-ready online store with a complete frontend, REST API backend, relational database, authentication system, and admin dashboard. The project covers the full lifecycle of an e-commerce platform: product discovery, shopping cart, checkout, payment confirmation, order tracking, and invoice delivery.

**Key Features:**

- Product catalog with search, filters (category, price, size, color, stock), sorting, and pagination
- Shopping cart with persistent state, size and color variant selection
- Full checkout flow with shipping address and Angolan payment methods (Multicaixa Express, Bank Transfer, Card)
- Order management: status updates, tracking code, payment proof upload
- Admin dashboard: products CRUD, orders, users, statistics charts, promotions, chat
- Advanced 3D interactive kimono viewer on the homepage (Three.js, custom GLSL shader, Draco compression)
- Professional invoice system: HTML email invoice generation and delivery, database persistence
- Authentication: email/password, Google OAuth 2.0, Two-Factor Authentication (OTP via email)
- User profile: avatar upload, order history, product reviews, GDPR data export/deletion
- Multi-language support: Portuguese, English, French (i18n with URL locale prefix)
- Real-time notifications (polling), chat widget (customer ↔ admin)
- Public REST API with API key authentication, rate limiting, and interactive documentation
- Privacy Policy, Terms of Service, and Returns Policy pages (dynamically editable by admin)
- HTTPS on all connections (self-signed SSL via nginx)

---

## Instructions

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and Docker Compose (required)
- Git

### Environment Setup

The project requires two `.env` files:

**1. Backend — `backend_karma_store/.env`** (copy from `backend_karma_store/.env.example`):

```bash
cp backend_karma_store/.env.example backend_karma_store/.env
```

Fill in the required values:

```env
DATABASE_URL="postgresql://user:password@postgres:5432/karma_store"
JWT_SECRET="your-secret-here"          # generate: openssl rand -base64 32
FRONTEND_URL="https://localhost:8443"
BACKEND_URL="https://localhost:8443"

# Google OAuth (create at console.cloud.google.com)
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI="https://localhost:8443/api/auth/google/callback"

# Cloudinary (image/3D model uploads — create at console.cloudinary.com)
CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=

# SMTP Email (Gmail App Password — myaccount.google.com/apppasswords)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=
SMTP_PASS=
SMTP_FROM="Karma Store <email@gmail.com>"
```

**2. Frontend — `frontend_karma_store/.env.local`** (copy from `frontend_karma_store/.env.local.example`):

```bash
cp frontend_karma_store/.env.local.example frontend_karma_store/.env.local
```

```env
NEXT_PUBLIC_API_URL=https://localhost:8443
BACKEND_URL=https://localhost:8443
```

### Run the Project

Start everything with a single command:

```bash
make up
```

This builds (if needed), starts all containers, and applies database migrations.

**Access the application:**
- HTTPS: `https://localhost:8443`
- HTTP redirect: `http://localhost:8080`

> The browser will show a certificate warning because the SSL certificate is self-signed. Click "Advanced" → "Proceed" to continue.

### Other Makefile Commands

```bash
make stop       # Stop all containers
make logs       # Stream logs from all services
make migrate    # Run database migrations manually
make clean      # Remove containers and volumes (full reset)
make db-shell   # Open a PostgreSQL shell
```

### Default Admin Account

After the first `make up`, create an admin user by registering normally at `/registro`, then update the role directly in the database:

```bash
make db-shell
UPDATE "User" SET role = 'admin' WHERE email = 'your@email.com';
```

---

## Team Information

The team has 4 members. With a 4-person team, some members hold multiple roles as permitted by the subject.

| Login | Role(s) | Responsibilities |
|-------|---------|-----------------|
| **luztorre** | Product Owner + Technical Lead + Developer | Overall project vision and architecture, site creation, backend API, auth system (email/password + Google OAuth + 2FA), invoice system, Docker/nginx setup, admin dashboard, general improvements and bug fixes |
| **ylima** | Project Manager + Developer | Interactive 3D kimono viewer (Three.js, shader), Google OAuth integration, console error fixes |
| **adinis** | Developer | Team coordination, full i18n translation (PT/EN/FR for all pages), Google OAuth frontend integration, console error fixes |
| **icatamba** | Developer | API documentation page (`/api-docs`), README (initial version), Terms of Service page, Return Policy page, Privacy Policy page |

---

## Project Management

### Work Organization

Work was divided by feature area and each team member was responsible for their assigned features end-to-end (frontend + backend when needed). The project was built iteratively — core features (auth, products, cart, orders) first, then modules and improvements.

### Communication

| Channel | Usage |
|---------|-------|
| WhatsApp | Daily communication, blockers, coordination |
| In-person | Weekly planning and integration sessions |
| GitHub | Version control, commit history per member |

### Tools

| Tool | Purpose |
|------|---------|
| GitHub | Version control, commit history |
| VS Code | Primary development environment |
| Docker | Local containerized development |
| Prisma Studio | Database inspection during development |

---

## Technical Stack

### Frontend

| Technology | Version | Purpose |
|-----------|---------|---------|
| Next.js | 15 | React framework with App Router, SSR, i18n middleware |
| React | 19 | UI component model |
| Tailwind CSS | 4 | Utility-first CSS, responsive design |
| Three.js / React Three Fiber | Latest | 3D kimono viewer |
| i18next / react-i18next | Latest | Multi-language (PT/EN/FR) |
| Recharts | Latest | Admin analytics charts |
| Framer Motion | Latest | Animations (rotating text, transitions) |
| Axios | Latest | HTTP client with interceptors |

### Backend

| Technology | Version | Purpose |
|-----------|---------|---------|
| Next.js API Routes | 15 | REST API endpoints (App Router) |
| Prisma ORM | Latest | Type-safe database access, migrations |
| Node.js | ≥ 18 | Runtime |
| jsonwebtoken | Latest | JWT generation and verification |
| bcryptjs | Latest | Password hashing (10 rounds + salt) |
| Nodemailer | Latest | SMTP email (invoices, 2FA OTP, GDPR confirmations) |
| Cloudinary SDK | Latest | Image and 3D model file storage |

### Database

| Technology | Justification |
|-----------|--------------|
| PostgreSQL 16 | Relational, ACID compliant — required for e-commerce order integrity |
| Prisma | Generates TypeScript types from schema, prevents SQL injection, handles migrations automatically |

### Infrastructure

| Technology | Purpose |
|-----------|---------|
| Docker + Docker Compose | Single-command containerized deployment (nginx + frontend + backend + postgres) |
| nginx | Reverse proxy, HTTPS termination (self-signed SSL), routes port 8443 → frontend/backend |

### Technical Choices — Justification

- **Next.js for both frontend and backend**: A single full-stack framework counts as both the frontend and backend module (Major, 2pts). It eliminates context-switching, allows sharing TypeScript types, and simplifies deployment.
- **Prisma over raw SQL**: Type-safe queries, auto-migration, and visual database browser accelerated development and prevented SQL injection bugs.
- **Self-signed SSL with nginx**: Meets the subject HTTPS requirement locally without external dependencies.
- **Polling (2s) over WebSockets for notifications**: Simpler to implement reliably across Docker networking; adequate for the notification frequency required (order updates, chat messages).

---

## Database Schema

### Entity-Relationship Overview

```
User ──┬── Order ────── OrderItem ──── Product
       │      └── Payment                │
       │      └── Invoice                │
       │                                 │
       ├── Review ───────────────────────┘
       ├── Session
       ├── OtpCode
       ├── PasswordReset
       ├── AccountDeletionToken
       ├── ApiKey
       ├── Notification
       └── ChatRoom ──── ChatMessage

SiteSettings (standalone key-value config)
RegistrationDraft (temporary signup drafts)
```

### Tables

**User** — Core authentication and profile

| Field | Type | Description |
|-------|------|-------------|
| id | String (cuid) | Primary key |
| email | String (unique) | Login identifier |
| password | String | bcrypt hash (10 rounds) |
| googleId | String? (unique) | Google OAuth provider ID |
| name | String | Full name |
| role | String | `user` or `admin` |
| twoFactorEnabled | Boolean | 2FA toggle |
| avatar | String? | Cloudinary image URL |
| phone, address, city, zipCode, country | String? | Profile/shipping info |

**Product** — Catalog items

| Field | Type | Description |
|-------|------|-------------|
| id | String (cuid) | Primary key |
| slug | String (unique) | SEO URL identifier — indexed |
| name, nameEn, nameFr | String | i18n product names |
| description, descriptionEn, descriptionFr | String? | i18n descriptions |
| price | Float | Base price in AKZ (Kwanza) |
| salePrice | Float? | Promotional price |
| category | String | Category — indexed |
| sizes, colors, images, tags | String[] | Variant arrays |
| featured, inStock | Boolean | Display/availability flags |
| quantity | Int | Stock count |

**Order** — Customer orders

| Field | Type | Description |
|-------|------|-------------|
| id | String (cuid) | Primary key |
| orderNumber | Int (autoincrement) | Sequential display number |
| userId | String (FK→User) | Owner — indexed |
| status | String | `pending` → `confirmed` → `shipped` → `delivered` → `cancelled` — indexed |
| totalPrice | Float | Order total in AKZ |
| shippingAddress | String (JSON) | Serialized address object |
| trackingCode | String? | Carrier tracking number |

**OrderItem** — Line items per order

| Field | Type | Description |
|-------|------|-------------|
| orderId | String (FK→Order) | |
| productId | String (FK→Product) | |
| quantity | Int | Units ordered |
| price | Float | Price snapshot at time of purchase |
| size, color | String? | Selected variant |

**Payment** — Payment record per order

| Field | Type | Description |
|-------|------|-------------|
| orderId | String (FK→Order, unique) | One-to-one with Order |
| method | String | `multicaixa_express`, `transferencia`, `multicaixa_referencia`, `cartao` |
| status | String | `pending`, `confirmed`, `failed` |
| proofImageUrl | String? | Uploaded payment proof (Cloudinary) |
| referenceCode, phoneNumber, bankName | String? | Method-specific fields |

**Invoice** — Professional invoice records

| Field | Type | Description |
|-------|------|-------------|
| orderId | String (FK→Order, unique) | One-to-one with Order |
| userId | String (FK→User) | |
| invoiceNumber | String | Sequential formatted number |
| items | Json | Line items snapshot |
| discount, shippingFee, taxRate | Float? | Financial fields |
| status | String | `sent`, `draft` |
| sentAt | DateTime | Email delivery timestamp |
| paymentTerms, dueDate | String?/DateTime? | Payment conditions |

**Session** — Per-device login sessions

| Field | Type | Description |
|-------|------|-------------|
| userId | String (FK→User) | |
| sessionToken | String (unique) | Embedded in JWT payload |
| userAgent, ipAddress | String? | Device fingerprint |
| expiresAt | DateTime | Session expiry |
| isActive | Boolean | Can be revoked individually |

**OtpCode** — 2FA one-time passwords

| Field | Type | Description |
|-------|------|-------------|
| userId | String (FK→User) | |
| code | String | 6-digit OTP |
| expiresAt | DateTime | Short-lived (5 min) |
| used | Boolean | Prevents replay attacks |

**Notification** — User notifications

| Field | Type | Description |
|-------|------|-------------|
| userId | String (FK→User) | |
| type | String | `order_update`, `chat`, `promotion` |
| read | Boolean | Read state |
| link | String? | Deep link to relevant page |

**ChatRoom / ChatMessage** — Customer support chat

| Field | Type | Description |
|-------|------|-------------|
| ChatRoom.userId | String (FK→User) | One room per customer |
| ChatRoom.status | String | `open`, `closed` |
| ChatMessage.isAdmin | Boolean | Distinguishes sender side |
| ChatMessage.fileUrl, fileType | String? | File attachment support |

**ApiKey** — Public API authentication

| Field | Type | Description |
|-------|------|-------------|
| keyHash | String (unique) | SHA-256 hash of the actual key |
| keyPrefix | String | First 8 chars for display (`ks_XXXXXX…`) |
| userId | String (FK→User) | Key owner (admin) |
| isActive | Boolean | Revocation flag |

---

## Features List

| Feature | Description | Implemented by |
|---------|-------------|---------------|
| User Registration & Login | Email/password auth with bcrypt + JWT sessions | luztorre, adinis |
| Google OAuth 2.0 | Sign in with Google (custom backend flow, no library) | luztorre, ylima, adinis |
| Two-Factor Authentication | OTP sent by email on login, verify before session creation | luztorre |
| Role-based Access Control | `user` and `admin` roles — protected routes frontend + backend | luztorre |
| Product Catalog | Browse all products with images, price, sizes, colors, stock | luztorre |
| Advanced Search & Filters | Full-text search, category/price/size/color/stock filters, sorting, pagination | luztorre |
| Shopping Cart | Persistent cart, size/color variants, quantity controls | luztorre |
| Checkout Flow | Multi-step: address → payment method → confirm | luztorre |
| Angolan Payment Methods | Multicaixa Express, Bank Transfer, Card with payment proof upload | luztorre |
| Order Management | Create, track, update status, upload payment proof | luztorre |
| Product Reviews | Authenticated users rate (1-5) and comment — modal with product image | luztorre |
| Admin Dashboard | Back-office for products, orders, users, stats, chat, promotions | luztorre |
| Analytics Dashboard | Revenue, daily sales, top products charts (Recharts), CSV export | luztorre |
| Invoice System | HTML email invoice (Fatura Pró-Forma), DB persistence, resend — Angolan format (AKZ) | luztorre |
| User Profile | Avatar upload, personal info, order history, active sessions, stats | luztorre |
| GDPR Compliance | Export personal data (JSON), delete account (with cascade), confirmation emails | luztorre |
| Notification System | In-app notifications (order updates, chat, promotions), badge count, mark as read | luztorre |
| Chat Widget | Customer ↔ Admin real-time chat, file/image attachments, unread badges | luztorre |
| Public REST API | API key auth, rate limiting (100 req/min), 5+ endpoints (GET/POST/PUT/DELETE) | luztorre |
| API Documentation | Interactive `/api-docs` page with endpoint descriptions and examples | icatamba |
| 3D Kimono Viewer | Interactive Three.js viewer — OrbitControls, Draco decompression, color shader | ylima |
| Kimono Color Customization | Custom GLSL shader tints kimono by luminance, 6 colors, real-time update | ylima |
| Multi-language (PT/EN/FR) | Complete i18n for all pages, URL locale prefix, language switcher | adinis |
| Privacy Policy | Dynamic page, content editable by admin | icatamba |
| Terms of Service | Dynamic page, content editable by admin | icatamba |
| Returns Policy | Dynamic page, content editable by admin | icatamba |
| File Upload System | Profile avatar, product images, 3D model, chat files — Cloudinary with type/size validation | luztorre |
| HTTPS | Self-signed SSL via nginx, all connections encrypted | luztorre |
| Console Error Cleanup | Suppressed THREE.js warnings, fixed auth race conditions, fixed WebGL context lost | luztorre, ylima, adinis |

---

## Modules

**Total validated points: 20** (14 mandatory + 6 bonus — score capped at 125 by subject rules)

### Major Modules (2 pts each)

---

**1. Web — Framework (Next.js for frontend and backend) — 2 pts**

The subject states that full-stack frameworks (Next.js, Nuxt.js, SvelteKit) count as both the frontend and backend framework module if both their frontend and backend capabilities are used.

- **Frontend**: Next.js 15 App Router with React 19 server and client components, Tailwind CSS, i18n middleware
- **Backend**: Next.js API Routes for all REST endpoints, middleware for auth and rate limiting
- **Contributors**: luztorre (architecture, backend), adinis (i18n, frontend), ylima (3D, frontend), icatamba (documentation pages)

---

**2. User Management — Advanced Permissions System — 2 pts**

- **Justification**: E-commerce requires strict separation between customer actions and business administration.
- **Implementation**: `User.role` field (`user` | `admin`). Server-side middleware rejects non-admin requests to `/api/admin/*` and `/api/users/:id` management endpoints. Frontend hides admin UI and guards `/admin/*` routes. Admin panel at `/admin/usuarios` provides full CRUD: view all users, change roles, suspend, and delete accounts with cascade.
- **Contributors**: luztorre

---

**3. Web — Public API — 2 pts**

- **Justification**: Allows third-party integrations (BJJ gyms, affiliate sites) to query and manage the product catalog.
- **Implementation**: Endpoints under `/api/public/products` (GET list, GET by id, POST, PUT, DELETE). All requests require `x-api-key` header validated against SHA-256 hashes stored in the `ApiKey` table. Rate limiting: 100 requests/minute per key, returns `429` on exceeded with `X-RateLimit-*` headers. Interactive documentation at `/api-docs`.
- **Contributors**: luztorre (endpoints and security), icatamba (docs page)

---

**4. Data & Analytics — Advanced Statistics Dashboard — 2 pts**

- **Justification**: Provides administrators with business intelligence for inventory and sales decisions.
- **Implementation**: `GET /api/admin/stats` aggregates total revenue, orders by status, new users, and top products. `GET /api/admin/stats/sales?period=30d` returns daily grouped sales data. Frontend renders interactive line charts (revenue over time), bar charts (top products), and metric cards using Recharts. Full data export to CSV.
- **Contributors**: luztorre

---

**5. Gaming & User Experience — Advanced 3D Graphics (Three.js) — 2 pts**

The subject lists "Implement advanced 3D graphics using a library like Three.js or Babylon.js" as a **Major** module (IV.6), worth 2 points.

- **Justification**: The kimono is the core product. An interactive 3D viewer creates an immersive product experience that differentiates the store from standard e-commerce.
- **Implementation**:
  - React Three Fiber (`Canvas`, `useGLTF`, `OrbitControls`) for declarative Three.js integration
  - Custom GLSL shader using `onBeforeCompile` to tint the kimono fabric by luminance (values above 0.5), leaving skin and belt unchanged — updates via WebGL uniform without shader recompilation
  - Draco decoder (local, `/public/draco/`) compresses the model from 19 MB to 1.4 MB
  - Model served via Cloudinary CDN
  - OrbitControls with angle and distance limits, auto-rotate
  - Responsive: `clamp()`-based height, collapses to 1 column on mobile
  - THREE.js internal warnings (Context Lost, Clock deprecation) suppressed via patched `console.*`
- **Contributors**: ylima

---

**6. Module of Choice — Professional Invoice System — 2 pts**

This module is claimed under "Module of choice (Major)" from section IV.10 of the subject.

- **Why this module**: E-commerce in Angola requires professional invoice delivery. Customers need legal proof of purchase; administrators need invoice records for accounting. No existing module in the subject covers this.
- **Technical complexity addressed**:
  - Custom HTML template engine (600+ lines) rendering a complete *Fatura Pró-Forma* with: company header, customer info block, line-items table with per-item discount percentages, VAT summary table, bank details, total in words (*Total por Extenso*), payment method labels (Multicaixa Express, Referência Multicaixa, Transferência Bancária)
  - Angolan business format compliance: AKZ (Kwanza) currency formatting, `pt-AO` locale, IVA table, ORIGINAL stamp
  - Complete lifecycle: `POST /api/email/send-invoice` (generate + email + persist) → `GET /api/invoices` (history) → `POST /api/orders/resend-invoice` (admin resend, role-protected)
  - `Invoice` model in database with full financial fields (discount, shippingFee, taxRate, dueDate, paymentTerms, status, sentAt)
  - Admin invoice modal with custom fields (notes, payment terms, due date)
  - Customer invoice view in `/perfil → Meus Pedidos`
- **Value added**: Enables legal and professional customer communication specific to the Angolan market — not achievable by combining existing modules.
- **Contributors**: luztorre

---

### Minor Modules (1 pt each)

---

**7. Web — ORM (Prisma) — 1 pt**

- **Implementation**: Prisma Client with PostgreSQL. Schema in `backend_karma_store/prisma/schema.prisma` defines 15 models with typed relations, cascading deletes, and database indexes on `Product.slug`, `Product.category`, `Order.userId`, `Order.status`. Migrations via `npx prisma migrate deploy`.
- **Contributors**: luztorre

---

**8. Web — File Upload and Management — 1 pt**

- **Implementation**: Client-side validation (type and size) before upload. Server validates file buffer before sending to Cloudinary. Supports: profile avatars (JPEG/PNG/WEBP, max 5 MB), product images (multiple), 3D model `.glb`, chat attachments (images, video, PDF, documents). Preview before confirmation. Progress indicator. Delete with confirmation. Secure access via Cloudinary signed URLs.
- **Contributors**: luztorre

---

**9. Web — Advanced Search — 1 pt**

- **Implementation**: Full-text search on product name and description with debouncing. Sidebar filters: category, price range (min/max), sizes, colors, in-stock toggle. Sort: newest, price ascending, price descending, highest rated. Pagination with page numbers and previous/next. All filters reflected in URL query parameters for shareable links. Skeleton loaders during fetch.
- **Contributors**: luztorre

---

**10. Web — Notification System — 1 pt**

- **Implementation**: `Notification` model stores all notifications with `type`, `read` flag, and optional deep link. The frontend polls `GET /api/notifications` every 2 seconds. A bell icon in the navbar shows an unread count badge. Users can mark individual notifications as read or all at once. Triggers: new order, order status change, new chat message, new promotion.
- **Contributors**: luztorre

---

**11. User Management — OAuth 2.0 (Google) — 1 pt**

- **Implementation**: Custom OAuth 2.0 flow without third-party auth libraries. `GET /api/auth/google` redirects to Google consent screen. `GET /api/auth/google/callback` exchanges the authorization code for tokens, fetches the Google user profile, and either creates a new account or associates the Google ID with an existing account (by matching email). Issues a JWT session. Frontend `/auth/google/callback` page receives the token and stores the session. "Sign in with Google" buttons on `/login` and `/registro`.
- **Contributors**: luztorre (backend flow), ylima (3D page Google button), adinis (login/registro Google button)

---

**12. User Management — Two-Factor Authentication (2FA) — 1 pt**

- **Implementation**: Optional per-user 2FA toggle in `/perfil`. On login, if 2FA is enabled, a 6-digit OTP is generated, stored as a hashed `OtpCode` record (5-minute expiry), and sent by email. The user is redirected to a verification page. `POST /api/auth/otp/verify` validates the code and issues the session JWT. `POST /api/auth/otp/resend` regenerates and resends the code. Replay prevention via `used` flag.
- **Contributors**: luztorre

---

**13. Data & Analytics — GDPR Compliance — 1 pt**

- **Implementation**: `GET /api/users/me/data` exports all personal data (profile, sessions, orders, reviews, notifications, invoices) as a downloadable JSON. `DELETE /api/users/me` sends a confirmation email with a time-limited token; `POST /api/users/me/confirm-deletion` validates the token and permanently deletes the account with full cascade (orders, reviews, sessions, notifications). Both actions trigger confirmation emails via SMTP.
- **Contributors**: luztorre

---

**14. Accessibility & Internationalization — Multiple Languages (PT/EN/FR) — 1 pt**

- **Implementation**: `i18next` and `react-i18next`. Translation bundles in `src/i18n/locales/pt.json`, `en.json`, `fr.json` covering all UI text. Next.js middleware in `src/middleware.ts` auto-prefixes URLs (`/pt/`, `/en/`, `/fr/`) and detects the browser's preferred language on first visit. Language switcher in the navbar. Products have multilingual fields (`nameEn`, `nameFr`, `descriptionEn`, `descriptionFr`) served by the backend.
- **Contributors**: adinis (all translations), luztorre (i18n setup, middleware)

---

**Point Summary**

| Module | Type | Points |
|--------|------|--------|
| Web — Next.js Framework (frontend + backend) | Major | 2 |
| User Management — Advanced Permissions | Major | 2 |
| Web — Public API | Major | 2 |
| Data & Analytics — Statistics Dashboard | Major | 2 |
| Gaming — Advanced 3D Graphics (Three.js) | Major | 2 |
| Module of Choice — Invoice System | Major | 2 |
| Web — ORM (Prisma) | Minor | 1 |
| Web — File Upload | Minor | 1 |
| Web — Advanced Search | Minor | 1 |
| Web — Notification System | Minor | 1 |
| User Management — OAuth 2.0 | Minor | 1 |
| User Management — 2FA | Minor | 1 |
| Data & Analytics — GDPR | Minor | 1 |
| Accessibility — Multiple Languages | Minor | 1 |
| **TOTAL** | | **20** |

> 14 mandatory points + 6 bonus. Subject caps bonus at 5, giving a maximum score of **125** on the 42 scale.

---

## Individual Contributions

### luztorre — Product Owner + Technical Lead + Developer

**Worked on:**
- Full project architecture, folder structure, and API design conventions
- All backend API modules: auth (email/password, Google OAuth, 2FA), products, orders, payments, reviews, chat, invoices, notifications, GDPR, admin stats, public API, file uploads, email (SMTP), store settings, legal content
- Google OAuth backend flow (custom, no library): `/api/auth/google`, `/api/auth/google/callback`
- Two-Factor Authentication: OTP generation, email delivery, verification endpoint, resend
- Professional invoice system: HTML template engine (600+ lines), Fatura Pró-Forma, email delivery, database persistence, admin modal, customer view
- Docker Compose orchestration (nginx + frontend + backend + postgres), nginx configuration, self-signed SSL
- Admin dashboard (all sections), user profile page, checkout flow, order tracking
- Auth race condition fix (isLoading guard preventing redirect on page reload)
- Console error suppression (THREE.js, WebGL context loss, autofill intercept on checkout fields)
- Project evaluation, module scoring review, README update

**Challenges:**
The auth race condition on page reload (isAuthenticated briefly false while session restores from storage) caused protected pages to redirect to login. Solved by consuming an isLoading state from AuthContext and guarding redirects until loading completes. The invoice HTML template required careful Angolan business format research (IVA table, "Quadro Resumo", Fatura Pró-Forma conventions) to produce a legally recognizable document.

---

### ylima — Project Manager + Developer

**Worked on:**
- Interactive 3D kimono viewer: Three.js model loading, OrbitControls, auto-rotate, responsive layout
- Custom GLSL shader for real-time kimono color customization (luminance-based tinting, WebGL uniform update)
- Draco decoder integration for model compression (19 MB → 1.4 MB)
- Console error suppression for THREE.js internal warnings
- Google OAuth integration on frontend

**Challenges:**
Writing a custom GLSL shader that selectively tints only the kimono fabric (based on luminance) without affecting skin tones or the belt color required understanding how Three.js's `onBeforeCompile` hook works and injecting uniform declarations at the correct point in the shader pipeline. Getting the Draco decoder to work locally (instead of from a CDN) also required careful configuration of the `DRACOLoader` path in the public directory.

---

### adinis — Developer

**Worked on:**
- Complete UI translation into Portuguese, English, and French for all application pages
- Added Google OAuth login/register buttons on frontend pages
- Console error cleanup across multiple pages
- Team coordination and communication management

**Challenges:**
Translating the entire application required systematically auditing every user-facing string across 20+ pages and ensuring the translation keys remained consistent across three locale files. Some UI elements required context-dependent translations that differed between the three languages.

---

### icatamba — Developer

**Worked on:**
- Interactive API documentation page at `/api-docs` with endpoint descriptions and usage examples
- Initial README structure
- Privacy Policy page (`/privacidade`) — dynamic content loaded from backend, editable by admin
- Terms of Service page (`/termos`) — dynamic content loaded from backend, editable by admin
- Returns Policy page (`/devolucoes`) — dynamic content loaded from backend

**Challenges:**
Building the API documentation page required understanding the full API surface and presenting it clearly for third-party developers, including example requests and response formats. Ensuring the legal content pages loaded dynamically from the backend (rather than being hardcoded) required integrating with the SiteSettings API.

---

## Public API

Karma Store exposes a public REST API for third-party integrations.

**Interactive docs:** `https://localhost:8443/api-docs`

### Authentication

All requests require an `x-api-key` header. Admin write operations additionally require a bearer JWT.

```
x-api-key: ks_your_key_here
Authorization: Bearer <admin_jwt>   # POST, PUT, DELETE only
```

API keys are created by an admin at **Admin → Settings → API Keys**.

### Rate Limiting

100 requests per minute per API key. Exceeding returns `429 Too Many Requests` with `X-RateLimit-Limit`, `X-RateLimit-Remaining`, and `X-RateLimit-Reset` headers.

### Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/public/products` | List products (supports filters, pagination) |
| `GET` | `/api/public/products/:id` | Single product with reviews |
| `POST` | `/api/public/products` | Create a product (admin JWT required) |
| `PUT` | `/api/public/products/:id` | Update a product (admin JWT required) |
| `DELETE` | `/api/public/products/:id` | Delete a product (admin JWT required) |

### GET /api/public/products — Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `category` | string | Filter by category (`kimonos`, `faixas`, `bags`…) |
| `inStock` | boolean | `true` returns only in-stock products |
| `featured` | boolean | `true` returns only featured products |
| `search` | string | Full-text search on name and description |
| `page` | number | Page number (default: 1) |
| `limit` | number | Items per page, max 100 (default: 20) |

### Response Format

```json
{ "success": true, "data": { ... } }
{ "success": false, "error": "Error message" }
```

### Quick Example

```bash
curl "https://localhost:8443/api/public/products?category=kimonos&inStock=true" \
  -H "x-api-key: ks_your_key_here"
```

---

## Resources

### Documentation & References

- [Next.js Documentation](https://nextjs.org/docs) — App Router, API Routes, middleware
- [Prisma Documentation](https://www.prisma.io/docs) — ORM, schema, migrations
- [Tailwind CSS](https://tailwindcss.com/docs) — Utility-first CSS
- [Three.js Documentation](https://threejs.org/docs) — 3D rendering
- [React Three Fiber](https://docs.pmnd.rs/react-three-fiber) — React declarative Three.js
- [i18next Documentation](https://www.i18next.com) — Internationalization
- [Cloudinary Documentation](https://cloudinary.com/documentation) — File and media upload
- [PostgreSQL Documentation](https://www.postgresql.org/docs/) — Relational database
- [Docker Compose Documentation](https://docs.docker.com/compose/) — Container orchestration
- [GLSL Shader Reference](https://www.khronos.org/opengl/wiki/OpenGL_Shading_Language) — Custom kimono shader
- [Draco 3D Compression](https://google.github.io/draco/) — 3D model compression

### AI Usage

AI tools (Claude) were used to assist in the following parts of this project:

- **Debugging assistance**: Diagnosing auth race conditions (isLoading state), Chrome autofill intercepting keyboard input on checkout fields, nginx COPY path issues after project reorganization
- **Console error suppression**: Identifying the correct console levels (log/warn/error) for THREE.js message suppression
- **GLSL shader development**: Understanding `onBeforeCompile` hook structure and luminance-based tinting logic for the kimono color customization
- **Invoice HTML template**: Structure of the Fatura Pró-Forma layout, Angolan business format conventions
- **Code refactoring**: Switching checkout onChange handlers to functional setState to prevent stale closures, adding autocomplete attributes to form fields for DevTools compliance
- **Project evaluation**: Cross-referencing the subject module list against implemented features to identify correct point claims (identifying that 3D is a Major not Minor module, that Game Customization requires a game, etc.)
- **README writing**: Structure, English writing, completeness check against subject requirements

All AI-generated content was reviewed, tested, and understood by the team before integration. No code was copied without full comprehension of its behavior.
