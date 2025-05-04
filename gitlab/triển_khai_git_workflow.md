# Triển khai Git Workflow (Git Flow)

## 1. Môi trường triển khai

Trong một quy trình phát triển phần mềm tiêu chuẩn, thường có 3 môi trường chính:

-   **Development**: Môi trường phát triển — nơi lập trình viên viết và thử nghiệm các tính năng mới.
-   **Staging**: Môi trường kiểm thử (gần giống production) — nơi tester thực hiện kiểm thử, kiểm tra tích hợp và chấp nhận.
-   **Production**: Môi trường chạy thực tế — dành cho người dùng cuối sử dụng hệ thống.

## 2. Các nhánh trong Git Flow

Git Flow sử dụng nhiều nhánh để tổ chức quy trình phát triển:

| Tên nhánh           | Mục đích                                        | Tạo ra từ | Merge vào                             |
| ------------------- | ----------------------------------------------- | --------- | ------------------------------------- |
| `main`              | Chứa mã nguồn ổn định đã phát hành (production) | -         | Không thường xuyên cập nhật trực tiếp |
| `develop`           | Chứa mã nguồn đang phát triển (development)     | `main`    | `release`, `main`                     |
| `feature/<tên>`     | Chứa các tính năng mới (chưa hoàn thiện)        | `develop` | `develop`                             |
| `release/<version>` | Chuẩn bị cho bản phát hành (testing/staging)    | `develop` | `main`, `develop`                     |
| `hotfix/<tên>`      | Sửa lỗi khẩn cấp trong production               | `main`    | `main`, `develop`                     |

### Quy trình tổng thể:

1. **Tạo nhánh feature**:

    ```bash
    git checkout develop
    git checkout -b feature/ten-tinh-nang
    ```

2. **Hoàn thiện tính năng → merge vào develop**:

    ```bash
    git checkout develop
    git merge feature/ten-tinh-nang
    git branch -d feature/ten-tinh-nang
    ```

3. **Tạo bản phát hành** (testing/staging):

    ```bash
    git checkout develop
    git checkout -b release/v1.0.0
    ```

4. **Sau khi test ổn → merge vào main và gắn tag**:

    ```bash
    git checkout main
    git merge release/v1.0.0
    git tag -a v1.0.0 -m "Release version 1.0.0"
    ```

5. **Cập nhật lại develop với các thay đổi trong release (nếu có)**:

    ```bash
    git checkout develop
    git merge release/v1.0.0
    git branch -d release/v1.0.0
    ```

6. **Sửa lỗi khẩn cấp (hotfix)**:

    ```bash
    git checkout main
    git checkout -b hotfix/ten-loi
    ```

    Sau khi sửa xong:

    ```bash
    git checkout main
    git merge hotfix/ten-loi
    git tag -a v1.0.1 -m "Hotfix version 1.0.1"

    git checkout develop
    git merge hotfix/ten-loi
    git branch -d hotfix/ten-loi
    ```

## 3. Gắn tag cho release

Tag thường được dùng để đánh dấu các bản phát hành:

-   Tạo tag:

    ```bash
    git tag -a v1.0.0 -m "Release version 1.0.0"
    ```

-   Đẩy tag lên remote:

    ```bash
    git push origin v1.0.0
    ```

-   Liệt kê các tag:
    ```bash
    git tag
    ```

## 4. Tổng kết

Mô hình Git Flow giúp bạn:

-   Quản lý rõ ràng quy trình phát triển nhiều tính năng.
-   Kiểm soát tốt quá trình phát hành và sửa lỗi.
-   Dễ dàng rollback hoặc theo dõi phiên bản bằng `tag`.

> 💡 Lưu ý: Với các nhóm nhỏ hoặc dự án đơn giản, có thể dùng mô hình đơn giản hơn như GitHub Flow hoặc Trunk-based Development.
