# Container để làm gì? Tại sao sử dụng Docker?

Ở phần trước ta đã học được cách triển khai các dự án trực tiếp trên linux hay còn gọi là cách triển khai daemon.

Tiếp theo ta sẽ đến mảng kiến thức Docker hay còn gọi là cách triển khai Container.

## Tại sao cần sử dụng Docker?

Tùy doanh nghiệp và dự án sẽ có cách triển khai phù hợp. Triển khai Container có những khác biệt so với các triển khai daemon mà ta đã học trước đó.

### Hạn chế của triển khai daemon trên Linux

Ở các triển khai trực tiếp trên linux bạn đã biết là ta sẽ phải cần cài đặt các công cụ như: maven, java, node, npm, ... trực tiếp trên chính chiếc server đó.

Điều này dẫn đến một số vấn đề:

1. **Xung đột phiên bản công cụ**: Các dự án có thể yêu cầu version khác nhau của cùng một công cụ. Ví dụ như dự án A yêu cầu chính xác node 11 nhưng dự án B yêu cầu node 18. Việc cài đặt nhiều phiên bản của cùng một công cụ trên một server và quản lý chúng sẽ gặp khó khăn, đặc biệt với những người chưa rành về Linux.

2. **Ảnh hưởng qua lại giữa các dự án**: Các dự án có nguy cơ ảnh hưởng đến nhau vì đang dùng chung một hạ tầng.

3. **Khó khăn khi triển khai trên nhiều server**: Trên thực tế, các doanh nghiệp thường triển khai dự án trên nhiều server và sử dụng Load Balancing. Có thể hiểu đơn giản giống như một trường học có một cổng vào nhưng có nhiều lớp học khác nhau. Người dùng truy cập vào cùng một địa chỉ (ví dụ: devopsedu.vn) nhưng có thể được chuyển đến server 1 hoặc server 2, điều này giúp đảm bảo không có server nào bị quá tải.

    Tuy nhiên, việc triển khai daemon trên nhiều server sẽ gặp khó khăn vì phải cài đặt các công cụ cần thiết và thiết lập môi trường giống hệt nhau trên tất cả các server.

## Lợi ích của Container và Docker

Bạn có thể tưởng tượng như thế này: Bạn ra quán net chơi một game offline nào đó và đang chơi rất say mê, nhưng bỗng nhiên vì lý do nào đó bạn phải về gấp. Bạn cảm thấy tiếc những gì mình đang trải nghiệm và quyết định đóng gói tất cả nội dung của game đó (bao gồm cả file cấu hình, tiến trình...) vào một "cục" và đẩy lên Google Drive. Khi về nhà, bạn tải "cục" đó về máy tính của mình và tiếp tục chơi game như tại thời điểm ở quán net.

Dự án phần mềm cũng tương tự như vậy. Bạn có một dự án trên máy tính cá nhân và muốn chạy nó trên các thiết bị khác nhau hoặc trên server. Bạn cần một công cụ có thể:

-   Chạy được trên các nền tảng khác nhau (Windows, Linux, macOS)
-   Linh hoạt trong việc triển khai các dự án

Đây chính là lúc Docker phát huy tác dụng:

1. **Đóng gói thành Docker Image**: Docker giúp đóng gói dự án thành một "cục" gọi là Docker Image, bao gồm tất cả mã nguồn, thư viện và phụ thuộc.
2. **Lưu trữ trên Registry**: Docker Image có thể được đưa lên Registry (tương tự như việc bạn đưa "cục" game lên Google Drive).
3. **Triển khai dễ dàng**: Các thiết bị khác hoặc server chỉ cần kéo Docker Image về và chạy thành Container.
4. **Độc lập với nền tảng**: Miễn là có cài đặt Docker, bạn có thể chạy Container trên bất kỳ hệ điều hành nào mà không phụ thuộc vào nền tảng.

## Docker là gì?

Docker là nền tảng ảo hóa cấp Container. Nó tạo ra một môi trường độc lập cho phép người phát triển đóng gói ứng dụng và tất cả các phụ thuộc của nó vào một Container duy nhất.

Bạn có thể hình dung như sau: Nếu cài đặt Ubuntu trên Windows của bạn và triển khai các dự án lên đó, thì tương tự bạn cài Docker trên Ubuntu và triển khai dự án trên Docker. Điểm khác biệt là Docker sẽ gọn nhẹ hơn, không cồng kềnh như một server hoàn chỉnh, nó chỉ chứa những thứ cần thiết nhất để có thể chạy được dự án.

## Kiến trúc Docker (Docker Architecture)

Quy trình làm việc với Docker bao gồm các bước sau:

1. **Source code dự án**: Bạn bắt đầu với mã nguồn của dự án.
2. **Viết Dockerfile**: Tạo file hướng dẫn Docker cách đóng gói ứng dụng.
3. **Build Docker Image**: Sử dụng lệnh `docker build` để xây dựng Dockerfile và đóng gói dự án thành Docker Image.
4. **Push lên Registry**: Đưa Docker Image lên kho lưu trữ.
    - Có thể sử dụng Docker Hub (công khai và miễn phí, nhưng có giới hạn về lưu trữ riêng tư)
    - Hoặc tạo Registry riêng (các doanh nghiệp thường làm điều này để bảo mật).
5. **Pull và Run Container**: Trên các server, kéo Docker Image về và khởi chạy thành Container.

## Cách Docker hoạt động

Docker sử dụng kiến trúc client-server. Docker client (giao diện dòng lệnh hoặc desktop app) giao tiếp với Docker daemon (chạy nền trên hệ thống) thông qua REST API. Docker daemon xử lý các tác vụ như xây dựng, chạy và phân phối các container.

Docker dựa trên các tính năng của Linux kernel như namespaces và cgroups để cung cấp sự cô lập giữa các container. Mỗi container có không gian riêng biệt với hệ thống file, mạng, và các process riêng, tuy nhiên tất cả đều chia sẻ cùng một kernel hệ điều hành.

## Các thành phần chính

### 1. Docker Engine

Docker Engine là thành phần cốt lõi của Docker, bao gồm:

-   Docker daemon: Quản lý các container và xử lý các lệnh từ client.
-   REST API: Giao tiếp giữa client và daemon.
-   CLI (Command Line Interface): Giao diện dòng lệnh để tương tác với Docker.

### 2. Docker Images

Docker Image là một template chỉ đọc, chứa tất cả những gì cần thiết để chạy ứng dụng. Image được xây dựng từ các lớp (layers) và mỗi lớp tương ứng với một câu lệnh trong Dockerfile.

### 3. Docker Container

Container là một instance của Image đang chạy. Mỗi container có thể cô lập hoàn toàn với các container khác và hệ thống host, với tập định danh và không gian tài nguyên riêng.

### 4. Docker Registry

Registry là nơi lưu trữ và phân phối Docker Image. Docker Hub là Registry công khai chính thức của Docker, chứa hàng ngàn image mà bạn có thể sử dụng. Các doanh nghiệp thường tạo Registry riêng để lưu trữ và quản lý các image nội bộ.

## Lợi ích của Docker trong thực tế

1. **Nhất quán trong môi trường phát triển**: "Works on my machine" không còn là vấn đề khi tất cả các developer đều sử dụng cùng một container.
2. **Triển khai nhanh chóng**: Không cần cài đặt và cấu hình nhiều, chỉ cần pull image và run.
3. **Hiệu quả về tài nguyên**: Container nhẹ hơn máy ảo vì chúng chia sẻ kernel của hệ điều hành.
4. **Cô lập ứng dụng**: Mỗi ứng dụng chạy trong container riêng, giảm xung đột và tăng bảo mật.
5. **Khả năng mở rộng**: Dễ dàng nhân rộng ứng dụng bằng cách tạo nhiều container từ cùng một image.
6. **Quản lý phiên bản**: Dễ dàng quản lý các phiên bản khác nhau của ứng dụng bằng các tag image.

Tóm lại, Docker giúp giải quyết các vấn đề phổ biến trong việc phát triển và triển khai ứng dụng, đặc biệt là trong môi trường phát triển và vận hành hiện đại (DevOps).
