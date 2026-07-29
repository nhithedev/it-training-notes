# Ghi chú: NestJS (overview)

---

## NestJS là gì?

- Là một framework để viết backend bằng Node.js
- Backend là phần xử lý phía sau: nhận request, xử lý, trả kết quả về
- Viết bằng **TypeScript** (JavaScript có thêm kiểu dữ liệu)
- Lấy cảm hứng từ **Angular**, có decorator, module, dependency injection (giải thích bên dưới)

**Tại sao dùng Nest thay vì Express?**
- Express để cho lập trình viên tự do tổ chức code theo ý mình, nên dự án lớn dễ trở nên lộn xộn
- Nest quy định sẵn một cấu trúc chung, nên nhiều người cùng viết code vẫn giữ được sự thống nhất

---

## Dùng khi

- Xây API (REST, GraphQL...) cho các dự án dự kiến sẽ phát triển lớn dần
- Team nhiều người, cần code có cấu trúc thống nhất
- Người đã quen Angular sẽ thấy cách tổ chức của Nest khá quen thuộc

---

## Bốn thành phần chính 


### 1. Module
- Là nơi gom nhóm các phần code liên quan với nhau (ví dụ: mọi thứ liên quan đến "users" gom vào `UsersModule`)
- Mỗi ứng dụng đều có ít nhất một module gốc (`AppModule`)

```typescript
@Module({
  controllers: [UsersController],
  providers: [UsersService],
})
export class UsersModule {}
```

### 2. Controller
- Nhận request từ bên ngoài và trả về response
- Đây là nơi định nghĩa các route (đường dẫn API)

```typescript
@Controller('users')
export class UsersController {
  @Get()
  findAll() {
    return ['Alice', 'Bob'];
  }
}
```
Đoạn code trên tạo ra route `GET /users`.

### 3. Provider (thường gọi là Service)
- Nơi chứa logic xử lý chính: tính toán, lấy dữ liệu từ database, gọi các API khác...
- Controller không nên xử lý logic nặng, mà nên giao việc đó cho Service

```typescript
@Injectable()
export class UsersService {
  findAll() {
    return ['Alice', 'Bob'];
  }
}
```

### 4. Dependency Injection (DI)
Đây là khái niệm quan trọng nhất của Nest.

- Thông thường, Controller sẽ tự tạo instance của Service (`new UsersService()`)
- Với Nest, framework sẽ tự tạo sẵn Service và tự động đưa vào Controller khi cần

```typescript
@Controller('users')
export class UsersController {
  constructor(private usersService: UsersService) {}

  @Get()
  findAll() {
    return this.usersService.findAll();
  }
}
```

Lợi ích của cách làm này:
- Dễ viết test hơn (có thể thay Service thật bằng một Service giả khi test)
- Nest tự quản lý việc khởi tạo và kết nối các thành phần, giảm bớt công việc thủ công

---

## Một request đi qua Nest như thế nào

```
Request gửi đến (ví dụ: GET /users/5)
      ↓
Controller tiếp nhận (theo đúng route)
      ↓
Có thể đi qua middleware / guard / pipe trước
      ↓
Controller gọi Service để xử lý
      ↓
Service có thể truy vấn database (qua TypeORM, Prisma...)
      ↓
Kết quả được trả ngược lại thành response cho người dùng
```

---

## Một số thuật ngữ thường gặp

| Thuật ngữ | Ý nghĩa |
|---|---|
| Decorator | Ký hiệu dạng `@...()` gắn lên class hoặc method để báo cho Nest biết vai trò của nó (ví dụ: `@Controller()`, `@Injectable()`) |
| DTO | "Data Transfer Object" — class mô tả cấu trúc dữ liệu được gửi vào hoặc trả ra |
| Pipe | Kiểm tra hoặc biến đổi dữ liệu trước khi vào controller |
| Guard | Quyết định một request có được phép đi tiếp hay không (thường dùng để kiểm tra đăng nhập) |
| Interceptor | Bổ sung hành vi xung quanh một request, ví dụ ghi log hoặc chỉnh sửa response |
| Middleware | Đoạn code chạy trước route handler, tương tự middleware trong Express |

---

## Ví dụ tổng hợp

```typescript
// users.service.ts
@Injectable()
export class UsersService {
  private users = ['Alice', 'Bob'];
  findAll() {
    return this.users;
  }
}

// users.controller.ts
@Controller('users')
export class UsersController {
  constructor(private usersService: UsersService) {}

  @Get()
  findAll() {
    return this.usersService.findAll();
  }
}

// users.module.ts
@Module({
  controllers: [UsersController],
  providers: [UsersService],
})
export class UsersModule {}

// app.module.ts
@Module({
  imports: [UsersModule],
})
export class AppModule {}
```

Chạy ứng dụng và truy cập `/users` sẽ nhận được kết quả `["Alice", "Bob"]`.

---

## Các bước bắt đầu thực hành

1. Cài Nest CLI:
   ```
   npm i -g @nestjs/cli
   ```
2. Tạo project mới:
   ```
   nest new ten-project
   ```
3. Tạo từng thành phần khi cần:
   ```
   nest generate module users
   nest generate controller users
   nest generate service users
   ```
4. Chạy ứng dụng:
   ```
   npm run start:dev
   ```

---

## Điểm mạnh của Nest

- Giữ cho project có cấu trúc rõ ràng dù quy mô lớn đến đâu
- Có thể xây dựng REST API, GraphQL, WebSocket, microservices, đều dùng chung một bộ khái niệm
- Dễ dàng tích hợp với TypeORM/Prisma (database), Passport (xác thực), Swagger (tài liệu API)

## Một số điểm cần lưu ý

- Với các project nhỏ hoặc demo nhanh, Nest có thể tạo cảm giác rườm rà hơn so với Express thuần
- Cần có kiến thức cơ bản về TypeScript và decorator để học thuận lợi hơn
- Giai đoạn đầu học có phần khó khăn, nhưng sẽ có ích khi dự án phát triển lớn dần

---

## Việc cần làm tiếp theo

- Đọc tài liệu chính thức: https://docs.nestjs.com
- Tự xây dựng một API CRUD nhỏ (ví dụ: ứng dụng to-do list) để luyện tập module, controller, service
- Tìm hiểu thêm về guard (xác thực), pipe (validate dữ liệu với `class-validator`), và kết nối database thực tế