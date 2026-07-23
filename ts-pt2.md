## 12. Hàm (Functions)

```typescript
// Khai báo kiểu cho tham số và giá trị trả về - note: NÊN LÀM luôn, đừng lười, đỡ debug về sau
function cong(a: number, b: number): number {
  return a + b;
}

// Tham số tùy chọn (?) - note: phải để CUỐI danh sách tham số
function chao(ten: string, loiChao?: string): string {
  return `${loiChao ?? "Xin chào"}, ${ten}`;
}

// Tham số mặc định
function nhan(a: number, b: number = 2): number {
  return a * b;
}

// Rest parameter
function tongNhieuSo(...soList: number[]): number {
  return soList.reduce((tong, n) => tong + n, 0);
}

// Arrow function với kiểu tường minh
const tru = (a: number, b: number): number => a - b;

// Function type - khai báo "hình dạng" của 1 hàm để dùng làm kiểu
type PhepTinh = (a: number, b: number) => number;
const nhanTheoType: PhepTinh = (a, b) => a * b;

// void vs never trong hàm (note: xem lại phần Special Types nếu quên)
function ghiLog(msg: string): void {
  console.log(msg);
}
```

---

## 13. Ép kiểu (Casting)

```typescript
// note: casting KHÔNG chuyển đổi giá trị thật sự lúc runtime, chỉ nói cho TS biết
// "tôi chắc chắn đây là kiểu X", nên dùng cẩn thận, sai là lỗi runtime chứ TS không cứu được.

let giaTri: unknown = "Xin chào";

// Cách 1: dùng "as"
let doDai1: number = (giaTri as string).length;

// Cách 2: dùng dấu <> (note: KHÔNG dùng được trong file .tsx vì trùng cú pháp JSX)
let doDai2: number = (<string>giaTri).length;

// Ép kiểu qua DOM - note: rất hay gặp khi làm việc với HTML element
const inputEl = document.getElementById("myInput") as HTMLInputElement;
console.log(inputEl.value); // nếu không "as HTMLInputElement" thì TS không biết .value tồn tại
```

---

## 14. Lớp (Classes)

```typescript
class DongVat {
  // note: khai báo kiểu cho property luôn ngay đầu class, đỡ phải đoán field có gì
  ten: string;
  protected tuoi: number;   // chỉ class này và class con truy cập được
  private soDinhDanh: string; // chỉ chính class này truy cập được
  readonly loai: string = "Động vật"; // note: readonly = gán 1 lần lúc khởi tạo, sau đó không đổi được

  constructor(ten: string, tuoi: number) {
    this.ten = ten;
    this.tuoi = tuoi;
    this.soDinhDanh = Math.random().toString(36);
  }

  keu(): void {
    console.log(`${this.ten} đang kêu`);
  }
}

// note: cách viết tắt hay dùng - khai báo property NGAY trong constructor luôn, đỡ viết trùng lặp
class DongVatNgan {
  constructor(
    public ten: string,
    protected tuoi: number,
    private maSo: string = "N/A"
  ) {}
}

// Kế thừa
class Cho extends DongVat {
  constructor(ten: string, tuoi: number, public giong: string) {
    super(ten, tuoi);
  }

  // Override phương thức cha
  keu(): void {
    console.log(`${this.ten} sủa: Gâu gâu!`);
  }
}

const cho1 = new Cho("Lu", 3, "Husky");
cho1.keu();

// Interface áp dụng cho class - "implements"
interface CoTheBoi {
  boi(): void;
}
class Ca extends DongVat implements CoTheBoi {
  boi(): void {
    console.log(`${this.ten} đang bơi`);
  }
}

// Abstract class - note: không thể new trực tiếp, bắt buộc class con phải implement phần abstract
abstract class Hinh {
  abstract dienTich(): number;
  moTa(): void {
    console.log(`Diện tích: ${this.dienTich()}`);
  }
}
class HinhTron extends Hinh {
  constructor(private banKinh: number) {
    super();
  }
  dienTich(): number {
    return Math.PI * this.banKinh ** 2;
  }
}
```

---

## 15. Generics cơ bản (Basic Generics)

```typescript
// note: generic giải quyết vấn đề "muốn viết 1 hàm dùng được cho NHIỀU kiểu
// nhưng vẫn giữ được type-safety", thay vì phải dùng any (mất an toàn) hoặc viết trùng lặp nhiều hàm.

function traVe<T>(gia_tri: T): T {
  return gia_tri;
}
console.log(traVe<string>("Xin chào")); // T = string
console.log(traVe<number>(123));        // T = number
// note: đa số trường hợp không cần ghi <string> tường minh, TS tự suy luận được từ tham số truyền vào

function phanTuDauTien<T>(arr: T[]): T {
  return arr[0];
}
console.log(phanTuDauTien([1, 2, 3]));       // 1
console.log(phanTuDauTien(["a", "b", "c"])); // "a"

// Generic với nhiều kiểu tham số
function ghepCap<K, V>(key: K, value: V): [K, V] {
  return [key, value];
}
console.log(ghepCap<string, number>("tuoi", 25));

// Generic Interface - note: dùng rất nhiều khi định nghĩa API response chung
interface KetQuaAPI<T> {
  thanhCong: boolean;
  duLieu: T;
}
const ketQua: KetQuaAPI<{ ten: string }> = {
  thanhCong: true,
  duLieu: { ten: "An" },
};

// Generic Class
class HopChua<T> {
  private noiDung: T;
  constructor(gia_tri: T) {
    this.noiDung = gia_tri;
  }
  layGiaTri(): T {
    return this.noiDung;
  }
}
const hop = new HopChua<number>(100);

// Giới hạn generic (constraint) - note: bắt T phải "ít nhất" có thuộc tính length
function inDoDai<T extends { length: number }>(item: T): void {
  console.log(item.length);
}
inDoDai("Xin chào"); // OK vì string có length
inDoDai([1, 2, 3]);  // OK vì array có length
```

---

## 16. Utility Types

```typescript
interface NguoiDung {
  ten: string;
  tuoi: number;
  email: string;
}

// Partial<T> - biến TẤT CẢ thuộc tính thành optional
// note: hay dùng cho hàm "update", vì update thường chỉ sửa 1 vài field chứ không phải hết
function capNhat(user: NguoiDung, thayDoi: Partial<NguoiDung>): NguoiDung {
  return { ...user, ...thayDoi };
}

// Required<T> - ngược lại Partial, bắt buộc TẤT CẢ thuộc tính phải có
type NguoiDungBatBuoc = Required<NguoiDung>;

// Readonly<T> - tất cả thuộc tính không thể gán lại sau khi khởi tạo
const userChiDoc: Readonly<NguoiDung> = { ten: "An", tuoi: 25, email: "a@x.com" };
// userChiDoc.ten = "Bình"; // ❌ lỗi

// Pick<T, Keys> - chọn 1 vài thuộc tính cụ thể để tạo type mới
type ThongTinCoBan = Pick<NguoiDung, "ten" | "email">;

// Omit<T, Keys> - ngược lại Pick, LOẠI BỎ 1 vài thuộc tính
type NguoiDungKhongEmail = Omit<NguoiDung, "email">;

// Record<Keys, Type> - note: cực hay dùng để khai báo kiểu cho object dùng như "dictionary"
type BangDiem = Record<string, number>;
const diem: BangDiem = { Toan: 9, Van: 8, Anh: 7.5 };

// ReturnType<T> - lấy ra kiểu trả về của 1 hàm
function taoUser() {
  return { ten: "An", tuoi: 25 };
}
type KieuUser = ReturnType<typeof taoUser>;

// Exclude / Extract - lọc union type
type Kieu1 = "a" | "b" | "c";
type Kieu2 = Exclude<Kieu1, "a">; // "b" | "c"
type Kieu3 = Extract<Kieu1, "a" | "z">; // "a"
```

---

## 17. Keyof

```typescript
interface NguoiDung {
  ten: string;
  tuoi: number;
}

// keyof lấy ra union các TÊN THUỘC TÍNH của 1 type, dạng string literal
type KeyNguoiDung = keyof NguoiDung; // "ten" | "tuoi"

// note: hay dùng kết hợp generic để viết hàm "lấy giá trị theo key" mà vẫn an toàn kiểu
function layGiaTri<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
const user = { ten: "An", tuoi: 25 };
console.log(layGiaTri(user, "ten"));  // OK, trả về string
// layGiaTri(user, "email");          // ❌ lỗi vì "email" không phải key của user
```

---

## 18. Null (xử lý null/undefined)

```typescript
// note: mặc định TS coi null/undefined là "con" của mọi kiểu, TRỪ KHI bật strictNullChecks trong tsconfig
// -> mình LUÔN bật strictNullChecks (mặc định true khi bật "strict": true) vì nó bắt được rất nhiều bug tiềm ẩn.

let ten: string | null = null;
ten = "An"; // OK

function inTen(ten: string | null) {
  if (ten !== null) {
    console.log(ten.toUpperCase());
  } else {
    console.log("Chưa có tên");
  }
}

// Optional chaining (?.) - note: cực kỳ hữu dụng, tránh crash "Cannot read property of undefined"
type NguoiDung = { diaChi?: { thanhPho?: string } };
const u: NguoiDung = {};
console.log(u.diaChi?.thanhPho ?? "Không rõ");

// Non-null assertion (!) - note: dùng khi MÌNH CHẮC CHẮN 100% giá trị không null,
// nhưng cẩn thận vì nếu đoán sai sẽ lỗi runtime, TS không cứu được nữa.
function layPhanTu(arr: number[] | null) {
  return arr!.length; // khẳng định với TS "arr chắc chắn không null ở đây"
}
```

---

## 19. DefinitelyTyped

```typescript
// note: DefinitelyTyped là kho chứa các file khai báo kiểu (.d.ts) cho các thư viện JS
// vốn được viết bằng JS thuần (không có sẵn type), ví dụ lodash, jquery,...

// cài package thường
// npm install lodash

// cài thêm type definition tương ứng (namespace @types)
// npm install --save-dev @types/lodash

import _ from "lodash";
const arr = _.chunk([1, 2, 3, 4, 5], 2);
// note: nhờ có @types/lodash mà TS biết _.chunk trả về kiểu gì, có gợi ý autocomplete đầy đủ

// Nếu 1 thư viện KHÔNG có sẵn type, có thể tự khai báo tối thiểu:
declare module "thu-vien-khong-co-type" {
  export function lamGi(input: string): number;
}
```

---

## 20. Cập nhật TypeScript 5 (TS 5 Updates)

```typescript
// note: liệt kê vài điểm mới đáng chú ý mà mình hay dùng từ TS 5.x trở lên

// 1. const type parameters - giữ nguyên kiểu literal chính xác thay vì bị "mở rộng" thành kiểu chung
function layDauTien<const T extends readonly unknown[]>(arr: T): T[0] {
  return arr[0];
}
const ketQua = layDauTien(["a", "b", "c"]); // kiểu trả về là "a", không bị mở rộng thành string

// 2. Decorators theo chuẩn ECMAScript mới (xem thêm phần Decorators bên dưới)

// 3. Hỗ trợ tốt hơn cho enum, cải thiện performance của trình biên dịch

// note: TS cập nhật khá nhanh (2-3 tháng/lần minor version), nên thỉnh thoảng đọc changelog chính thức
// tại github.com/microsoft/TypeScript/releases để cập nhật tính năng mới.
```