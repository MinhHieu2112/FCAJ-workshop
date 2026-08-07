---
title: "Event 2"
date: 2026-07-04
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Enterprise Cloud Architectures and Industry Application

### Mục Đích Sự Kiện

- Giúp sinh viên hiểu cách các doanh nghiệp thiết kế và vận hành hệ thống trên nền tảng AWS, đồng thời cập nhật yêu cầu tuyển dụng và định hướng nghề nghiệp trong lĩnh vực Cloud.
- Cung cấp góc nhìn toàn cảnh về nhu cầu tuyển dụng, tiêu chuẩn kỹ thuật thực tế đối với kỹ sư Cloud trẻ và các kênh tiếp cận cơ hội nghề nghiệp trong ngành.
- Tiếp nối tinh thần *"Pay it Forward"* của cộng đồng AWS First Cloud AI Journey – nơi các thế hệ đi trước quay lại chia sẻ, định hướng và hỗ trợ kỹ năng cho thế hệ tiếp nối.

### Danh Sách Diễn Giả

- **Mr.Nguyen Gia Hung** - Head of Solution Architect, AWS Việt Nam (Founder của AWS First Cloud AI Journey)
- **Mr.Khang Nguyen** - Solution Architect, Cloud Kinetics
- **Ms.Nhu Tran** - Account Manager, Amazon Web Services Việt Nam
- **Mr.Vinh Banh** - Senior Data Engineer, Renova Cloud

### Nội Dung Nổi Bật

#### Thực trạng và tiêu chuẩn tuyển dụng ngành Cloud

- **Tiêu chuẩn đầu vào gia tăng:** Yêu cầu dành cho các vị trí Thực tập sinh (Intern) hay Fresher ngày càng khắt khe. Ngay từ vị trí Intern, nhà tuyển dụng đã kỳ vọng ứng viên nắm rõ kiến thức về Containerization, Orchestration (như Kubernetes/K8s) và nền tảng Mạng (Networking).
- **Dịch chuyển hệ thống lõi lên Cloud:** Xu hướng đưa cả hệ thống Core Banking, hệ thống tài chính, bảo hiểm và thương mại điện tử lên Cloud đòi hỏi kỹ sư trẻ phải có tư duy vững chắc về bảo mật, tính sẵn sàng cao và khả năng mở rộng.

#### Giải mã thị trường tuyển dụng "Ẩn"

- **Thực tế kênh tuyển dụng:** Có đến **90% - 100%** nhu cầu tuyển dụng thực tế trong lĩnh vực Cloud chuyên sâu không xuất hiện trên các trang tin tuyển dụng đại trà.
- **Tuyển dụng qua mạng lưới nội bộ:** Phần lớn các cơ hội việc làm đến từ tuyển dụng nội bộ, chương trình tiến cử và mối quan hệ cá nhân.
- **Tăng cường sự hiện diện:** Việc chủ động tham gia và đóng góp cho các cộng đồng chuyên môn là chìa khóa để tiếp cận các công việc chất lượng mà không cần thông qua các trang đăng tin truyền thống.

#### Xu hướng kiến trúc đám mây doanh nghiệp

- Các doanh nghiệp lớn đang dịch chuyển mạnh mẽ từ việc ứng dụng Cloud ở quy mô nhỏ sang triển khai toàn bộ hạ tầng cốt lõi trên AWS.
- Việc thiết kế kiến trúc hệ thống bắt buộc phải tuân thủ nghiêm ngặt các tiêu chuẩn về an toàn dữ liệu, tối ưu chi phí vận hành và phân bổ tài tài nguyên linh hoạt.

### Những Gì Học Được

#### Tư duy thiết kế & năng lực kỹ thuật

- Qua phần chia sẻ của các diễn giả, em nhận thấy kiến thức nền tảng như Linux, Networking, Docker, Kubernetes và Infrastructure as Code vẫn là yêu cầu quan trọng đối với kỹ sư Cloud. Đây cũng là những kỹ năng cần được đầu tư trước khi tìm hiểu các dịch vụ AWS chuyên sâu.
- Khi đối mặt với bài toán kỹ thuật phức tạp, cần biết đưa ra các giả định, tinh chỉnh góc nhìn và thu hẹp phạm vi để người hỗ trợ dễ dàng tư vấn lời giải chính xác.

#### Định hướng phát triển sự nghiệp

- **Chủ động tạo dựng sự hiện diện:** Năng lực kỹ thuật cần đi đôi với sự chủ động kết nối. Xây dựng uy tín cá nhân trong cộng đồng giúp mở ra nhiều cơ hội nghề nghiệp dài hạn.
- **Sự kiên trì:** Thành công và sự may mắn trong sự nghiệp là kết quả của một quá trình học tập kiên trì, tích lũy liên tục chứ không tự nhiên xuất hiện.
- **Làm việc nhóm liên ngành:** Dự án thực tế đòi hỏi sự phối hợp giữa đội ngũ kỹ thuật và các bộ phận nghiệp vụ (Business, Marketing) để đảm bảo sản phẩm tạo ra đúng giá trị kinh doanh.

### Ứng Dụng Vào Project

- **Tối ưu quản lý và phân phối tệp phương tiện:** Sau buổi chia sẻ, em quyết định tách việc lưu trữ hình ảnh ra khỏi máy chủ ứng dụng và sử dụng Amazon S3. Điều này giúp Backend chỉ xử lý nghiệp vụ thay vì phải lưu trữ các tệp dung lượng lớn.
- **Tích hợp dịch vụ vị trí địa lý:** Đối với chức năng quản lý bất động sản, em lựa chọn Amazon Location Service để hỗ trợ tìm kiếm địa chỉ và hiển thị vị trí trên bản đồ thay vì sử dụng các dịch vụ bản đồ bên thứ ba.
- **Xác thực và phân quyền tập trung:** Em cũng triển khai **AWS Cognito User Pool** để quản lý định danh người dùng, cấp phát JWT Tokens và kết hợp với NestJS Guards để phân quyền dựa trên vai trò (RBAC) cho người dùng trên nền tảng Next.js.
- **Tư duy thiết kế hạ tầng mở rộng:** Định hướng đóng gói ứng dụng bằng Docker và chuẩn bị lộ trình quản lý container để đáp ứng khả năng tự động mở rộng khi lượng truy cập tăng cao.

### Trải Nghiệm Trong Event

Đây là lần đầu tiên em tham gia một sự kiện kỹ thuật được tổ chức trực tiếp tại văn phòng AWS Việt Nam. Không khí của buổi chia sẻ khá cởi mở, các diễn giả dành nhiều thời gian trao đổi với sinh viên về kinh nghiệm triển khai dự án cũng như định hướng nghề nghiệp.

#### Học hỏi từ các chuyên gia đầu ngành
- Được trực tiếp lắng nghe chia sẻ từ anh Nguyễn Gia Hưng cùng các chuyên gia giải pháp từ Cloud Kinetics và Renova Cloud về cách triển khai hạ tầng đám mây cho các hệ thống lớn.
- Tiếp thu cái nhìn thực tế về tiêu chuẩn năng lực của kỹ sư Cloud trong giai đoạn hiện tại.

#### Trao đổi và kết nối cộng đồng
- Bầu không khí cởi mở giúp sinh viên tự tin đặt câu hỏi, thảo luận về các khúc mắc kỹ thuật cũng như định hướng sự nghiệp.
- Điều khiến em ấn tượng là tinh thần Pay it Forward của cộng đồng AWS First Cloud AI Journey. Nhiều anh chị từng tham gia chương trình quay trở lại để chia sẻ kinh nghiệm và giải đáp câu hỏi cho các bạn sinh viên.

#### Bài học rút ra
- Cần có tầm nhìn sự nghiệp dài hạn, không dừng lại ở mức hoàn thành môn học mà phải liên tục cập nhật các công nghệ thực chiến như Kubernetes, CI/CD, Serverless.
- Sau buổi chia sẻ, em nhận ra rằng kiến thức kỹ thuật chỉ là một phần. Việc chủ động tham gia cộng đồng, xây dựng mối quan hệ và duy trì việc học liên tục cũng quan trọng không kém khi tìm kiếm cơ hội nghề nghiệp.

#### Hình ảnh tham gia sự kiện

![AWS Study Tour Event](https://img.youtube.com/vi/FKtMkUqyny4/maxresdefault.jpg)
***Hình 1: Toàn cảnh sự kiện Study Tour "AWS: Enterprise Cloud Architectures and Industry Application" tại văn phòng AWS Việt Nam.***

![Overview](/images/4-EventParticipated/event_2.jpeg)
***Hình 2: Hình ảnh tập thể chụp cùng các diễn giả sự kiện.***

> Tổng thể, sự kiện không chỉ củng cố kiến thức kỹ thuật về AWS mà còn giúp em định hình rõ ràng tư duy thiết kế hệ thống SaaS cho dự án thực tập, đồng thời thay đổi phương pháp tiếp cận cơ hội nghề nghiệp trong tương lai.