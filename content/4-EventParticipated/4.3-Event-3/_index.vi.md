---
title: "Event 3"
date: 2026-07-25
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

# FCAJ x Agentic AI Build Week: Show Up. Build. Pitch. WIN!

## Giới thiệu sự kiện

Chương trình chia sẻ về hành trình của các đội tham gia cuộc thi Hackathon do cộng đồng **AWS First Cloud AI Journey (FCAJ)** phối hợp cùng **JI Fund** tổ chức. Sự kiện quy tụ nhiều sinh viên, lập trình viên và các chuyên gia trong lĩnh vực AI và điện toán đám mây nhằm cùng học hỏi, xây dựng sản phẩm và chia sẻ kinh nghiệm thực tế.

Em tham gia sự kiện với mong muốn tìm hiểu cách các nhóm phát triển sản phẩm AI trong thời gian ngắn, đồng thời học hỏi tư duy thiết kế sản phẩm và những yêu cầu cần có khi đưa một ứng dụng AI vào môi trường thực tế.

## Mục tiêu của sự kiện

- Tìm hiểu xu hướng phát triển của Agentic AI và các ứng dụng thực tế.
- Tìm hiểu quy trình xây dựng một sản phẩm PoC có khả năng mở rộng thực tế trong thời gian ngắn.
- Học hỏi kinh nghiệm, lắng nghe chia sẻ từ các đội tham gia Hackathon.
- Hiểu thêm những yếu tố cần quan tâm khi đưa một sản phẩm AI vào môi trường thực tế.

## Diễn giả

- **Mr.Nguyen Gia Hung** - Head of Solution Architect, AWS Việt Nam (Founder của AWS First Cloud AI Journey)
- **Mr.Joseph Marazota** - Head of Technology, Amazon ASEAN.
- Các đội thi Hackathon.

## Nội dung chính

### Tổng quan về cuộc thi Hackathon

Điểm khác biệt của cuộc thi là khuyến khích phát triển các AI Agent có khả năng tự lập kế hoạch, sử dụng công cụ và thực thi quy trình phức tạp, thay vì chỉ dừng lại ở các công cụ Generative AI hỏi - đáp đơn thuần.

Cuộc thi không chỉ đánh giá ở mức sản phẩm chạy thử (POC) mà còn xoay quanh các bài toán thực chiến khi triển khai AI trong doanh nghiệp: xây dựng Guardrails, tích hợp phân quyền (RBAC), cơ chế giám sát con người (Human-in-the-loop) và tối ưu hóa chi phí gọi API.

Trong suốt thời gian diễn ra Hackathon, các đội phát triển ý tưởng thành một sản phẩm có thể giải quyết một bài toán thực tế trong khoảng 48 giờ. Mỗi nhóm chỉ có khoảng 5 phút để trình bày ý tưởng và sản phẩm của mình.

### Một số phần trình bày của các đội thi

Trong chương trình, các đội thi trình bày nhiều ý tưởng ứng dụng Agentic AI để giải quyết những bài toán thực tế. Mỗi nhóm đều xây dựng một sản phẩm mẫu (MVP/POC) và trình bày cách tiếp cận, kiến trúc cũng như khả năng mở rộng của giải pháp.

#### 1. Ứng dụng Agentic AI trong đặt hàng trực tuyến

Nhóm tập trung giải quyết những bất tiện trong quy trình đặt hàng trực tuyến. Theo nhóm, người dùng thường phải trải qua nhiều bước như đăng ký tài khoản, nhập thông tin thanh toán và tìm kiếm món ăn qua nhiều giao diện khác nhau, làm giảm trải nghiệm sử dụng.

![Overview](/images/4-EventParticipated/Event_3/oneTeam.png)
***Hình 1. Kiến trúc AWS mà đội thi xây dựng.***

Để giải quyết vấn đề này, nhóm xây dựng một AI Agent có khả năng hỗ trợ người dùng đặt hàng thông qua hội thoại tự nhiên. Một số điểm nổi bật của giải pháp gồm:

- Thu thập dữ liệu thực đơn từ website chính thức bằng công cụ web scraping và lưu trữ trên hạ tầng AWS.
- Xây dựng cơ chế lưu trữ lịch sử tương tác (Memory) để AI ghi nhớ sở thích và các đơn hàng trước đó của từng người dùng.
- Tự động tạo đơn hàng và thêm sản phẩm vào giỏ hàng ngay trong quá trình trò chuyện, giúp giảm số lượng thao tác thủ công.

Giải pháp cho thấy Agentic AI có thể đóng vai trò như một trợ lý hỗ trợ người dùng hoàn thành tác vụ thay vì chỉ trả lời câu hỏi như các chatbot truyền thống.

#### 2. Ứng dụng Agentic AI trong phân tích dữ liệu

Một nhóm khác lựa chọn bài toán hỗ trợ phân tích dữ liệu và lập báo cáo tự động cho Data Analyst. Mục tiêu của nhóm là giảm thời gian thực hiện các công việc lặp lại và hỗ trợ quá trình ra quyết định.

![Overview](/images/4-EventParticipated/Event_3/Data-analysis.png)
***Hình 2. Kiến trúc AWS mà đội thi xây dựng.***

Giải pháp được xây dựng theo mô hình Proof of Concept (POC), trong đó AI Agent có khả năng:

- Tiếp nhận yêu cầu phân tích dữ liệu và tạo báo cáo ban đầu.
- Xây dựng cơ chế Agent Loop để tiếp nhận phản hồi từ người phân tích dữ liệu, sau đó tiếp tục thu thập thông tin và cải thiện kết quả.
- Áp dụng Guardrails nhằm kiểm tra tính hợp lệ của dữ liệu đầu ra trước khi cung cấp cho người sử dụng.

Qua phần trình bày, nhóm cho thấy Agentic AI không chỉ hỗ trợ tự động hóa quy trình phân tích dữ liệu mà còn có thể phối hợp với con người để từng bước nâng cao chất lượng kết quả.

#### 3. Ứng dụng Agentic AI theo dõi lưu lượng khách hàng
Nhóm tập trung xây dựng giải pháp giải quyết vấn đề theo dõi lưu lượng hành khách ra vào ở các khu vực công ty, hãng sân bay.

![Overview](/images/4-EventParticipated/Event_3/Guest.png)
***Hình 3. Kiến trúc AWS mà đội thi xây dựng.***

- Kiến trúc sử dụng Amazon Kinesis Video Streams để tiếp nhận dữ liệu hình ảnh từ camera và đưa vào môi trường xử lý trên AWS. 
- Dữ liệu được xử lý thông qua ECS, Amazon ECR và SageMaker Endpoint nhằm phân tích hình ảnh và nhận diện thông tin liên quan đến lưu lượng khách hàng. 
- Các kết quả và dữ liệu sự kiện được lưu trữ bằng Amazon S3 và DynamoDB. 
CloudFront, API Gateway và AWS Lambda đảm nhiệm việc cung cấp và xử lý các yêu cầu từ phía người dùng 
- AgentCore Runtime kết hợp Amazon Bedrock hỗ trợ xây dựng Agent có khả năng phân tích và tương tác với dữ liệu. 
- Tích hợp Cognito, IAM, Secrets Manager, CloudTrail và CloudWatch để quản lý xác thực, phân quyền, bảo mật và giám sát hệ thống. 

Qua kiến trúc này, em có thể hình dung rõ hơn cách các dịch vụ AWS được kết hợp thành một hệ thống hoàn chỉnh thay vì sử dụng từng dịch vụ một cách độc lập.

## Những kiến thức tiếp thu

Sau sự kiện, em rút ra một số kiến thức quan trọng:

- Một AI Agent không chỉ thực hiện một câu lệnh duy nhất mà cần có quy trình lập kế hoạch, thực hiện và đánh giá kết quả.
- Khi phát triển ứng dụng AI cho doanh nghiệp, cần xây dựng các cơ chế kiểm soát (Guardrails) để giới hạn phạm vi hoạt động của AI.
- Một sản phẩm trình diễn (POC/MVP) và một sản phẩm triển khai thực tế khác nhau ở độ ổn định, khả năng mở rộng, bảo mật và chi phí vận hành.
- AI nên được sử dụng để giảm số lượng thao tác của người dùng thay vì chỉ bổ sung thêm nhiều tính năng.

## Ứng dụng vào dự án

Sau sự kiện, em có thêm một số định hướng để cải thiện dự án cá nhân:

- Tích hợp AI Agent hỗ trợ khách thuê báo cáo sự cố bằng ngôn ngữ tự nhiên.
- Hỗ trợ người quản lý tra cứu báo cáo doanh thu và tình trạng bất động sản bằng câu hỏi tự nhiên.
- Áp dụng Guardrails kết hợp với cơ chế phân quyền (RBAC) để giới hạn dữ liệu AI được phép truy cập.
- Lựa chọn mô hình AI phù hợp cho từng tác vụ nhằm cân bằng giữa hiệu quả và chi phí.

## Trải nghiệm của em

Qua phần trao đổi giữa các đội thi và các chuyên gia, em hiểu rõ hơn rằng để một hệ thống AI có thể sử dụng lâu dài thì ngoài việc hoạt động đúng còn cần đáp ứng các yêu cầu về bảo mật, khả năng mở rộng và chi phí vận hành.

Bên cạnh đó, em cũng có cơ hội gặp gỡ nhiều bạn sinh viên và kỹ sư đang quan tâm đến AI và AWS. Những cuộc trao đổi ngắn trong sự kiện giúp em có thêm nhiều góc nhìn về cách học tập cũng như định hướng phát triển trong thời gian tới.

## Bài học rút ra

Sau khi tham gia sự kiện, em rút ra một số bài học cho bản thân:

- Luôn bắt đầu từ nhu cầu thực tế của người dùng trước khi lựa chọn công nghệ.
- Khi thiết kế ứng dụng AI cần cân bằng giữa tính năng, độ chính xác và chi phí vận hành.
- Các cơ chế phân quyền và kiểm soát dữ liệu cần được thiết kế ngay từ đầu nếu AI được phép truy cập dữ liệu của hệ thống.
- Tham gia các sự kiện kỹ thuật và Hackathon là cơ hội tốt để học hỏi kinh nghiệm, mở rộng mối quan hệ và cập nhật những xu hướng công nghệ mới.

## Hình ảnh tham gia sự kiện

![FCAJ x Agentic AI Build Week Event](https://img.youtube.com/vi/hz32VBrvW7M/maxresdefault.jpg)

***Hình 4. Toàn cảnh sự kiện FCAJ x Agentic AI Build Week.***

![Overview](/images/4-EventParticipated/Event_3/Meeting.jpeg)
***Hình 5. Hình chụp tập thể cùng các đội thi Hackathon***

## Kết luận

Sự kiện **FCAJ x Agentic AI Build Week** đã mang đến cho em rất nhiều kiến thức và trải nghiệm quý báu. Em hiểu rõ hơn về cách xây dựng một ứng dụng AI từ ý tưởng đến sản phẩm có thể trình diễn, đồng thời nhận thức được những yêu cầu quan trọng khi triển khai vào môi trường thực tế. Những kiến thức và kinh nghiệm thu được từ chương trình sẽ là cơ sở để em tiếp tục hoàn thiện dự án cá nhân, đặc biệt ở các định hướng ứng dụng AI trong tương lai.