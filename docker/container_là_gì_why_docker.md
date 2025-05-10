# Container để làm gì ? Tại sao sử dụng Docker ?

Ở phần trước ta đã học được cách triển khai các dự án trực tiếp trên linux hay còn gọi là cách triển khai
daemon

Tiếp theo ta sẽ đến mảng kiến thức Docker hay còn gọi là cách triển khai Container

Vậy tại sao cần sử dụng Docker?

Tùy doanh nghiệp và dự án sẽ có cách triển khai phù hợp,
Triển khai Container có khác biệt gì so với các triển khai daemon mà ta đã học trước đó

Ở các triển khai trực tiếp trên linux bạn đã biết là ta sẽ phải cần cài đặt các công cụ như:
maven, java, node, npm, ..., trên chính chiếc server đó
--> Vậy lúc này xảy ra một vấn đề là các dự án có thể version của công cụ sẽ khác nhau ví dụ như:
dự án A yêu cầu chính xác node 11 nhưng dự án B yêu cầu node 18 chẳng hạn thì lúc đó việc cài cắm
nhiều version của công cụ trên một server và việc quản lý sử dụng chúng sẽ gặp chút khó khăn.
Đối với một số bạn chưa rành linux có thể sẽ tốn thêm thời gian để nghiên cứu.
Nếu không cẩn thận những dự án cũng sẽ có nguy cơ ảnh hưởng đến nhau vì đang dùng chung một hạ tầng
Rồi thì việc triển khai các dự án trên các server khác nhau:
Trên thực tế, họ có thể không chỉ triển khai dự án trên một server mà họ có thể triển khai trên nhiều server
và sư dụng Load Balancing. Ở đây có thể hiểu đơn giản giống như việc ngôi trường có một cổng vào nhưng có rất
là nhiều lớp học vậy tuy rằng họ chỉ truy cập vào đúng địa chỉ ví dụ như là devopsedu.vn chẳng hạn nhưng người
thì được truy cập vào server 1, người thì được truy cập vào server 2. Vậy thì sẽ đảm bảo là 1 server sẽ không bị
quá tải khi có nhiều người truy cập.
Vì thế mà việc triển khai daemon sẽ gặp chút khó khăn hơn vì phải cài cắm các công cụ cần thiết và triển khai
giống hệt nhau trên các server

Vậy thì nếu triển khai Container thì có những lợi ích gì ?
Bạn có thể tưởng tượng như thế này. Bạn ra quán net chơi một game offline nào đấy và đang chơi rất say mê nhưng bổng
nhiên vì lý do nào đó bạn phải về gấp thì lúc này bạn thấy tiết những gì mình đang trải nghiệm và bạn quyết định
ném tất cả nội dung của game đó từ file cấu hình vân vân vào một cục rồi bạn đẩy lên trên google driver chẳng hạn
và khi về nhà bạn tiến hành tải cục đó vào máy tính của bạn chạy game lên chơi bình thưởng như tại thời điểm ở quán
net. Thì chính xác lúc này dự án cũng tương tự như vậy
Bạn có một dự án trên máy tính cá nhân vậy làm sao để chạy nó lên ở các thiết bị khác nhau hoặc trên server.
Thì bạn sẽ đảm bảo việc có một công cụ nào đó có thể chạy được trên các nền tảng khác nhau như window, linux,
masOS và phải linh hoạt triển khai các dự án.
Thì lúc này chúng ta sẽ sử dụng docker để đóng gói dự án đó thành 1 cục và cục đó được gọi là Docker Image
rồi đưa lên trên Registry hay chính là việc bạn đóng gói game thành một cục và đưa lên trên google driver
Thì Registry ở đây tương tự là một nơi chuyên biệt để chức các Docker Image sau khi các thiết bị khác hay các server
kéo cục Docker Image đó về thì cục Docker Image đó chỉ cần chạy lên Container tuy nhiên thì khác biệt ở đây là chỉ
cần bạn có cài đặt Docker là hoàn toàn có thể chạy được tất cả các Container mà không bị phụ thuộc vào nền tảng.

Vậy thì Docker là gì ?
Docker là nền tảng ảo hóa cấp Container. Nó tạo ra một môi trường độc lập cho phép người phát triển đóng gói ứng
dụng và tất cả các phụ thuộc của nó vào một Container duy nhất.
Hay bạn có thể hình dung dễ hơn như này:
Bạn cài đặt ubuntu trên Window của bạn và triển khai các dự án lên đó thì tương tự bạn cài Docker trên ubuntu
và triển triển khai dự án trên Docker, khác là Docker sẽ gọn nhẹ hơn không cồng kềnh nhiều thứ như là server,
nó chỉ chứa những thứ cần thiết nhất để có thể chạy được dự án thôi.

Docker Architecture:
Thì như bạn thấy theo đúng follow chúng ta có

--> Source code dự án
--> Tiến hành viết Dockerfile
--> Sử dụng lệnh docker build để build Dockerfile đó và đóng gói dự án thành một cục được gọi là Docker Image
--> Và từ cục Docker Image đó chúng ta sẽ push lên trên Registry ở đây có thể là một Docker Hub hoặc là một
nơi lưu trữ tự tạo riêng biệt vì Docker Hub miễn phí sẽ giới hạn việc lưu trữ bí mật những Docker Image của bạn
và chắc chắn rồi Docker Image được đóng gói từ dự án của bạn vậy nên không thể công khai nó được.
Cũng chính vì vậy các doanh nghiệp sẽ có một Registry riêng và các bài học sau ta sẽ dựng lên một Registry giống
như các doanh nghiệp để có được kiến thức thực thế
--> Xong tiếp theo trên các Server mà bạn muốn chạy dự án thì bây giờ đơn giản chỉ cần là kéo Docker Image đó
từ Registry về và tiến hành khởi chạy thành Container

Ở trên là các kiến thức nền tảng nhất về Docker
