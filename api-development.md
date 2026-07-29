

## Mục lục

1. [API & REST API](#1-api--rest-api)
2. [Request - Response](#2-request---response)
3. [API Authentication](#3-api-authentication)
4. [API Document](#4-api-document)
5. [API Tool & Template](#5-api-tool--template)

---

## 1. API & REST API

### 1.1. API overview

**API (Application Programming Interface)** là một tập hợp các quy tắc và giao thức cho phép hai phần mềm giao tiếp với nhau. Có thể hình dung API giống như một "người phục vụ" trong nhà hàng:

- Bạn (client/ứng dụng) muốn gọi món (yêu cầu dữ liệu/chức năng)
- Người phục vụ (API) mang yêu cầu đó vào bếp (server)
- Bếp (server/database) chế biến và trả món ăn (dữ liệu) ra cho bạn thông qua người phục vụ

Bạn không cần biết bếp nấu như thế nào, chỉ cần biết cách gọi món qua người phục vụ — đó chính là bản chất của API: **che giấu độ phức tạp bên trong, chỉ expose ra những gì cần thiết**.

### 1.2. Các loại kiến trúc API phổ biến

| Kiến trúc | Đặc điểm chính | Khi nào dùng |
|---|---|---|
| **REST** | Dựa trên HTTP, dùng URL + method, phổ biến nhất | Ứng dụng web/mobile thông thường |
| **GraphQL** | Client tự định nghĩa dữ liệu cần lấy trong 1 request | Dữ liệu phức tạp, nhiều quan hệ, cần tối ưu số lượng request |
| **gRPC** | Dùng Protocol Buffers, tốc độ cao, hai chiều (streaming) | Giao tiếp giữa các microservice nội bộ |
| **SOAP** | Dùng XML, có chuẩn bảo mật/transaction chặt chẽ | Hệ thống ngân hàng, doanh nghiệp lớn, legacy system |
| **WebSocket** | Kết nối 2 chiều liên tục (real-time) | Chat, thông báo real-time, game online |

### 1.3. REST API là gì?

**REST (Representational State Transfer)** là một **kiểu kiến trúc** (không phải giao thức) do Roy Fielding đề xuất năm 2000, dựa trên giao thức HTTP.

**6 nguyên tắc của REST:**

1. **Client-Server**: Tách biệt giao diện (client) và xử lý dữ liệu (server)
2. **Stateless**: Mỗi request phải chứa đầy đủ thông tin cần thiết, server không lưu trạng thái của client giữa các request
3. **Cacheable**: Response cần chỉ rõ có thể cache được hay không, giúp tăng hiệu năng
4. **Uniform Interface**: Giao diện thống nhất — dùng URL để định danh resource, dùng HTTP method để thao tác
5. **Layered System**: Có thể có nhiều lớp trung gian (proxy, load balancer...) mà client không cần biết
6. **Code on Demand** (tùy chọn): Server có thể gửi code (vd: JavaScript) để client thực thi

### 1.4. Resource & URL trong REST

Trong REST, mọi thứ đều là **resource** (tài nguyên), được định danh bằng URL:

```
GET    /users          → Lấy danh sách người dùng
GET    /users/123      → Lấy thông tin người dùng có id = 123
POST   /users          → Tạo mới một người dùng
PUT    /users/123      → Cập nhật toàn bộ user 123
PATCH  /users/123      → Cập nhật một phần user 123
DELETE /users/123      → Xóa user 123
```

**Quy tắc đặt tên URL tốt:**
- Dùng danh từ số nhiều: `/users` thay vì `/getUsers` hay `/user`
- Thể hiện quan hệ phân cấp: `/users/123/orders` (đơn hàng của user 123)
- Không dùng động từ trong URL (hành động thể hiện qua HTTP method)
- Dùng dấu gạch ngang `-` thay vì gạch dưới `_` hoặc camelCase

### 1.5. HTTP Methods (Verbs)

| Method | Ý nghĩa | Idempotent* | Có body không |
|---|---|---|---|
| GET | Lấy dữ liệu | ✅ | Không |
| POST | Tạo mới | ❌ | Có |
| PUT | Cập nhật toàn bộ (thay thế) | ✅ | Có |
| PATCH | Cập nhật một phần | ❌ (thường) | Có |
| DELETE | Xóa | ✅ | Thường không |

> *Idempotent: gọi nhiều lần cho kết quả giống như gọi 1 lần (không gây thêm side-effect).

---

## 2. Request - Response

### 2.1. Cấu trúc một HTTP Request

Một request gồm 4 phần chính:

```
POST /api/v1/users HTTP/1.1          ← Request line (method, path, version)
Host: api.example.com                 ← 
Content-Type: application/json        ← Headers
Authorization: Bearer eyJhbGciOi...   ←

{                                      ← Body (dữ liệu gửi đi)
  "name": "Nguyen Van A",
  "email": "a@example.com"
}
```

**Các thành phần cụ thể:**

- **Method**: GET, POST, PUT, PATCH, DELETE...
- **URL/Endpoint**: đường dẫn đến resource, có thể kèm:
  - **Path parameter**: `/users/{id}` → `/users/123`
  - **Query parameter**: `/users?page=2&limit=10&sort=name`
- **Headers**: metadata đi kèm request, ví dụ:
  - `Content-Type`: định dạng dữ liệu gửi (application/json, multipart/form-data...)
  - `Authorization`: thông tin xác thực
  - `Accept`: định dạng dữ liệu mong muốn nhận về
- **Body**: dữ liệu gửi kèm (thường dùng với POST, PUT, PATCH)

### 2.2. Cấu trúc một HTTP Response

```
HTTP/1.1 200 OK                       ← Status line
Content-Type: application/json        ← Headers
Date: Tue, 28 Jul 2026 10:00:00 GMT   ←

{                                      ← Body
  "id": 123,
  "name": "Nguyen Van A",
  "email": "a@example.com",
  "created_at": "2026-07-28T10:00:00Z"
}
```

### 2.3. HTTP Status Code — Nhóm mã trạng thái

| Nhóm | Ý nghĩa | Mã thường gặp |
|---|---|---|
| **1xx** | Thông tin (informational) | 100 Continue |
| **2xx** | Thành công | 200 OK, 201 Created, 204 No Content |
| **3xx** | Chuyển hướng (redirection) | 301 Moved Permanently, 304 Not Modified |
| **4xx** | Lỗi từ phía client | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 409 Conflict, 422 Unprocessable Entity, 429 Too Many Requests |
| **5xx** | Lỗi từ phía server | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable |

**Bảng chi tiết các mã hay gặp nhất:**

- `200 OK` — Request thành công, có trả dữ liệu
- `201 Created` — Tạo mới thành công (dùng cho POST)
- `204 No Content` — Thành công nhưng không có dữ liệu trả về (dùng cho DELETE)
- `400 Bad Request` — Dữ liệu gửi lên sai định dạng/thiếu field
- `401 Unauthorized` — Chưa xác thực (chưa đăng nhập / token sai)
- `403 Forbidden` — Đã xác thực nhưng không có quyền truy cập
- `404 Not Found` — Không tìm thấy resource
- `409 Conflict` — Xung đột dữ liệu (vd: email đã tồn tại)
- `422 Unprocessable Entity` — Dữ liệu đúng định dạng nhưng sai logic validate
- `429 Too Many Requests` — Gọi API quá nhiều lần (rate limit)
- `500 Internal Server Error` — Lỗi phía server, không xác định nguyên nhân cụ thể

### 2.4. Cấu trúc Response Body chuẩn (best practice)

Một format response nhất quán giúp frontend dễ xử lý:

```json
// Thành công
{
  "success": true,
  "data": {
    "id": 123,
    "name": "Nguyen Van A"
  },
  "message": "Lấy thông tin thành công"
}

// Thất bại
{
  "success": false,
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "Không tìm thấy người dùng với id = 123"
  }
}

// Danh sách có phân trang
{
  "success": true,
  "data": [ /* mảng items */ ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 100,
    "total_pages": 10
  }
}
```

---

## 3. API Authentication

Authentication (xác thực) trả lời câu hỏi **"Bạn là ai?"**, còn Authorization (phân quyền) trả lời **"Bạn được làm gì?"**. Dưới đây là các phương pháp phổ biến nhất.

### 3.1. API Key

- Server cấp cho client một chuỗi ký tự bí mật (key)
- Client gửi kèm key trong mỗi request (thường qua header hoặc query param)

```
GET /api/data?api_key=abc123xyz
```
hoặc
```
X-API-Key: abc123xyz
```

**Ưu điểm:** Đơn giản, dễ triển khai
**Nhược điểm:** Không định danh được user cụ thể, nếu lộ key thì nguy hiểm, khó thu hồi theo phạm vi

### 3.2. Basic Authentication

Gửi `username:password` được mã hóa Base64 trong header:

```
Authorization: Basic bmd1eWVuOjEyMzQ1Ng==
```

**Nhược điểm:** Base64 không phải mã hóa (dễ giải mã ngược), **bắt buộc phải dùng HTTPS**.

### 3.3. Token-based Authentication (Bearer Token / JWT)

Đây là phương pháp phổ biến nhất hiện nay.

**Luồng hoạt động:**

1. User đăng nhập (gửi username/password)
2. Server xác thực, trả về một **token** (thường là JWT)
3. Client lưu token, gửi kèm trong header ở các request tiếp theo:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**JWT (JSON Web Token)** gồm 3 phần, ngăn cách bởi dấu chấm: `header.payload.signature`

- **Header**: thuật toán mã hóa dùng để ký (vd: HS256)
- **Payload**: dữ liệu (user id, quyền, thời gian hết hạn...)
- **Signature**: chữ ký để xác minh token không bị giả mạo

**Access Token vs Refresh Token:**
- **Access token**: thời gian sống ngắn (15 phút - 1 giờ), dùng để gọi API
- **Refresh token**: thời gian sống dài (vài ngày - vài tháng), dùng để lấy access token mới khi hết hạn mà không cần đăng nhập lại

### 3.4. OAuth 2.0

Chuẩn cho phép ứng dụng bên thứ ba truy cập tài nguyên **thay mặt người dùng** mà không cần biết mật khẩu (vd: "Đăng nhập bằng Google/Facebook").

**Các thành phần:**
- **Resource Owner**: người dùng
- **Client**: ứng dụng muốn truy cập
- **Authorization Server**: server cấp quyền (vd: Google)
- **Resource Server**: server chứa dữ liệu (vd: Google API)

**Luồng phổ biến (Authorization Code Flow):**
1. Client chuyển hướng user đến trang đăng nhập của Google
2. User đồng ý cấp quyền
3. Google trả về **authorization code**
4. Client dùng code đó đổi lấy **access token** từ Google
5. Client dùng access token gọi Google API

### 3.5. Session-based Authentication

- Server lưu trạng thái đăng nhập (session) trong bộ nhớ/database
- Client lưu **session ID** trong cookie
- Mỗi request, server tra session ID để biết ai đang gọi

**Khác biệt với Token-based:** Session cần server lưu trạng thái (stateful), Token thường stateless — phù hợp hơn cho hệ thống phân tán/microservice.

### 3.6. So sánh nhanh các phương pháp

| Phương pháp | Độ phức tạp | Phù hợp với |
|---|---|---|
| API Key | Thấp | API nội bộ, server-to-server |
| Basic Auth | Thấp | Prototype, nội bộ, ít dùng thực tế |
| JWT/Token | Trung bình | Ứng dụng web/mobile hiện đại, SPA |
| OAuth 2.0 | Cao | Đăng nhập qua bên thứ 3, API public lớn |
| Session | Trung bình | Ứng dụng web truyền thống (server-rendered) |

---

## 4. API Document

Tài liệu API (API Documentation) giúp người khác (frontend dev, đối tác, dev khác trong team) hiểu và sử dụng API mà không cần đọc code.

### 4.1. Một tài liệu API tốt cần có gì?

- **Endpoint**: URL đầy đủ + HTTP method
- **Mô tả chức năng**: endpoint này dùng để làm gì
- **Authentication**: cần token/key gì không
- **Request parameters**: path param, query param, headers, body — kèm kiểu dữ liệu, bắt buộc hay không
- **Response mẫu**: ví dụ response thành công và các trường hợp lỗi
- **Status code có thể trả về**
- **Ví dụ gọi thực tế** (curl, code mẫu)

### 4.2. Ví dụ một mục document chuẩn

```markdown
## Lấy thông tin người dùng

`GET /api/v1/users/{id}`

### Mô tả
Trả về thông tin chi tiết của một người dùng theo ID.

### Authentication
Yêu cầu Bearer Token.

### Path Parameters
| Tên | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| id | integer | Có | ID của người dùng |

### Response 200 OK
​```json
{
  "id": 123,
  "name": "Nguyen Van A",
  "email": "a@example.com"
}
​```

### Response lỗi
| Status | Mô tả |
|---|---|
| 401 | Chưa xác thực |
| 404 | Không tìm thấy user |

### Ví dụ (cURL)
​```bash
curl -X GET https://api.example.com/v1/users/123 \
  -H "Authorization: Bearer <token>"
​```
```

### 4.3. Chuẩn OpenAPI (Swagger)

**OpenAPI Specification** là chuẩn phổ biến nhất để mô tả REST API dưới dạng file YAML/JSON có cấu trúc, giúp:

- Tự động sinh ra giao diện tài liệu tương tác (Swagger UI)
- Tự động sinh code client/server (code generation)
- Validate request/response tự động

Ví dụ một đoạn OpenAPI (YAML) đơn giản:

```yaml
openapi: 3.0.0
info:
  title: User API
  version: 1.0.0
paths:
  /users/{id}:
    get:
      summary: Lấy thông tin người dùng
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: Thành công
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: integer
                  name:
                    type: string
```

### 4.4. Các công cụ viết document phổ biến

| Công cụ | Đặc điểm |
|---|---|
| **Swagger / OpenAPI** | Chuẩn công nghiệp, có UI tương tác, generate code |
| **Postman** | Vừa test vừa document, dễ chia sẻ collection |
| **Stoplight** | Thiết kế API-first, giao diện đẹp |
| **Redoc** | Render tài liệu từ file OpenAPI, giao diện đẹp, tối ưu đọc |
| **Notion/Confluence** | Đơn giản, phù hợp team nhỏ, không tự động hóa |

---

## 5. API Tool & Template

### 5.1. Công cụ test & thiết kế API

| Công cụ | Mục đích chính |
|---|---|
| **Postman** | Gửi request thử nghiệm, tạo collection, viết test tự động, mock server |
| **Insomnia** | Tương tự Postman, nhẹ hơn, giao diện đơn giản |
| **Hoppscotch** | Công cụ web, mã nguồn mở, chạy trực tiếp trên trình duyệt |
| **cURL** | Command-line, gọi API nhanh từ terminal, dùng trong script |
| **HTTPie** | Command-line, cú pháp thân thiện hơn cURL |
| **Swagger UI / Editor** | Thiết kế và test API dựa trên OpenAPI spec |
| **Apidog** | Kết hợp thiết kế, test, mock, document trong 1 công cụ |

### 5.2. Ví dụ gọi API bằng cURL

```bash
# GET
curl -X GET "https://api.example.com/users/123" \
  -H "Authorization: Bearer <token>"

# POST kèm JSON body
curl -X POST "https://api.example.com/users" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{"name": "Nguyen Van A", "email": "a@example.com"}'
```

### 5.3. Postman Collection — Template mẫu (JSON)

Có thể import thẳng vào Postman:

```json
{
  "info": {
    "name": "User API Collection",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Get User by ID",
      "request": {
        "method": "GET",
        "header": [
          { "key": "Authorization", "value": "Bearer {{token}}" }
        ],
        "url": {
          "raw": "{{base_url}}/users/{{user_id}}",
          "host": ["{{base_url}}"],
          "path": ["users", "{{user_id}}"]
        }
      }
    },
    {
      "name": "Create User",
      "request": {
        "method": "POST",
        "header": [
          { "key": "Content-Type", "value": "application/json" },
          { "key": "Authorization", "value": "Bearer {{token}}" }
        ],
        "body": {
          "mode": "raw",
          "raw": "{\n  \"name\": \"Nguyen Van A\",\n  \"email\": \"a@example.com\"\n}"
        },
        "url": {
          "raw": "{{base_url}}/users",
          "host": ["{{base_url}}"],
          "path": ["users"]
        }
      }
    }
  ],
  "variable": [
    { "key": "base_url", "value": "https://api.example.com" },
    { "key": "token", "value": "" },
    { "key": "user_id", "value": "1" }
  ]
}
```

### 5.4. Template checklist khi xây dựng một API mới

```
□ Đặt tên endpoint theo chuẩn RESTful (danh từ số nhiều, không có động từ)
□ Chọn đúng HTTP method cho từng hành động
□ Định nghĩa rõ request/response schema (dùng OpenAPI)
□ Chuẩn hóa format response (success/error nhất quán)
□ Áp dụng authentication phù hợp (JWT/OAuth...)
□ Xử lý và trả về đúng status code cho từng trường hợp
□ Validate dữ liệu đầu vào, trả lỗi rõ ràng (422/400)
□ Viết document đầy đủ (OpenAPI + ví dụ thực tế)
□ Thêm rate limiting để tránh bị lạm dụng (429)
□ Viết test tự động cho các endpoint (Postman/Newman, hoặc unit test)
□ Versioning API ngay từ đầu (vd: /api/v1/...)
□ Log request/response để debug và giám sát
```

### 5.5. Một số nguyên tắc thiết kế API nên nhớ

- **Versioning**: luôn đánh version (`/api/v1/`) để tránh phá vỡ ứng dụng cũ khi cập nhật
- **Pagination**: với danh sách lớn, luôn phân trang (`page`, `limit` hoặc `cursor`)
- **Rate limiting**: giới hạn số request/thời gian để bảo vệ hệ thống
- **Consistent naming**: đặt tên field nhất quán (camelCase hoặc snake_case, không lẫn lộn)
- **Error handling rõ ràng**: message lỗi phải giúp người gọi API hiểu và sửa được
- **HTTPS bắt buộc**: đặc biệt khi có authentication
- **Idempotency**: với các thao tác quan trọng (thanh toán...), dùng idempotency key để tránh xử lý trùng lặp

---

## 📌 Tổng kết nhanh

| Chủ đề | Câu hỏi cốt lõi |
|---|---|
| API & REST | Client và Server giao tiếp với nhau như thế nào? |
| Request - Response | Dữ liệu được gửi đi và trả về theo cấu trúc nào? |
| Authentication | Làm sao biết ai đang gọi API và họ có quyền gì? |
| Document | Làm sao để người khác dùng được API mà không cần hỏi trực tiếp? |
| Tool & Template | Dùng công cụ gì để test, thiết kế, và chuẩn hóa quy trình làm API? |

---

