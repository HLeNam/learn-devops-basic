# 🚀 Tư Duy Để Triển Khai Dự Án

> **Mấu chốt của DevOps là triển khai càng nhiều loại dự án sẽ càng giá trị**

---

## 📦 Dự Án Được Chia Làm 2 Loại Chính:

-   `Frontend`
-   `Backend`

---

## 🧰 Công Cụ Cần Thiết Cho Từng Dự Án

Để triển khai **bất kỳ dự án nào**, cần phải có **công cụ tương ứng**:

-   Muốn triển khai `nodejs` thì cần cài `nodejs`
-   Muốn triển khai `java` thì cần cài `java`
-   Muốn triển khai `php` thì cần cài `php`

> 🔍 **Lưu ý**: Phiên bản công cụ phải **>= yêu cầu của dự án**

**Ví dụ**:

-   Dự án yêu cầu `node` phiên bản 16 thì phải cài phiên bản `>= 16`

---

## 📂 File Trong Dự Án

Bất kỳ dự án nào cũng chia ra làm **3 loại file chính**:

1. **File chức năng**:  
   → Là các file code thực hiện chức năng như thêm giỏ hàng, tạo user,...  
   → 🛑 _Với DevOps: chưa cần quan tâm_

2. **File cấu hình**:  
   → Là các file chứa thông tin như:

    - Kết nối database
    - Port chạy của dự án
    - Kết nối đến dự án khác  
      → ✅ _Đây chính xác là các file DevOps cần quan tâm_

3. **File khác**:  
   → Ví dụ: `README.md` (mô tả hệ thống)  
   → 🛑 _Cũng chưa cần quan tâm đến_

---

## ⚙️ Các Bước Triển Khai Dự Án

Mọi dự án đều có 2 bước chính:

1. **Build**  
   → Chạy một câu lệnh nào đó để tạo ra thư mục hoặc file

2. **Run**  
   → Chạy từ file hoặc thư mục đó để khởi động dự án

---

## ❓ Cần Trả Lời Được Các Câu Hỏi Sau

-   📌 Dự án đó **cần công cụ gì?**
-   📌 **File cấu hình** nằm ở đâu? là file nào?
-   📌 Làm sao để **Build dự án**?
-   📌 Và làm sao để **Run** nó lên?

---

## ⚠️ Chú Ý Khi Triển Khai

1. ✅ Với **mỗi dự án** sẽ có **một thư mục làm việc riêng**.

2. ✅ Nên dùng **user riêng cho từng dự án** thay vì user `root`.

---

### ❗ Vì sao không dùng `root` để chạy dự án?

-   User `root` có **quyền cao nhất** trong hệ thống
-   Nếu chạy dự án bằng `root`, khi bị xâm nhập thì:
    -   Hacker sẽ chiếm được quyền thao tác toàn hệ thống
    -   Gây nguy hiểm nghiêm trọng cho toàn bộ hệ thống
-   ➤ Cách an toàn:
    -   Dùng một **user riêng biệt**
    -   User đó **chỉ có quyền trong thư mục làm việc của dự án**
    -   Khi dự án bị xâm nhập, chỉ có **thư mục đó bị ảnh hưởng**

---
