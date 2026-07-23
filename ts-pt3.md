## 21. Cấu hình dự án (Configuration - tsconfig.json)

```bash
# note: lệnh khởi tạo file cấu hình mặc định
tsc --init
```

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",        // note: bản JS output tương thích với môi trường nào
    "module": "ESNext",
    "strict": true,             // note: BẬT CÁI NÀY LUÔN, gộp nhiều check an toàn (bao gồm strictNullChecks)
    "outDir": "./dist",         // thư mục chứa file .js sau khi build
    "rootDir": "./src",
    "esModuleInterop": true,    // note: giúp import kiểu CommonJS (vd: import express from "express") không lỗi
    "skipLibCheck": true,       // note: bỏ qua check type trong node_modules cho build nhanh hơn
    "sourceMap": true,          // note: bật cái này để debug .ts trực tiếp trong DevTools/VSCode thay vì .js đã build
    "noUnusedLocals": true,     // báo lỗi nếu khai báo biến mà không dùng
    "noImplicitAny": true       // bắt buộc khai báo kiểu rõ ràng, cấm để TS tự suy ra "any"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

---

## 22. TypeScript với Node.js

```bash
# note: setup 1 project Node + TS mình hay làm
npm init -y
npm install typescript ts-node @types/node --save-dev
npx tsc --init
```

```typescript
// file: server.ts
import http from "http";

const server = http.createServer((req, res) => {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.end("Xin chào từ TypeScript + Node.js");
});

server.listen(3000, () => {
  console.log("Server đang chạy tại http://localhost:3000");
});
```

```bash
# note: chạy trực tiếp không cần build trước
npx ts-node server.ts

# hoặc build ra JS rồi chạy bằng node thường (dùng khi deploy production)
npx tsc
node dist/server.js
```

---

## 23. TypeScript với React

```tsx
// note: file React + TS phải đặt đuôi .tsx (không phải .ts) vì có cú pháp JSX

import { useState } from "react";

// Định nghĩa kiểu cho props
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

function MyButton({ label, onClick, disabled = false }: ButtonProps) {
  return (
    <button onClick={onClick} disabled={disabled}>
      {label}
    </button>
  );
}

// Component với state có kiểu
function Counter() {
  const [count, setCount] = useState<number>(0);
  // note: useState thường TỰ suy luận kiểu từ giá trị khởi tạo, không nhất thiết phải ghi <number>
  // nhưng ghi rõ vẫn tốt khi giá trị khởi tạo là null hoặc kiểu phức tạp.

  return (
    <div>
      <p>Đếm: {count}</p>
      <MyButton label="Tăng" onClick={() => setCount(count + 1)} />
    </div>
  );
}

export default Counter;
```

```bash
# note: tạo project React + TS nhanh nhất hiện nay (2026) dùng Vite
npm create vite@latest my-app -- --template react-ts
```

---

## 24. Công cụ hỗ trợ (Tooling)

```bash
# note: bộ công cụ mình hay dùng khi làm project TS thật sự

# ESLint - kiểm tra style & lỗi tiềm ẩn
npm install eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin --save-dev

# Prettier - tự động format code
npm install prettier --save-dev

# ts-node-dev - tự động restart khi code thay đổi (giống nodemon nhưng hiểu TS)
npm install ts-node-dev --save-dev
```

```json
// package.json (đoạn scripts hay dùng)
{
  "scripts": {
    "dev": "ts-node-dev --respawn src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "lint": "eslint src --ext .ts"
  }
}
```

---

## 25. Kiểu nâng cao (Advanced Types)

```typescript
// Intersection type - "gộp" nhiều type lại làm 1
type CoTen = { ten: string };
type CoTuoi = { tuoi: number };
type NguoiDayDu = CoTen & CoTuoi; // bắt buộc có CẢ 2 thuộc tính

// Union + Discriminated Union - note: pattern RẤT hay dùng để mô hình hóa "trạng thái"
type TrangThaiTaiDuLieu =
  | { trangThai: "dang_tai" }
  | { trangThai: "thanh_cong"; duLieu: string }
  | { trangThai: "loi"; loi: string };

function xuLy(state: TrangThaiTaiDuLieu) {
  switch (state.trangThai) {
    case "dang_tai":
      console.log("Đang tải...");
      break;
    case "thanh_cong":
      console.log("Dữ liệu:", state.duLieu); // TS biết chắc có .duLieu ở nhánh này
      break;
    case "loi":
      console.log("Lỗi:", state.loi);
      break;
  }
}
```

---

## 26. Type Guards

```typescript
// note: type guard = cách "thu hẹp" (narrow) kiểu dữ liệu để TS biết chính xác đang xử lý kiểu gì

// typeof guard - dùng cho kiểu nguyên thủy
function inGiaTri(x: string | number) {
  if (typeof x === "string") {
    console.log(x.toUpperCase());
  } else {
    console.log(x.toFixed(2));
  }
}

// instanceof guard - dùng cho class
class Cho { sua() { console.log("Gâu"); } }
class Meo { keu() { console.log("Meo"); } }
function amThanh(dv: Cho | Meo) {
  if (dv instanceof Cho) {
    dv.sua();
  } else {
    dv.keu();
  }
}

// "in" guard - kiểm tra thuộc tính có tồn tại trong object không
type Chim = { bay: () => void };
type Ca = { boi: () => void };
function diChuyen(dv: Chim | Ca) {
  if ("bay" in dv) {
    dv.bay();
  } else {
    dv.boi();
  }
}

// Custom type guard (dùng "is") - note: tự viết hàm kiểm tra kiểu riêng, tái sử dụng được nhiều nơi
function laChuoi(gia_tri: unknown): gia_tri is string {
  return typeof gia_tri === "string";
}
function xuLyGiaTri(gia_tri: unknown) {
  if (laChuoi(gia_tri)) {
    console.log(gia_tri.toUpperCase()); // TS đã biết chắc là string
  }
}
```

---

## 27. Conditional Types

```typescript
// note: kiểu có "logic if/else" ngay trong hệ thống type, hơi trừu tượng lúc đầu nhưng quen là dùng quen tay

type LaString<T> = T extends string ? "phai" : "khong_phai";
type A = LaString<string>; // "phai"
type B = LaString<number>; // "khong_phai"

// Kết hợp với infer - note: dùng để "trích xuất" 1 kiểu con bên trong kiểu phức tạp
type PhanTuMang<T> = T extends (infer U)[] ? U : never;
type KieuPhanTu = PhanTuMang<string[]>; // string

// Ví dụ thực tế: lấy kiểu resolve của 1 Promise
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;
type KetQua = UnwrapPromise<Promise<number>>; // number
```

---

## 28. Mapped Types

```typescript
// note: dùng để "biến đổi" tất cả thuộc tính của 1 type theo cùng 1 quy luật, thay vì viết tay từng field

type NguoiDung = { ten: string; tuoi: number };

// Tự chế lại Partial<T> để hiểu cơ chế
type TuLamPartial<T> = {
  [K in keyof T]?: T[K];
};

// Tự chế lại Readonly<T>
type TuLamReadonly<T> = {
  readonly [K in keyof T]: T[K];
};

// Đổi tất cả property thành kiểu string (hữu ích khi làm form data)
type TatCaLaChuoi<T> = {
  [K in keyof T]: string;
};
type FormNguoiDung = TatCaLaChuoi<NguoiDung>; // { ten: string; tuoi: string }
```

---

## 29. Suy luận kiểu (Type Inference) - nâng cao

```typescript
// note: phần "Explicit & Inference" ở Part 1 là cơ bản, đây là ứng dụng suy luận nâng cao hơn

// TS tự suy ra kiểu return của hàm dựa vào logic bên trong
function tinhTong(a: number, b: number) {
  return a + b; // TS tự hiểu return type là number, không cần ghi ": number"
}

// Suy luận kiểu trong generic dựa vào tham số truyền vào
function taoMang<T>(...items: T[]): T[] {
  return items;
}
const mang = taoMang(1, 2, 3); // TS suy ra T = number, không cần chỉ định

// Suy luận "kiểu tốt nhất chung" (best common type) khi mảng chứa nhiều kiểu
let hoTron = [1, "hai", true]; // TS suy ra (string | number | boolean)[]
```

---

## 30. Literal Types

```typescript
// note: literal type = giới hạn giá trị CHÍNH XÁC, không phải kiểu chung chung như string/number

let huong: "trai" | "phai" | "len" | "xuong";
huong = "trai"; // ✅
// huong = "cheo"; // ❌ lỗi vì không nằm trong danh sách literal cho phép

// Literal type cho number
type XucXac = 1 | 2 | 3 | 4 | 5 | 6;

// Kết hợp literal type với template literal (TS 4.1+) - note: tính năng mình khá thích, giúp validate string pattern
type ThuTrongTuan = "Mon" | "Tue" | "Wed";
type SuKien = `su-kien-${ThuTrongTuan}`; // "su-kien-Mon" | "su-kien-Tue" | "su-kien-Wed"
```

---

## 31. Namespaces

```typescript
// note: namespace là cách "gom nhóm" code TS trước khi ES Modules (import/export) phổ biến rộng rãi.
// Ngày nay hầu như chỉ nên dùng ES Modules, namespace chủ yếu gặp trong code cũ hoặc file .d.ts.

namespace TienIch {
  export function congTien(a: number, b: number): number {
    return a + b;
  }
  export const PHI_VAN_CHUYEN = 30000;
}

console.log(TienIch.congTien(100, 200));
console.log(TienIch.PHI_VAN_CHUYEN);

// note: nếu đang bắt đầu project mới, hãy dùng module (import/export) thay vì namespace.
```

---

## 32. Index Signatures

```typescript
// note: dùng khi KHÔNG BIẾT TRƯỚC hết tất cả tên thuộc tính của object,
// nhưng biết kiểu chung của key và value.

interface BangGia {
  [tenSanPham: string]: number;
}
const gia: BangGia = {
  "Áo thun": 150000,
  "Quần jean": 350000,
};
gia["Giày"] = 500000; // vẫn hợp lệ dù chưa khai báo trước tên "Giày"

// Kết hợp index signature với vài thuộc tính cố định
interface CauHinh {
  tenApp: string;
  [key: string]: string; // note: các key khác ngoài "tenApp" đều phải kiểu string
}
```

---

## 33. Declaration Merging

```typescript
// note: TS cho phép "gộp" nhiều khai báo CÙNG TÊN lại với nhau, chỉ áp dụng cho interface/namespace
// (không áp dụng cho type alias).

interface NguoiDung {
  ten: string;
}
interface NguoiDung {
  tuoi: number;
}
// note: 2 khai báo interface NguoiDung ở trên tự động gộp thành 1: { ten: string; tuoi: number }

const u: NguoiDung = { ten: "An", tuoi: 25 };

// Ứng dụng thực tế: mở rộng type có sẵn của thư viện (module augmentation)
// declare global {
//   interface Window {
//     myCustomProp: string; // note: dùng khi muốn thêm property tùy biến vào window mà TS không báo lỗi
//   }
// }
```

---

## 34. Lập trình bất đồng bộ (Async Programming)

```typescript
// note: async/await trong TS y hệt JS, điểm khác biệt là khai báo kiểu Promise<T> tường minh

function cho(ms: number): Promise<void> {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function layDuLieu(): Promise<string> {
  await cho(1000);
  return "Dữ liệu đã tải xong";
}

async function main() {
  try {
    const data = await layDuLieu();
    console.log(data);
  } catch (err) {
    // note: TS mặc định coi err trong catch là "unknown" (từ TS 4.4+), phải kiểm tra kiểu trước khi dùng
    if (err instanceof Error) {
      console.log(err.message);
    }
  }
}

// Gọi API với kiểu response rõ ràng - note: pattern hay dùng nhất trong thực tế
interface NguoiDungAPI {
  id: number;
  name: string;
}
async function layUsers(): Promise<NguoiDungAPI[]> {
  const res = await fetch("https://jsonplaceholder.typicode.com/users");
  const data: NguoiDungAPI[] = await res.json();
  return data;
}
```

---

## 35. Decorators

```typescript
// note: decorator là "hàm bọc" thêm hành vi cho class/method/property mà không sửa code gốc.
// Hay gặp nhiều trong framework như Angular, NestJS. Cần bật "experimentalDecorators": true trong tsconfig
// (hoặc dùng decorator chuẩn ECMAScript mới của TS 5+ không cần bật flag này).

function Log(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`Gọi hàm ${propertyKey} với tham số:`, args);
    return originalMethod.apply(this, args);
  };
  return descriptor;
}

class MayTinh {
  @Log
  cong(a: number, b: number): number {
    return a + b;
  }
}

const mt = new MayTinh();
mt.cong(2, 3);
// note: khi gọi mt.cong(2,3), console sẽ tự in ra log tham số nhờ decorator, không cần sửa gì bên trong cong()
```

---

## 36. TS trong dự án JS (JSDoc)

```javascript
// note: không phải lúc nào cũng convert hết project sang .ts, có thể dùng JSDoc
// để TS "kiểm tra kiểu ngầm" ngay trong file .js thường, hữu ích khi migrate dần dần.

/**
 * @param {number} a
 * @param {number} b
 * @returns {number}
 */
function cong(a, b) {
  return a + b;
}

// Thêm dòng này đầu file .js để bật type-checking từ VS Code/TS
// @ts-check
cong(1, "2"); // ⚠️ VS Code sẽ gạch đỏ báo lỗi kiểu dù đây là file .js thường
```

---

## 37. Di trú từ JS sang TS (Migration)

```bash
# note: quy trình mình hay áp dụng khi convert 1 project JS lớn sang TS, làm TỪ TỪ chứ không đổi 1 lần

# Bước 1: cài TS và tạo tsconfig với "allowJs": true để .js và .ts sống chung
npm install typescript --save-dev
npx tsc --init
```
```json
{
  "compilerOptions": {
    "allowJs": true,
    "checkJs": false,
    "strict": false,
    "noImplicitAny": false
  }
}
```
```typescript
// note: Bước 2: đổi dần từng file .js -> .ts, ưu tiên file ít phụ thuộc nhất trước
// Bước 3: sau khi đổi xong 1 file, bật dần các cờ strict lên (strict: true, noImplicitAny: true)
// Bước 4: xử lý các lỗi kiểu phát sinh, thường tập trung ở chỗ dùng any ngầm định

// note: đừng cố bật "strict": true ngay từ đầu cho cả project cũ, sẽ ngập lỗi và nản, làm từng bước là ổn nhất.
```

---

## 38. Xử lý lỗi (Error Handling)

```typescript
// note: TS 4.4+ mặc định coi biến trong catch là "unknown", không phải "any" như JS thường
// -> đây là 1 điểm khác biệt hay bị bug nếu ai quen JS chuyển qua.

class LoiKhongTimThay extends Error {
  constructor(message: string) {
    super(message);
    this.name = "LoiKhongTimThay";
  }
}

function timSanPham(id: number) {
  if (id !== 1) {
    throw new LoiKhongTimThay(`Không tìm thấy sản phẩm id=${id}`);
  }
  return { id, ten: "Áo thun" };
}

try {
  timSanPham(99);
} catch (err) {
  if (err instanceof LoiKhongTimThay) {
    console.log("Lỗi cụ thể:", err.message);
  } else if (err instanceof Error) {
    console.log("Lỗi chung:", err.message);
  } else {
    console.log("Lỗi không xác định:", err);
  }
}
```

---

## 39. Thực hành tốt (Best Practices)

```typescript
// note: đây là những điều mình rút ra sau 1 thời gian dùng TS, không phải luật cứng nhưng nên theo

// 1. LUÔN bật "strict": true trong tsconfig ngay từ đầu project mới
// 2. Tránh dùng "any" - nếu thực sự chưa biết kiểu, dùng "unknown" rồi narrow xuống
// 3. Ưu tiên interface cho object/class, type cho union/intersection
let vd_any: any;      // ❌ tránh
let vd_unknown: unknown; // ✅ an toàn hơn

// 4. Đặt kiểu tường minh cho function param/return, để TS tự suy luận biến local là được
function tot(a: number, b: number): number { // ✅ rõ ràng
  const tong = a + b; // không cần ghi ": number" ở đây, TS tự hiểu
  return tong;
}

// 5. Dùng readonly cho props/config không nên bị thay đổi ngoài ý muốn
interface CauHinhApp {
  readonly apiUrl: string;
}

// 6. Tận dụng Utility Types (Partial, Pick, Omit...) thay vì viết tay lại type tương tự

// 7. Không lạm dụng "!" (non-null assertion) - mỗi lần dùng là 1 lần tự chịu trách nhiệm nếu sai
```


