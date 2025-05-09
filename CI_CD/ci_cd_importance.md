# Tại sao CI/CD lại quan trọng như vậy?

CI/CD được xem là quy trình không thể thiếu nếu muốn phát triển doanh nghiệp.

Cũng như là mảng kiến thức giúp cho bất cứ ai trong ngành lập trình sẽ đều nổi bật hơn người khác, kể cả khi ta không làm chuyên môn DevOps.

**Ví dụ:**

-   Ta làm Dev hay System, biết thêm kiến thức DevOps (hay cụ thể hơn là CI/CD) và áp dụng vào doanh nghiệp, đã là góp phần rất nhiều trong việc tối ưu tài nguyên doanh nghiệp.
-   Khi ứng tuyển, việc có kinh nghiệm CI/CD sẽ giúp CV nổi bật hơn nhiều.
-   Và sau này, nếu muốn chuyển sang làm DevOps, đã có kiến thức và kinh nghiệm trước đó, việc chuyển ngành sẽ thuận lợi hơn.

## Trường hợp thực tế

Trong doanh nghiệp:

-   Dev code dự án --> Ops/System triển khai.
-   Với 1-2 source code thì đơn giản.
-   Nhưng với 20 dự án trên Git, bao nhiêu System mới triển khai kịp?
-   Mỗi dự án thường có 3 môi trường:

    -   `Development` dành cho dev code và commit hàng giờ.
    -   `Staging` (hay Testing) dành cho tester kiểm thử.
    -   `Production` dành cho end-user.

**Vấn đề:**

-   Dev commit code mà phải đợi System triển khai thì mới xem kết quả trên server --> Rất chậm.
-   System cũng khổ: deploy thủ công tựng dự án, nguy cơ gõ sai cao, mất nhiều thời gian.

**Giải pháp:**

-   Dev code xong, commit --> CI/CD triển khai tự động.
-   Hoặc cẩn bơm tay thêm một click là xong.

# CI/CD là gì?

CI/CD bao gồm 2 phần chính:

## 1. CI - Continuous Integration

-   Clone source code
-   Build project
-   Tích hợp các bước test:

    -   Test performance
    -   Test clean code (code style, format)
    -   Test bảo mật (security linter, scanner)

-   Nhằm đảm bảo chất lượng code trước khi deploy

## 2. CD - Continuous Deployment/Delivery

### Continuous Deployment

-   Triển khai hoàn toàn tự động mỗi commit.
-   Không cần thao tác con người.

### Continuous Delivery

-   Tự động build + test --> tạo bán sản phẩm sẵn sàng deploy.
-   Bước deploy là _manual_ (thủ công): cần con người confirm.

### Tại sao lại có 2 loại CD?

-   Mỗi loại đều có điểm mạnh/yếu:

    -   Deployment tự động: tối ưu hiệu suất.
    -   Delivery thủ công: kiểm soát tốt hơn.

-   Trong thực tế, doanh nghiệp thường tích hợp linh hoạt cả hai:

    -   Môi trường dev: auto deploy.
    -   Môi trường production: delivery hoặc semi-auto.

## Công cụ CI/CD phổ biến

Ta sẽ thực hành CI/CD với:

-   **GitLab CI/CD**
-   **Jenkins**

Cả hai là những công cụ CI/CD phổ biến nhất hiện nay.

Mục tiêu:

-   Biết cách dùng cả 2.
-   So sánh điểm mạnh/yếu.
-   Tự trả lời câu hỏi: **"Tại sao tôi dùng công cụ này?"**

## Lưu ý khi triển khai CI/CD trong thực tế

-   Mỗi doanh nghiệp không giống nhau:

    -   Doanh nghiệp lớn, nhỏ, trong nước, nước ngoài, outsource, product...

-   Công nghệ dự án, quy mô team, workflow team khác nhau.
-   Phải linh hoạt trong thiết kế quy trình CI/CD để tối ưu hệ thống.

> CI/CD không chỉ là một công nghệ mà là một tư duy: tự động hoá, kiểm soát chất lượng, giảm thiểu sai sót và tăng hiệu suất.
