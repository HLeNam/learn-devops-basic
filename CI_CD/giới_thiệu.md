# Tại sao CI/CD lại quan trọng như vậy?

CI/CD là được xem là quy trình không thể thiếu nếu muốn phát triển doanh nghiệp.

Cũng như là mảng kiến thức giúp cho bất cứ ai trong nghành lập trình, sẽ đều nổi bật hơn người khác
kể cả ta không làm chuyên môn devops

Ví dụ: Ta làm dev hay system biết thêm kiến thức devops hay cụ thể hơn chỉ là CI/CD thôi
và áp dụng vào trong doanh nghiệp thì đã đóng góp được rất nhiều vào việc tối ưu trực tiếp
tài nguyên của công ty rồi.
Và chắc chắn khi ứng tuyển một người trong CV có ghi thêm dòng là "Đã từng triển khai CI/CD"
cho dự án thì thực sự sẽ nổi bật hơn rất nhiều so với ứng viên khác.
Cũng vì vậy sau muốn chuyển sang devops thì sẽ thuận lợi hơn rất là nhiều vì đã có kinh nghiệm
trước đó.

Trường hợp thực tế như sau:

Ở trong doanh nghiệp dev sẽ tiến hành code các dự án, rồi sau đó các bạn
ops hay system sẽ tiến hành lấy code và triển khai dự án.
Với 1, 2 source code thì có vẻ chưa có vấn đề
Nhưng trong doanh nghiệp mỗi dự án có thể có hàng chục source code hay
nói dễ hiểu hơn là 20 dự án trên git thôi thì vậy cần bao nhiêu system
mới đủ để triển khai kịp thời các dự án được.
Mà quá trình phát triển dự án lại có 3 môi trường chính là development dàng cho dev,
staging dành cho test và production dành cho end user. Vậy thì môi trường development
các bạn dev code hàng ngày, commit hàng giờ và hàng phút, vậy mà phải chờ system triển
khai xong mới có thể xem được code của mình chạy đúng chưa ở trên môi trường server để còn
đưa lên những môi trường quan trọng hơn thì điều đó thực sự rất tốn thời gian và ảnh hường
trực tiếp đến hiệu suất của dự án.
Thêm nữa nếu không có CI/CD thì các bạn system cũng sẽ khổ không kém, bạn sẽ phải triển khai
thủ công từng dự án một và công việc vô cùng tốn thời gian hơn nữa còn nguy hiểm là rất có
khả năng gặp sai lầm vì việc triển khai thủ công không thể đảm bảo gõ được chính xác hoàn
toàn các câu lệnh.

Vậy bây giờ chính xác là các bạn dev khi code xong chỉ cần một dòng commit là code mới sẽ
hoàn toàn tự động được triển khai lên trên server mà chẳng cần sự can thiệp của con người
hay cẩn thận hơn cũng chỉ cần một và click là chức năng mới cũng hoàn toàn được triển khai tự động
chứ không cần làm thủ công như trước

# CI/CD là gì?

CI/CD được chia thành 2 bước hay chính xác là 2 phần:

-   CI (Continuous Integration):
    -   Chính là bước clone code,
    -   build dự án,
    -   tích hợp công cụ test dự án như: test performant, test clean code, test bảo mật, ...
    -   để đảm bảo chất lượng của source code
-   CD: 2 loại chiến lược triển khai dự án

    -   Continuous Deployment:
        -   Triển khai hoàn toàn tự động hay chính xác là bạn dev chỉ cần một dong commit code là chức năng
            mới sẽ được triển khai mà không cần thêm bước nào cả.
    -   Continuous Delivery:
        -   Bước triển khai sẽ là thủ công và chính xác ta sẽ cần một bước xác nhận để triển khai dự án,
        -   Hoặc thủ công hơn nữa là sử dụng các chức năng,..
    -   Tại sao lại có 2 cách triển khai?:
        -   Thì cả 2 chiến lược đều có những điểm yếu và điểm mạnh riêng ví dụ:
            -   Tự động hoàn toàn sẽ tối ưu về mặt hiệu suất
            -   Thủ công một chút sẽ tăng khả năng kiểm soát hơn
        -   Tuy nhiên ta hoàn toàn có thể tận dụng điểm mạnh của tất cả các chiến lược rồi tích hợp ra một quy
            trình CI/CD tốt nhất cho doanh nghiệp.

Ta sẽ thực hành sử dụng 2 công cụ để triển khai CI/CD:

-   Gitlab CI/CD
-   Jenkins
-   Đây là 2 công cụ có thể nói là được ưa dùng nhất hiện tại

Dùng 2 công cụ để biết được điểm mạnh điểm yếu của chúng và quan trọng là trả lới được câu hỏi:
"Tại sao tôi dùng?"

Tuy nhiên để có được một quy trình CI/CD phù hợp thực sự không dễ đàng bởi vì doanh nghiệp có doanh nghiệp
to, doanh nghiệp nhỏ, có doanh nghiệp trong nước, doanh nghiệp ngoài nước, có out source và product,
rồi rất nhiều cách phân loại khác. Đó là chưa kể cụ thể trong dự án sử dụng công nghệ gì và làm sao để tối
ưu được nó.
