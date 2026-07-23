
# PHẦN 1: JAVASCRIPT CƠ BẢN

## 1. Giới thiệu JavaScript

JavaScript là ngôn ngữ lập trình thông dịch (interpreted), hướng đối tượng dựa trên prototype, chạy được trên trình duyệt (client-side) và server (Node.js).

**3 đặc điểm cốt lõi:**
- Weakly-typed (kiểu dữ liệu động, không cần khai báo kiểu)
- Single-threaded nhưng hỗ trợ bất đồng bộ qua Event Loop
- Chạy được ở khắp nơi: trình duyệt, server, mobile, IoT

```javascript
// Chương trình JS đầu tiên
console.log("Xin chào, JavaScript!");
```

---

## 2. Nhúng JavaScript vào trang web (Where To)

**Cách 1 – Inline (trong thẻ HTML):**
```html
<button onclick="alert('Đã click!')">Click tôi</button>
```

**Cách 2 – Internal (trong thẻ `<script>`):**
```html
<!DOCTYPE html>
<html>
<body>
  <h1 id="demo">Xin chào</h1>
  <script>
    document.getElementById("demo").innerText = "Đã thay đổi bằng JS";
  </script>
</body>
</html>
```

**Cách 3 – External (file .js riêng):**
```html
<script src="app.js"></script>
```
```javascript
// app.js
console.log("File JS ngoài đã chạy");
```

> 💡 Nên đặt thẻ `<script>` trước `</body>` hoặc dùng thuộc tính `defer` để không chặn tải trang.

```html
<script src="app.js" defer></script>
```

---

## 3. Xuất dữ liệu (Output)

```javascript
console.log("In ra console (dành cho dev)");
alert("Hiển thị hộp thoại popup");
document.write("Ghi trực tiếp vào trang (ít dùng)");
document.getElementById("demo").innerHTML = "Ghi vào phần tử HTML";
```

---

## 4. Cú pháp cơ bản (Syntax)

```javascript
// Khai báo biến
var a = 10;      // phạm vi function, có thể khai báo lại (nên tránh dùng)
let b = 20;      // phạm vi block, khuyến nghị dùng
const c = 30;    // hằng số, không thể gán lại

// Chú thích
// Đây là chú thích 1 dòng
/* Đây là
   chú thích nhiều dòng */

// Dấu chấm phẩy (không bắt buộc nhưng nên dùng)
let x = 5; let y = 10;
```

---

## 5. Toán tử (Operators)

```javascript
// Số học
let tong = 5 + 3;      // 8
let hieu = 5 - 3;      // 2
let tich = 5 * 3;      // 15
let thuong = 5 / 3;    // 1.666...
let du = 5 % 3;        // 2
let luyThua = 5 ** 2;  // 25

// Gán
let n = 10;
n += 5;   // n = 15
n -= 2;   // n = 13

// So sánh
console.log(5 == "5");    // true  (chỉ so giá trị)
console.log(5 === "5");   // false (so cả kiểu dữ liệu - nên dùng cái này)
console.log(5 !== "5");   // true

// Logic
let ketQua = (5 > 3) && (2 < 4); // true
let ketQua2 = (5 > 3) || (2 > 4); // true
let phuDinh = !(5 > 3); // false

// Toán tử ba ngôi (ternary)
let tuoi = 20;
let loai = tuoi >= 18 ? "Người lớn" : "Trẻ em";
```

---

## 6. Cấu trúc điều kiện (If Conditions)

```javascript
let diem = 8;

if (diem >= 8) {
  console.log("Giỏi");
} else if (diem >= 6.5) {
  console.log("Khá");
} else if (diem >= 5) {
  console.log("Trung bình");
} else {
  console.log("Yếu");
}

// Switch case
let mau = "do";
switch (mau) {
  case "do":
    console.log("Màu đỏ");
    break;
  case "xanh":
    console.log("Màu xanh");
    break;
  default:
    console.log("Không xác định");
}
```

---

## 7. Vòng lặp (Loops)

```javascript
// for
for (let i = 0; i < 5; i++) {
  console.log("for:", i);
}

// while
let i = 0;
while (i < 5) {
  console.log("while:", i);
  i++;
}

// do...while (chạy ít nhất 1 lần)
let j = 0;
do {
  console.log("do-while:", j);
  j++;
} while (j < 5);

// for...in (duyệt key của object)
const nguoiDung = { ten: "An", tuoi: 25 };
for (let key in nguoiDung) {
  console.log(key, "=", nguoiDung[key]);
}

// for...of (duyệt giá trị của mảng/iterable)
const arr = [10, 20, 30];
for (let value of arr) {
  console.log(value);
}

// break và continue
for (let i = 0; i < 10; i++) {
  if (i === 3) continue; // bỏ qua i = 3
  if (i === 6) break;    // dừng khi i = 6
  console.log(i);
}
```

---

## 8. Chuỗi (Strings)

```javascript
let ho = "Nguyễn";
let ten = "Văn A";

// Nối chuỗi
let hoTen = ho + " " + ten;

// Template literals (khuyến nghị)
let hoTen2 = `${ho} ${ten}`;
console.log(`Xin chào, ${hoTen2}!`);

// Các phương thức phổ biến
let s = "  Hello World  ";
console.log(s.length);            // 15
console.log(s.trim());            // "Hello World"
console.log(s.toUpperCase());     // "  HELLO WORLD  "
console.log(s.toLowerCase());     // "  hello world  "
console.log(s.includes("World")); // true
console.log(s.replace("Hello", "Hi"));
console.log(s.trim().split(" ")); // ["Hello", "World"]
console.log("Hello".slice(0, 3)); // "Hel"
console.log("Hello".indexOf("l"));// 2
console.log("Hello".repeat(2));   // "HelloHello"
```

---

## 9. Số (Numbers)

```javascript
let soNguyen = 100;
let soThuc = 3.14;
let soAm = -5;

console.log(Number("123"));        // 123
console.log(Number("abc"));        // NaN
console.log(isNaN("abc"));         // true
console.log((3.14159).toFixed(2)); // "3.14"
console.log(parseInt("100px"));    // 100
console.log(parseFloat("3.14m"));  // 3.14
console.log(Number.isInteger(10)); // true
console.log(Number.MAX_SAFE_INTEGER);
```

---

## 10. Hàm (Functions)

```javascript
// Function declaration
function chao(ten) {
  return `Xin chào, ${ten}!`;
}

// Function expression
const chao2 = function(ten) {
  return `Chào, ${ten}`;
};

// Arrow function
const chao3 = (ten) => `Hi, ${ten}`;
const cong = (a, b) => a + b;

// Tham số mặc định
function nhan(a, b = 1) {
  return a * b;
}

// Tham số dạng rest (...args)
function tongNhieuSo(...nums) {
  return nums.reduce((tong, n) => tong + n, 0);
}
console.log(tongNhieuSo(1, 2, 3, 4)); // 10

console.log(chao("An"));
console.log(cong(2, 3));
```

---

## 11. Timers (setTimeout / setInterval)

```javascript
// Chạy 1 lần sau 2 giây
const idTimeout = setTimeout(() => {
  console.log("Đã chạy sau 2 giây");
}, 2000);

// Chạy lặp lại mỗi 1 giây
let dem = 0;
const idInterval = setInterval(() => {
  dem++;
  console.log("Đếm:", dem);
  if (dem === 5) clearInterval(idInterval); // dừng lặp
}, 1000);

// Hủy timeout nếu cần
clearTimeout(idTimeout);
```

---

## 12. Đối tượng (Objects)

```javascript
const sinhVien = {
  ten: "Lan",
  tuoi: 20,
  monHoc: ["Toán", "Lý"],
  chao: function() {
    console.log(`Xin chào, tôi là ${this.ten}`);
  }
};

console.log(sinhVien.ten);       // Lan
console.log(sinhVien["tuoi"]);   // 20
sinhVien.chao();                 // Xin chào, tôi là Lan

// Thêm/sửa/xóa thuộc tính
sinhVien.diem = 9;
delete sinhVien.tuoi;

// Duyệt object
for (let key in sinhVien) {
  console.log(key, sinhVien[key]);
}

// Object.keys / values / entries
console.log(Object.keys(sinhVien));
console.log(Object.values(sinhVien));
console.log(Object.entries(sinhVien));

// Destructuring (giải cấu trúc)
const { ten, monHoc } = sinhVien;
```

---

## 13. Phạm vi biến (Scope)

```javascript
let toanCuc = "Tôi là biến toàn cục";

function vidu() {
  let cucBo = "Tôi là biến cục bộ";
  console.log(toanCuc); // truy cập được
  console.log(cucBo);
}

// Block scope với let/const
{
  let x = 10;
  console.log(x); // 10
}
// console.log(x); // Lỗi: x không tồn tại ngoài block

// var KHÔNG có block scope (dễ gây lỗi)
if (true) {
  var y = 5;
}
console.log(y); // 5 (vẫn truy cập được - đây là lý do nên tránh var)
```

---

## 14. Ngày giờ (Dates)

```javascript
const now = new Date();
console.log(now);                    // Ngày giờ hiện tại
console.log(now.getFullYear());      // Năm
console.log(now.getMonth() + 1);     // Tháng (0-11, cần +1)
console.log(now.getDate());          // Ngày trong tháng
console.log(now.getDay());           // Thứ trong tuần (0=CN)
console.log(now.getHours());
console.log(now.getMinutes());

const ngayCuThe = new Date("2026-07-23");
console.log(ngayCuThe.toDateString());

// Tính khoảng cách giữa 2 ngày
const d1 = new Date("2026-01-01");
const d2 = new Date("2026-07-23");
const soNgay = (d2 - d1) / (1000 * 60 * 60 * 24);
console.log(soNgay, "ngày");
```

---

## 15. Mảng (Arrays)

```javascript
const arr = [1, 2, 3, 4, 5];

// Thêm/xóa
arr.push(6);        // thêm cuối
arr.pop();           // xóa cuối
arr.unshift(0);      // thêm đầu
arr.shift();         // xóa đầu
console.log(arr);

// Các phương thức xử lý mảng quan trọng
const nums = [1, 2, 3, 4, 5];

console.log(nums.map(n => n * 2));        // [2,4,6,8,10]
console.log(nums.filter(n => n % 2 === 0)); // [2,4]
console.log(nums.reduce((tong, n) => tong + n, 0)); // 15
console.log(nums.find(n => n > 3));       // 4
console.log(nums.some(n => n > 4));       // true
console.log(nums.every(n => n > 0));      // true
console.log(nums.includes(3));            // true
console.log(nums.sort((a, b) => b - a));  // [5,4,3,2,1]
console.log(nums.reverse());
console.log(nums.slice(1, 3));            // cắt mảng, không đổi mảng gốc
console.log(nums.splice(1, 2));           // cắt và xóa khỏi mảng gốc
console.log(nums.join("-"));              // nối thành chuỗi
console.log(Array.isArray(nums));         // true
```

---

## 16. Set

```javascript
const mySet = new Set([1, 2, 2, 3, 3, 3]);
console.log(mySet);        // Set { 1, 2, 3 } - tự loại bỏ trùng lặp
mySet.add(4);
mySet.delete(1);
console.log(mySet.has(2)); // true
console.log(mySet.size);   // 3

// Chuyển Set sang Array
const arrFromSet = [...mySet];
```

---

## 17. Map

```javascript
const myMap = new Map();
myMap.set("ten", "An");
myMap.set("tuoi", 25);

console.log(myMap.get("ten")); // "An"
console.log(myMap.has("tuoi"));// true
console.log(myMap.size);       // 2

myMap.delete("tuoi");

// Duyệt Map
for (let [key, value] of myMap) {
  console.log(key, value);
}
```

---

## 18. Iterations (Duyệt dữ liệu)

```javascript
const arr = [10, 20, 30];

arr.forEach((value, index) => {
  console.log(index, value);
});

// Generator function - tự tạo iterator
function* demSo(max) {
  for (let i = 1; i <= max; i++) {
    yield i;
  }
}
for (let so of demSo(3)) {
  console.log(so); // 1, 2, 3
}
```

---

## 19. Math

```javascript
console.log(Math.round(4.6));   // 5
console.log(Math.floor(4.9));   // 4
console.log(Math.ceil(4.1));    // 5
console.log(Math.abs(-5));      // 5
console.log(Math.pow(2, 3));    // 8
console.log(Math.sqrt(16));     // 4
console.log(Math.max(1, 5, 3)); // 5
console.log(Math.min(1, 5, 3)); // 1
console.log(Math.random());     // số ngẫu nhiên 0-1

// Số ngẫu nhiên trong khoảng [min, max]
function random(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
console.log(random(1, 100));
```

---

## 20. Biểu thức chính quy (RegExp)

```javascript
const pattern = /^[a-z0-9_]{3,16}$/; // username hợp lệ

console.log(pattern.test("an_van_123")); // true
console.log(pattern.test("A!"));         // false

const str = "Email: contact@example.com";
const emailRegex = /[\w.-]+@[\w.-]+\.\w+/;
console.log(str.match(emailRegex)[0]);   // contact@example.com

// Thay thế bằng regex
console.log("Hello World".replace(/o/g, "0")); // "Hell0 W0rld"
```

---

## 21. Kiểu dữ liệu (Data Types)

```javascript
console.log(typeof "chuỗi");     // "string"
console.log(typeof 123);         // "number"
console.log(typeof true);        // "boolean"
console.log(typeof undefined);   // "undefined"
console.log(typeof null);        // "object" (đây là 1 lỗi lịch sử của JS)
console.log(typeof {});          // "object"
console.log(typeof []);          // "object"
console.log(typeof function(){});// "function"
console.log(typeof Symbol());    // "symbol"
console.log(typeof 10n);         // "bigint"

// Kiểm tra Object vs Array chính xác
console.log(Array.isArray([1,2,3])); // true
```

---

## 22. Xử lý lỗi (Errors)

```javascript
try {
  let x = y + 1; // y không tồn tại -> lỗi
} catch (error) {
  console.log("Đã bắt lỗi:", error.message);
} finally {
  console.log("Luôn chạy dù có lỗi hay không");
}

// Tự ném lỗi
function chia(a, b) {
  if (b === 0) {
    throw new Error("Không thể chia cho 0");
  }
  return a / b;
}

try {
  chia(10, 0);
} catch (e) {
  console.log(e.message);
}
```

---

## 23. Debugging

```javascript
console.log("Giá trị biến:", bien);
console.table([{ten: "An", tuoi: 20}, {ten: "Bình", tuoi: 22}]);
console.error("Đây là lỗi");
console.warn("Đây là cảnh báo");
debugger; // dừng chương trình tại đây khi mở DevTools (F12)
```

---

## 24. Quy ước viết code (Style Guide)

```javascript
// Nên: đặt tên biến rõ nghĩa, dùng camelCase
let soLuongSanPham = 10;

// Nên: dùng const/let, tránh var
const PI = 3.14;

// Nên: dùng === thay vì ==
if (a === b) { }

// Nên: thụt lề nhất quán (2 hoặc 4 space)
function tinhTong(a, b) {
  return a + b;
}
```

---

## 25. HTML DOM

```javascript
// Chọn phần tử
const el = document.getElementById("demo");
const els = document.querySelectorAll(".item");

// Thay đổi nội dung
el.innerHTML = "<b>Nội dung mới</b>";
el.innerText = "Chỉ text thuần";

// Thay đổi thuộc tính / style
el.setAttribute("class", "active");
el.style.color = "red";

// Tạo và thêm phần tử mới
const newDiv = document.createElement("div");
newDiv.textContent = "Div mới được tạo bằng JS";
document.body.appendChild(newDiv);
```

---

## 26. Sự kiện HTML (Events)

```javascript
const btn = document.getElementById("myBtn");

btn.addEventListener("click", function() {
  console.log("Nút đã được click");
});

document.addEventListener("keydown", (e) => {
  console.log("Phím vừa nhấn:", e.key);
});

// Xóa event listener
function handler() { console.log("click"); }
btn.addEventListener("click", handler);
btn.removeEventListener("click", handler);
```