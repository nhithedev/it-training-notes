

## 27. Hàm nâng cao (Functions Advanced)

```javascript
// Closure - hàm "nhớ" biến của phạm vi cha
function taoBoDem() {
  let dem = 0;
  return function() {
    dem++;
    return dem;
  };
}
const dem1 = taoBoDem();
console.log(dem1()); // 1
console.log(dem1()); // 2

// Higher-order function - hàm nhận/trả về hàm khác
function apDung(fn, gia_tri) {
  return fn(gia_tri);
}
console.log(apDung(x => x * 2, 5)); // 10

// Callback function
function xuLy(data, callback) {
  console.log("Đang xử lý...");
  callback(data);
}
xuLy("Xong", (ket_qua) => console.log(ket_qua));

// Đệ quy (Recursion)
function giaiThua(n) {
  if (n <= 1) return 1;
  return n * giaiThua(n - 1);
}
console.log(giaiThua(5)); // 120

// IIFE - Hàm tự thực thi ngay
(function() {
  console.log("Chạy ngay lập tức");
})();
```

---

## 28. Đối tượng nâng cao (Objects Advanced)

```javascript
// Prototype - cơ chế kế thừa của JS
function Nguoi(ten) {
  this.ten = ten;
}
Nguoi.prototype.chao = function() {
  return `Xin chào, tôi là ${this.ten}`;
};
const p1 = new Nguoi("An");
console.log(p1.chao());

// Getter và Setter
const taiKhoan = {
  _soDu: 1000,
  get soDu() {
    return `${this._soDu} VNĐ`;
  },
  set soDu(giaTri) {
    if (giaTri < 0) {
      console.log("Số dư không thể âm");
    } else {
      this._soDu = giaTri;
    }
  }
};
console.log(taiKhoan.soDu); // dùng getter
taiKhoan.soDu = 2000;       // dùng setter

// Object.freeze - đóng băng object không cho sửa đổi
const config = Object.freeze({ apiUrl: "https://api.example.com" });
config.apiUrl = "hack"; // không có tác dụng

// Object.assign - sao chép/gộp object
const obj1 = { a: 1 };
const obj2 = { b: 2 };
const merged = Object.assign({}, obj1, obj2); // { a: 1, b: 2 }

// Spread operator với object
const merged2 = { ...obj1, ...obj2 };
```

---

## 29. Lớp (Classes)

```javascript
class Dong_Vat {
  constructor(ten, tuoi) {
    this.ten = ten;
    this.tuoi = tuoi;
  }

  keu() {
    console.log(`${this.ten} đang kêu`);
  }

  // Getter
  get thongTin() {
    return `${this.ten}, ${this.tuoi} tuổi`;
  }

  // Phương thức tĩnh
  static tao(ten) {
    return new Dong_Vat(ten, 0);
  }
}

// Kế thừa
class Cho extends Dong_Vat {
  constructor(ten, tuoi, giong) {
    super(ten, tuoi); // gọi constructor của lớp cha
    this.giong = giong;
  }

  keu() {
    console.log(`${this.ten} sủa: Gâu gâu!`);
  }
}

const meo = new Dong_Vat("Miu", 2);
const cho1 = new Cho("Lu", 3, "Husky");

meo.keu();  // Miu đang kêu
cho1.keu(); // Lu sủa: Gâu gâu! (đã override phương thức cha)
console.log(cho1.thongTin);
console.log(cho1 instanceof Dong_Vat); // true

// Thuộc tính/phương thức private (dùng #)
class TaiKhoanNganHang {
  #soDu = 0;
  napTien(soTien) {
    this.#soDu += soTien;
  }
  get soDu() {
    return this.#soDu;
  }
}
const tk = new TaiKhoanNganHang();
tk.napTien(500);
console.log(tk.soDu); // 500
// console.log(tk.#soDu); // Lỗi - không truy cập được từ bên ngoài
```

---

## 30. JSON

```javascript
// Object -> JSON string
const nguoiDung = { ten: "An", tuoi: 25, hocSinh: true };
const jsonString = JSON.stringify(nguoiDung);
console.log(jsonString); // '{"ten":"An","tuoi":25,"hocSinh":true}'

// JSON string -> Object
const objTuJson = JSON.parse(jsonString);
console.log(objTuJson.ten); // An

// stringify với định dạng đẹp (indent)
console.log(JSON.stringify(nguoiDung, null, 2));

// Dùng khi lưu vào localStorage (JS chỉ lưu được string)
localStorage.setItem("user", JSON.stringify(nguoiDung));
const userDaLuu = JSON.parse(localStorage.getItem("user"));
```

---

## 31. Bất đồng bộ (Asynchronous JS)

```javascript
// Callback truyền thống (dễ gây "callback hell")
function layDuLieu(callback) {
  setTimeout(() => {
    callback("Dữ liệu đã tải xong");
  }, 1000);
}
layDuLieu((data) => console.log(data));

// Promise
function layDuLieuPromise() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const thanhCong = true;
      if (thanhCong) {
        resolve("Dữ liệu thành công");
      } else {
        reject("Có lỗi xảy ra");
      }
    }, 1000);
  });
}

layDuLieuPromise()
  .then((data) => console.log(data))
  .catch((err) => console.log(err))
  .finally(() => console.log("Hoàn tất"));

// Async/Await (cú pháp hiện đại, dễ đọc hơn Promise chain)
async function chinhAsync() {
  try {
    const data = await layDuLieuPromise();
    console.log("Nhận được:", data);
  } catch (err) {
    console.log("Lỗi:", err);
  }
}
chinhAsync();

// Fetch API - gọi API thực tế
async function layUsers() {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");
    const users = await response.json();
    console.log(users);
  } catch (err) {
    console.log("Lỗi khi gọi API:", err);
  }
}

// Promise.all - chạy nhiều promise song song
async function chayNhieuPromise() {
  const [a, b] = await Promise.all([
    layDuLieuPromise(),
    layDuLieuPromise()
  ]);
  console.log(a, b);
}
```

---

## 32. Module (JS Modules)

```javascript
// file: toanHoc.js
export function cong(a, b) {
  return a + b;
}
export const PI = 3.14159;
export default function tru(a, b) {
  return a - b;
}

// file: main.js
import tru, { cong, PI } from "./toanHoc.js";
console.log(cong(2, 3));
console.log(tru(5, 2));
console.log(PI);

// Import toàn bộ module dưới 1 tên
import * as ToanHoc from "./toanHoc.js";
console.log(ToanHoc.cong(1, 1));
```
```html
<!-- Cần khai báo type="module" khi dùng trong HTML -->
<script type="module" src="main.js"></script>
```

---

## 33. Meta Programming & Proxy

```javascript
const nguoiDung = { ten: "An", tuoi: 25 };

const handler = {
  get(target, thuocTinh) {
    console.log(`Đang đọc thuộc tính: ${thuocTinh}`);
    return target[thuocTinh];
  },
  set(target, thuocTinh, giaTri) {
    console.log(`Đang gán ${thuocTinh} = ${giaTri}`);
    target[thuocTinh] = giaTri;
    return true;
  }
};

const proxy = new Proxy(nguoiDung, handler);
proxy.ten;          // log: "Đang đọc thuộc tính: ten"
proxy.tuoi = 30;    // log: "Đang gán tuoi = 30"

// Reflect - API bổ trợ cho Proxy
console.log(Reflect.has(nguoiDung, "ten")); // true
console.log(Reflect.ownKeys(nguoiDung));    // ["ten", "tuoi"]
```

---

## 34. Typed Arrays

```javascript
// Mảng nhị phân hiệu năng cao, dùng khi xử lý dữ liệu nhị phân/ảnh/âm thanh
const buffer = new ArrayBuffer(16); // 16 byte
const view = new Int32Array(buffer);
view[0] = 42;
console.log(view[0]); // 42
console.log(view.length); // 4 (16 byte / 4 byte mỗi Int32)

const floatArr = new Float64Array([1.5, 2.5, 3.5]);
console.log(floatArr[1]); // 2.5
```

---

## 35. DOM Navigation

```javascript
const el = document.getElementById("demo");

console.log(el.parentElement);      // phần tử cha
console.log(el.children);           // danh sách phần tử con
console.log(el.firstElementChild);  // con đầu tiên
console.log(el.lastElementChild);   // con cuối cùng
console.log(el.nextElementSibling); // phần tử anh/em kế tiếp
console.log(el.previousElementSibling);
```

---

## 36. Browser API (window)

```javascript
console.log(window.innerWidth, window.innerHeight); // kích thước cửa sổ
console.log(window.location.href);   // URL hiện tại
window.location.reload();            // tải lại trang

// localStorage - lưu dữ liệu vĩnh viễn (đến khi xóa)
localStorage.setItem("theme", "dark");
console.log(localStorage.getItem("theme"));
localStorage.removeItem("theme");

// sessionStorage - lưu dữ liệu chỉ trong phiên làm việc
sessionStorage.setItem("tempData", "123");

// history - lịch sử duyệt web
window.history.back();
window.history.forward();
```

---

## 37. Web API

```javascript
// Geolocation API
navigator.geolocation.getCurrentPosition((pos) => {
  console.log(pos.coords.latitude, pos.coords.longitude);
});

// Notification API
if (Notification.permission === "granted") {
  new Notification("Xin chào!", { body: "Đây là thông báo" });
}

// Fetch API (đã dùng ở phần Async)
fetch("https://api.example.com/data")
  .then(res => res.json())
  .then(data => console.log(data));

// Clipboard API
navigator.clipboard.writeText("Đã copy văn bản này");
```

---

## 38. Đồ họa (Graphics - Canvas)

```html
<canvas id="myCanvas" width="300" height="150"></canvas>
<script>
  const canvas = document.getElementById("myCanvas");
  const ctx = canvas.getContext("2d");

  // Vẽ hình chữ nhật
  ctx.fillStyle = "blue";
  ctx.fillRect(20, 20, 100, 60);

  // Vẽ đường tròn
  ctx.beginPath();
  ctx.arc(200, 75, 40, 0, Math.PI * 2);
  ctx.fillStyle = "red";
  ctx.fill();

  // Vẽ text
  ctx.font = "20px Arial";
  ctx.fillStyle = "black";
  ctx.fillText("Canvas Demo", 20, 120);
</script>
```