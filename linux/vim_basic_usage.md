# Các câu lệnh cơ bản trong Vim (Ubuntu)

## Xóa dòng

-   **Nhấn**: `dd`  
    → Xóa 1 dòng tại con trỏ.

## Hồi phục dòng (Undo)

-   **Nhấn**: `u`  
    → Hủy thao tác gần nhất (Undo).

## Sao chép dòng (Copy)

-   **Nhấn**: `yy` hoặc `Y`  
    → Sao chép dòng hiện tại.

## Dán dòng (Paste)

-   **Nhấn**: `p`  
    → Dán nội dung đã sao chép hoặc cắt sau dòng hiện tại.
-   **Nhấn**: `P`  
    → Dán trước dòng hiện tại.

## Tìm kiếm

-   **Nhấn**: `/` và nhập từ khóa  
    → Ví dụ: `/main` rồi nhấn `Enter` để tìm từ “main”.

## Lưu và thoát

-   **Nhấn**: `:x` hoặc `:wq`  
    → Lưu và thoát Vim.

## Thoát mà không lưu

-   **Nhấn**: `:q!`  
    → Thoát mà không lưu thay đổi.

## Một số lệnh bổ sung hữu ích:

### Cắt dòng

-   **Nhấn**: `dd`  
    → Vừa xóa vừa lưu vào bộ nhớ tạm (giống như cắt).

### Lưu mà không thoát

-   **Nhấn**: `:w`  
    → Lưu file nhưng không thoát.

### Di chuyển đến dòng cụ thể

-   **Nhấn**: `:n`  
    → Trong đó `n` là số dòng, ví dụ `:10` sẽ đến dòng thứ 10.

### Vào chế độ chèn (Insert mode)

-   **Nhấn**: `i`  
    → Bắt đầu chèn văn bản trước vị trí con trỏ.

-   **Nhấn**: `a`  
    → Bắt đầu chèn văn bản sau vị trí con trỏ.

-   **Nhấn**: `o`  
    → Mở dòng mới bên dưới và vào chế độ chèn.

---

**Ghi chú**: Vim có nhiều chế độ, các lệnh trên thường được dùng trong **Normal Mode** (ấn `Esc` để quay lại chế độ này nếu đang ở chế độ chèn).
