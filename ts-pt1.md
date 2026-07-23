# PHẦN 1: NHẬP MÔN & KIỂU DỮ LIỆU CƠ BẢN

## 1. Giới thiệu TypeScript (Introduction)

```typescript
// note: TS = JS + hệ thống kiểu (type system). Code TS cuối cùng vẫn bị "compile" (thật ra là transpile) về JS thuần
// để chạy trên trình duyệt/Node, vì trình duyệt không hiểu TS.

let message: string = "Xin chào TypeScript";
console.log(message);

// note: phần lớn lỗi TS bắt được là lỗi lúc BIÊN DỊCH (compile-time),
// không phải lúc chạy (runtime) như JS thuần -> đỡ phải debug lúc app đã chạy cho user thấy.
```

**Vì sao dùng TS thay vì JS?**
- Bắt lỗi sớm ngay lúc gõ code (nhờ IDE gợi ý), không phải đợi chạy mới biết sai.
- Tự động gợi ý (autocomplete) chính xác hơn vì IDE biết rõ kiểu dữ liệu.
- Dễ bảo trì code lớn, nhiều người cùng làm.

---

## 2. Bắt đầu với TypeScript (Get Started)

```bash
# note: cần cài Node.js trước, sau đó cài TS toàn cục hoặc theo project
npm install -g typescript

# kiểm tra version
tsc -v
```

```typescript
// file: app.ts
let ten: string = "An";
console.log(`Xin chào ${ten}`);
```

```bash
# biên dịch file .ts sang .js
tsc app.ts
# note: lệnh trên sẽ tạo ra app.js, chạy được bằng node app.js hoặc nhúng vào HTML
```

> 💡 Trong thực tế hầu như không ai gõ `tsc` tay từng file — thường dùng kèm Vite, webpack, hoặc `ts-node` để chạy trực tiếp `.ts` không cần build ra `.js` trước.

```bash
# note: cách mình hay dùng để test nhanh 1 file .ts mà không build ra .js
npx ts-node app.ts
```

---

## 3. Kiểu dữ liệu đơn giản (Simple Types)

```typescript
let hoTen: string = "Nguyễn Văn A";
let tuoi: number = 25;         // note: TS chỉ có 1 kiểu number, không tách int/float như C/Java
let daTotNghiep: boolean = true;

// Gán sai kiểu -> báo lỗi ngay khi gõ, không cần chạy
// tuoi = "hai mươi lăm"; // ❌ Lỗi: Type 'string' is not assignable to type 'number'
```

---

## 4. Khai báo tường minh & suy luận kiểu (Explicit & Inference)

```typescript
// Explicit - khai báo kiểu rõ ràng
let a: number = 10;

// Inference - TS TỰ đoán kiểu dựa vào giá trị gán ban đầu
let b = 10; // note: TS tự hiểu b là number, không cần viết ": number"
// b = "text"; // ❌ vẫn báo lỗi dù mình không khai báo kiểu tường minh

// note: mình thường CHỈ khai báo kiểu tường minh khi:
// 1. Không gán giá trị ngay lúc khai báo
// 2. Function param/return (bắt buộc nên rõ ràng)
// 3. Kiểu phức tạp mà TS không đoán đúng ý mình
let c: string; // chưa có giá trị -> bắt buộc khai báo kiểu
c = "sau này gán";
```

---

## 5. Kiểu đặc biệt (Special Types)

```typescript
// any - tắt hoàn toàn kiểm tra kiểu (note: cố tránh dùng, mất hết lợi ích của TS)
let khongRoKieu: any = 5;
khongRoKieu = "giờ là string cũng được"; // TS không báo lỗi gì cả

// unknown - an toàn hơn any, bắt buộc phải kiểm tra kiểu trước khi dùng
let giaTri: unknown = "hello";
// giaTri.toUpperCase(); // ❌ lỗi vì TS không biết chắc đây là string
if (typeof giaTri === "string") {
  giaTri.toUpperCase(); // ✅ OK sau khi đã kiểm tra (type narrowing)
}

// void - dùng cho hàm không trả về giá trị gì
function log(msg: string): void {
  console.log(msg);
  // note: không có return, hoặc return; không có giá trị
}

// never - dùng cho hàm KHÔNG BAO GIỜ kết thúc bình thường (luôn throw hoặc loop vô hạn)
function baoLoi(msg: string): never {
  throw new Error(msg);
}

// object - kiểu tổng quát cho bất kỳ object nào (note: nên dùng interface/type cụ thể hơn thay vì object trần)
let obj: object = { ten: "An" };
```

---

## 6. Mảng (Arrays)

```typescript
let danhSachSo: number[] = [1, 2, 3];
let danhSachTen: string[] = ["An", "Bình"];

// Cách viết khác dùng generic Array<T> - note: 2 cách này tương đương, mình thấy dấu [] gọn hơn nên hay dùng
let ds2: Array<number> = [1, 2, 3];

danhSachSo.push(4);
// danhSachSo.push("text"); // ❌ lỗi vì mảng đã khai báo là number[]

// Mảng chứa nhiều kiểu (union type trong mảng)
let hoTron: (string | number)[] = ["An", 25, "Bình", 30];
```

---

## 7. Tuples

```typescript
// Tuple = mảng có SỐ LƯỢNG PHẦN TỬ CỐ ĐỊNH và MỖI VỊ TRÍ có kiểu riêng
let nguoiDung: [string, number] = ["An", 25];
// note: khác array thường ở chỗ thứ tự và kiểu từng phần tử được ràng buộc chặt

// nguoiDung = [25, "An"]; // ❌ lỗi vì sai thứ tự kiểu
// nguoiDung = ["An", 25, true]; // ❌ lỗi vì dư phần tử

console.log(nguoiDung[0].toUpperCase()); // TS biết chắc index 0 là string
console.log(nguoiDung[1] + 1);           // TS biết chắc index 1 là number

// Tuple có tên (từ TS 4+) - note: giúp code dễ đọc hơn hẳn, mình khuyên dùng cái này thay vì tuple trần
let toaDo: [x: number, y: number] = [10, 20];
```

---

## 8. Kiểu object (Object Types)

```typescript
// Khai báo trực tiếp kiểu cho object
let sinhVien: { ten: string; tuoi: number } = {
  ten: "Lan",
  tuoi: 20,
};

// Thuộc tính tùy chọn (optional) dùng dấu ?
let sanPham: { ten: string; giaKhuyenMai?: number } = {
  ten: "Áo thun",
  // note: giaKhuyenMai có thể bỏ qua vì đã đánh dấu optional
};

// note: nếu object type dùng đi dùng lại nhiều lần, NÊN tách ra `type` hoặc `interface`
// (xem phần Aliases & Interfaces bên dưới) thay vì viết lặp lại kiểu inline như trên.
```

---

## 9. Enums

```typescript
// note: enum dùng khi có tập giá trị cố định, hữu hạn, đặt tên có ý nghĩa hơn số/string "ma thuật"
enum TrangThaiDonHang {
  ChoXuLy,     // = 0 (mặc định bắt đầu từ 0)
  DangGiao,    // = 1
  DaGiao,      // = 2
  DaHuy,       // = 3
}
let trangThai: TrangThaiDonHang = TrangThaiDonHang.DangGiao;
console.log(trangThai); // 1

// Enum có gán giá trị tường minh
enum MaLoi {
  KhongTimThay = 404,
  LoiServer = 500,
  KhongCoQuyen = 403,
}

// String enum - note: mình thích dùng string enum hơn vì log/debug dễ đọc hơn số
enum HuongDi {
  Len = "LEN",
  Xuong = "XUONG",
  Trai = "TRAI",
  Phai = "PHAI",
}
console.log(HuongDi.Len); // "LEN"

// const enum - note: nhẹ hơn vì bị "xóa" lúc build, không sinh object JS thật, dùng khi cần tối ưu
const enum Mau {
  Do,
  Xanh,
}
```

---

## 10. Type Alias & Interface (Aliases & Interfaces)

```typescript
// Type alias - đặt tên cho 1 kiểu bất kỳ
type ID = string | number;
type NguoiDung = {
  ten: string;
  tuoi: number;
};

// Interface - cũng để định nghĩa hình dạng object, hay dùng cho class/OOP
interface INguoiDung {
  ten: string;
  tuoi: number;
  chao?(): void; // phương thức optional
}

const u1: NguoiDung = { ten: "An", tuoi: 25 };
const u2: INguoiDung = { ten: "Bình", tuoi: 30 };

// note: khác biệt lớn nhất mình hay áp dụng:
// - Interface có thể "mở rộng" nhiều lần (declaration merging) và dùng `extends` để kế thừa -> hợp OOP/class
// - Type alias linh hoạt hơn: có thể union, intersection, mapped type... mà interface không làm được
// -> Quy tắc mình theo: định nghĩa object/class dùng interface, còn union/kiểu phức tạp dùng type.

interface Động_Vật {
  ten: string;
}
interface Chó extends Động_Vật {
  giong: string;
}

type Diem = { x: number; y: number };
type Diem3D = Diem & { z: number }; // intersection type - gộp 2 type lại
```

---

## 11. Union Types

```typescript
// Union type = giá trị có thể là 1 TRONG NHIỀU kiểu khác nhau
let id: string | number;
id = "abc123"; // ✅
id = 123;      // ✅
// id = true;  // ❌

function inID(id: string | number) {
  // note: bắt buộc phải kiểm tra kiểu trước khi dùng method riêng của từng kiểu (type narrowing)
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id.toFixed(2));
  }
}

// Union với nhiều literal cụ thể - note: rất hay dùng cho props kiểu "chỉ được phép 1 trong các giá trị này"
type KichThuoc = "small" | "medium" | "large";
let size: KichThuoc = "medium";
// size = "big"; // ❌ lỗi vì không nằm trong danh sách cho phép
```