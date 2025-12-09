---
title: "Blog 2"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---



# Cách Smartsheet nâng cao các đề xuất bằng việc sử dụng Amazon Neptune và Knowledge Graphs

Đây là bài viết của khách mời được đồng tác giả bởi Divya Moorjaney, Principal Machine Learning tại Smartsheet.

Smartsheet là một nền tảng quản lý công việc cộng tác dựa trên SaaS hàng đầu được các doanh nghiệp trên toàn thế giới tin tưởng để quản lý dự án, tự động hóa quy trình làm việc và thúc đẩy sự cộng tác trên quy mô lớn. Smartsheet, một khách hàng lâu năm của AWS, đã liên tục phát triển để đáp ứng nhu cầu ngày càng tăng của cơ sở người dùng toàn cầu. Ngày nay, Smartsheet đang đưa nền tảng của mình lên một tầm cao mới bằng cách triển khai Knowledge Graphs được xây dựng trên Amazon Neptune với mục tiêu cung cấp thông minh, chủ động và theo ngữ cảnh từ dữ liệu cộng tác trong Smartsheet. Sự đổi mới này nhằm nâng cao trải nghiệm khách hàng, tăng năng suất và thúc đẩy duy trì lâu dài cũng như tăng trưởng dài hạn.

Trong bài viết này, chúng tôi mô tả Smartsheet Knowledge Graph, được xây dựng trong sự hợp tác giữa Smartsheet và AWS. Smartsheet Knowledge Graph là một mô hình dữ liệu thống nhất kết nối con người, nội dung và công việc trong Smartsheet, thể hiện cách người dùng tương tác với tài sản, nội dung và những người cộng tác viên của họ. Nói một cách đơn giản, nó là một mạng lưới chuyên nghiệp được thiết kế riêng cho mỗi người dùng trong Smartsheet. Việc nắm bắt sự cộng tác này giữa người dùng và công việc của họ giúp mở khóa khả năng cá nhân hóa, những hiểu biết và đề xuất cho từng người dùng.

## Giải pháp hiện có

Là một nền tảng quản lý công việc cộng tác hàng đầu, Smartsheet mở rộng quy mô để hỗ trợ một số doanh nghiệp lớn nhất thế giới. Nền tảng này chứa một lượng lớn dữ liệu cộng tác được tạo bởi người dùng khi họ lập kế hoạch, theo dõi và thực hiện công việc. Thách thức mà đội ngũ kỹ sư Smartsheet phải đối mặt là mô hình hóa dữ liệu này theo cách thể hiện sự cộng tác giữa người dùng và công việc của họ một cách thông minh, theo ngữ cảnh và đưa chúng vào trải nghiệm sản phẩm, đồng thời cũng đảm bảo khả năng mở rộng, bảo mật và hiệu suất trên toàn bộ cơ sở người dùng.

Smartsheet sử dụng các phương pháp kỹ thuật tốt nhất để triển khai các tính năng như micro-services. Do đó, mỗi micro-service sử dụng kho dữ liệu riêng của nó với công nghệ phù hợp với kiến trúc dịch vụ. Việc dữ liệu được chia nhỏ trên nhiều kho dữ liệu và công nghệ khác nhau khiến việc phân tích và ánh xạ các mối quan hệ giữa các đối tượng ở quy mô lớn trong thời gian thực trở nên khó khăn. Một mô hình dữ liệu graph thống nhất giải quyết thách thức này bằng cách tạo các node đối tượng và các edge xác định mối quan hệ giữa các đối tượng này. Làm như vậy cho phép Smartsheet duyệt qua mô hình dữ liệu graph này để hiểu ai là những người cộng tác thân thiết của người dùng và những tài sản nào liên quan đến người dùng, cho phép Smartsheet cung cấp hướng dẫn và giải pháp được cá nhân hóa và theo ngữ cảnh hơn cho người dùng. Giải quyết thách thức về dữ liệu này là một bước quan trọng trong việc cải thiện khả năng định hướng và cá nhân hóa trên toàn bộ nền tảng Smartsheet.

Trước khi phát triển mô hình dữ liệu graph, việc khám phá mối quan hệ giữa người dùng và công việc của họ trong Smartsheet có nghĩa là thực hiện các phép join trên các bảng trong Snowflake, di chuyển dữ liệu sang Amazon DynamoDB thông qua các hàm AWS Lambda, và suy ra insights bằng các mô hình ML cần được đào tạo lại liên tục khi dữ liệu và mối quan hệ thay đổi.

Tính linh hoạt của mô hình dữ liệu graph cho phép Smartsheet mô hình hóa các mối quan hệ và giữ chúng được cập nhật khi người dùng cộng tác trên nền tảng, ánh xạ các mối quan hệ lên đến 4 bậc từ người dùng chỉ với một openCypher query trên Neptune. Ví dụ, Smartsheet giờ đây có thể cung cấp các liên hệ được đề xuất phù hợp với mỗi người dùng để chia sẻ sheets, dashboards và workspaces, hoặc đề xuất nội dung liên quan cho người dùng dựa trên ngữ cảnh hoặc mức độ ưu tiên.


## Giải pháp được xây dựng với AWS

Smartsheet hợp tác với AWS để phát triển một nền tảng knowledge graph toàn diện kết hợp dữ liệu sử dụng của khách hàng trong Snowflake và các công nghệ graph. Trung tâm của giải pháp là một knowledge graph được cung cấp bởi Neptune, được thiết kế để mô hình hóa các mối quan hệ phức tạp trên nền tảng quản lý công việc cộng tác của Smartsheet. Kiến trúc này kết hợp một số dịch vụ AWS:

Amazon Neptune cung cấp nền tảng cơ sở dữ liệu graph, cho phép các truy vấn dựa trên mối quan hệ trên dữ liệu được kết nối với nhau.

Snowflake lưu trữ dữ liệu sử dụng của khách hàng về Smartsheet Plan của họ

Amazon Elastic Container Service (Amazon ECS)
  
- Các Task chuyển đổi và chuẩn bị dữ liệu cho việc ingest graph và tạo điều kiện cho    web service để đưa ra graph insights và đề xuất bằng cách sử dụng các truy vấn Cypher thông qua các API nội bộ cho các dịch vụ Smartsheet

- Web service đưa ra graph insights và đề xuất bằng cách sử dụng các truy vấn Cypher thông qua các API nội bộ cho các dịch vụ Smartsheet

Amazon Simple Storage Service (Amazon S3)  lưu trữ các file CSV dữ liệu graph để bulk loading vào Neptune

AWS Step Functions điều phối các ECS task để chuyển đổi và tải dữ liệu vào Neptune

Với kiến trúc này, Smartsheet cung cấp các insights theo ngữ cảnh và đề xuất được cá nhân hóa, giúp người dùng khám phá công việc và các kết nối của họ nhanh hơn.



Đối với Smartsheet, việc thiết kế một mô hình dữ liệu graph đảm bảo sự tách biệt logic của dữ liệu giữa các khách hàng của nó là vô cùng quan trọng – chúng tôi đã xem xét hướng dẫn "Multi-tenancy guidance for ISVs running Amazon Neptune databases" và chọn một mô hình pool để gán nhãn cho các đồ thị thuộc tính, đó là dữ liệu của mỗi người thuê (khách hàng Smartsheet) được lưu trữ trong một đồ thị duy nhất nhưng được tách biệt logic để đạt được phân vùng dữ liệu.

Smartsheet hợp tác với AWS để tối ưu hóa quá trình thu thập dữ liệu từ Snowflake vào Neptune bằng cách sử dụng các bulk loader operations để tạo và cập nhật các node và edge trong graph. Các thao tác xóa từ đồ thị được tối ưu hóa bằng cách sử dụng các Gremlin queries với timeout cho mỗi truy vấn và xử lý hàng loạt được tối ưu hóa. Khả năng chạy cả Gremlin và openCypher trên cùng một graph cho phép chúng tôi sử dụng cách tiếp cận tốt nhất cho từng công việc. Trong tương lai, các kỹ sư của chúng tôi sẽ cải thiện điều này hơn nữa bằng cách chuyển từ bulk loader sang quy trình luồng giao dịch. Để tránh việc đồ thị trở nên quá phức tạp và lưu giữ các mối quan hệ không liên quan, nhóm thường xuyên loại bỏ các node và edge của graph không hoạt động trong thời gian dài, chỉ giữ lại hoạt động gần đây và liên quan nhất.

Ở quy mô doanh nghiệp của nền tảng Smartsheet, mô hình dữ liệu graph phát triển nhanh chóng, đạt đến quy mô hàng triệu — hơn 70 triệu node và 150 triệu edge. Ở quy mô này, việc tối ưu hóa các truy vấn graph trở nên quan trọng để Smartsheet có thể cung cấp thông tin chi tiết trong SLA. Các kiến trúc sư giải pháp của AWS hợp tác với nhóm Smartsheet để tối ưu hóa các truy vấn openCypher, sử dụng các best practices như sử dụng câu lệnh WITH, chia nhỏ các truy vấn lớn hơn thành các truy vấn con và tối ưu hóa tính toán ngày tháng trên các trường ngày tháng trước đây được lưu trữ dưới dạng chuỗi.


## Tác động của sự hợp tác

Sự hợp tác giữa Smartsheet và AWS đã mang lại giá trị đáng kể cho cả hai công ty. Thông qua workshop Experience Based Acceleration (EBA) kéo dài 3 ngày của AWS, những gì bắt đầu như một proof-of-concept, đã chuyển thành một sáng kiến được đẩy nhanh sau khi ban lãnh đạo chứng kiến tiềm năng chuyển đổi tiềm năng của sự thấu hiểu về graph-powered trong suốt hoạt động cộng tác chuyên sâu.

"Sự hợp tác của chúng tôi với AWS và Amazon Neptune đã mang đến cho chúng tôi một nền tảng mạnh mẽ để đổi mới trong khi vẫn cung cấp quy mô, bảo mật và độ tin cậy mà khách hàng của chúng tôi mong đợi. Knowledge Graph mà chúng tôi đã xây dựng cùng nhau không chỉ là một mô hình dữ liệu — đó là cách chúng tôi có thể cung cấp những insights thông minh hơn, được cá nhân hóa hơn giúp các nhóm chuyển từ việc chỉ quản lý công việc sang giải quyết thách thức và thực thi chiến lược với tốc độ và độ chính xác cao."

– JB Brown, Phó Chủ tịch Kỹ thuật tại Smartsheet

 "Bằng cách xây dựng Knowledge Graph của mình trên Amazon Neptune, Smartsheet đang khai thác sức mạnh của dữ liệu được kết nối để cung cấp trải nghiệm theo ngữ cảnh và được cá nhân hóa hơn cho khách hàng. Cùng nhau, chúng tôi đang kết hợp một mô hình dữ liệu đổi mới, linh hoạt với quy mô và bảo mật cấp doanh nghiệp của AWS, cho phép các tổ chức làm việc thông minh hơn và nhanh hơn."

 – Brad Bebee, Giám đốc tại AWS

Smartsheet gần đây đã phát hành tính năng đầu tiên được hỗ trợ bởi Knowledge Graph (hình ảnh sau) cho các khách hàng Doanh nghiệp của mình. Mặc dù các kết quả sau đây vẫn còn sớm, chỉ trong vài tuần:

1 trên 7 khách hàng Doanh nghiệp đang sử dụng các đề xuất được hỗ trợ bởi Knowledge Graph để chia sẻ nội dung

99.9% người dùng chia sẻ từ 3 đề xuất đầu tiên

Những đề xuất này giúp khách hàng và người dùng Smartsheet chia sẻ nhanh hơn và dễ dàng hơn với những người mà họ thường xuyên cộng tác. Chỉ với vài cú nhấp chuột, tính năng mới nhưng đơn giản này giúp củng cố và nhắc nhở những người xây dựng giải pháp và quản trị viên Smartsheet nên chia sẻ với ai.
