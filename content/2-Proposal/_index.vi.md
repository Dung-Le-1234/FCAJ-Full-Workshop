---
title: "Bản đề xuất"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

Tại phần này, bạn cần tóm tắt các nội dung trong workshop mà bạn **dự tính** sẽ làm.

# Serverless Multiplayer Game Backend  
## Giải pháp AWS Serverless mở rộng cho game thời gian thực & xử lý Avatar AI  

### 1. Tóm tắt điều hành  
Dự án này nhằm xây dựng một hạ tầng backend serverless vững chắc cho game Unity multiplayer. Hệ thống phân tách nhiệm vụ giữa các nhóm DevOps, Frontend (Unity) và Backend.  

Các dịch vụ AWS được sử dụng:  
- **Xác thực người dùng** → Amazon Cognito  
- **Logic gameplay** → AWS Lambda  
- **Bảng xếp hạng thời gian thực** → API Gateway WebSocket + DynamoDB Streams  
- **Xử lý Avatar AI** → Lambda container (OpenCV/MediaPipe)  

Kiến trúc cung cấp độ sẵn sàng cao, tự động CI/CD và tích hợp liền mạch với WebGL, triển khai trên itch.io hoặc CloudFront.  

### 2. Tuyên bố vấn đề  
*Vấn đề hiện tại*  
Game multiplayer đòi hỏi các tính năng backend phức tạp như xác thực, giao tiếp thời gian thực và lưu trữ dữ liệu. Server truyền thống tốn chi phí, khó duy trì và không mở rộng hiệu quả khi lượng người chơi tăng đột biến. Ngoài ra, game cần **chuyển đổi avatar dựa trên AI**, tính toán nặng.  

*Giải pháp*  
Một kiến trúc AWS hoàn toàn serverless:  
- **Xác thực:** Amazon Cognito (User Pools + Hosted UI)  
- **Logic & tính toán:** Lambda (zip hoặc container image từ ECR)  
- **Tương tác thời gian thực:** API WebSocket + DynamoDB Streams  
- **Lưu trữ:** Amazon S3 cho avatar và asset  

*Lợi ích và hoàn vốn đầu tư (ROI)*  
Giải pháp tạo nền tảng cơ bản để các nhóm phát triển game có thể mở rộng backend, đồng thời cung cấp dữ liệu cho AI hoặc các phân tích khác. Backend serverless giảm chi phí vận hành, tự động mở rộng khi số người chơi tăng, và tiết kiệm thời gian quản lý so với server truyền thống. Chi phí ước tính < 5 USD/tháng trong giai đoạn phát triển.  

### 3. Kiến trúc giải pháp  
Hệ thống theo kiến trúc microservices event-driven. Unity giao tiếp với AWS qua REST (điểm số, cửa hàng) và WebSocket (bảng xếp hạng trực tiếp). Xử lý AI được thực hiện bằng Lambda container.  

![Serverless Game Architecture](/images/2-Proposal/game_architecture.jpeg)

*Dịch vụ AWS sử dụng*  
- *Amazon Cognito*: User Pools, Hosted UI  
- *API Gateway (REST + WebSocket)*  
- *AWS Lambda (Zip + Container Image)*  
- *Amazon DynamoDB + Streams*  
- *Amazon S3*  
- *Amazon ECR*  

*Thiết kế thành phần*  
- *Frontend*: Unity WebGL build, triển khai trên itch.io hoặc CloudFront  
- *Luồng dữ liệu*:  
  1. Người dùng đăng nhập → Cognito Token  
  2. Unity gọi REST API → Lambda → DynamoDB  
  3. Upload avatar → Presigned URL → S3 → Lambda (AI Container)  
  4. Cập nhật điểm → DynamoDB Stream → WebSocket broadcast  

### 4. Triển khai kỹ thuật  
*Các giai đoạn triển khai*  
1. *Thiết lập hạ tầng (DevOps)*: Cognito, DynamoDB, S3, API Gateway  
2. *Xây dựng backend skeleton (BE)*: API specs, Postman, code Lambda cơ bản  
3. *Tích hợp đăng nhập (FE)*: Unity AuthManager  
4. *Kết nối & Streams (DevOps)*: API → Lambda, bật Streams  
5. *Tích hợp gameplay (FE)*: DataManager → REST APIs  
6. *Kiểm thử end-to-end*: Login, Shop, Leaderboards, Avatar  
7. *Triển khai*: WebGL build + redirect URLs  

*Yêu cầu kỹ thuật*  
- *Frontend*: Unity (C#) – AwsConfig, AuthManager, DataManager, RealtimeManager  
- *Backend*: Node.js/Python cho Lambda, Docker cho AI container  
- *DevOps*: IAM roles, CloudFormation (tùy chọn), WAF (tùy chọn)  

### 5. Lộ trình & Mốc triển khai  
- *Phase 1: Foundation (Ngày 1–3)*: DevOps thiết lập Cognito, DynamoDB, S3, API Gateway  
- *Phase 2: Logic Development (Ngày 3–8)*: Backend xây Lambda + AI container, Frontend xây login  
- *Phase 3: Integration (Ngày 8–12)*: DevOps kết nối Streams, Frontend tích hợp API  
- *Phase 4: Testing & Launch (Ngày 13–15)*: Test bảng xếp hạng, pipeline avatar; triển khai WebGL  

### 6. Ước tính ngân sách  
(Theo AWS Pricing Calculator)  

*Chi phí hạ tầng*  
- AWS Lambda: chủ yếu Free Tier  
- DynamoDB: Free Tier (25GB)  
- S3: ~$0.023/GB  
- CloudWatch: ~$0.5–1/tháng  
- ECR: ~$0.10/GB  

*Tổng*: < 5 USD/tháng trong quá trình phát triển  

### 7. Đánh giá rủi ro  
*Ma trận rủi ro*  
- Mất mạng: Trung bình, xác suất trung bình  
- Hỏng cảm biến/AI container: Cao, xác suất thấp  
- Vượt ngân sách: Trung bình, xác suất thấp  

*Chiến lược giảm thiểu*  
- Mạng: lưu trữ cục bộ trên thiết bị phát triển nếu AWS gặp sự cố  
- Container AI: kiểm tra định kỳ, backup image  
- Chi phí: cảnh báo ngân sách AWS, tối ưu dịch vụ  

*Kế hoạch dự phòng*  
- Quay lại thủ công nếu AWS gặp sự cố  
- Sử dụng CloudFormation để rollback cấu hình liên quan chi phí  

### 8. Kết quả kỳ vọng  
*Cải tiến kỹ thuật*: Backend game serverless hoàn toàn, bảo mật dữ liệu người chơi, bảng xếp hạng thời gian thực, xử lý avatar AI tự động.  
*Giá trị dài hạn*: Kiến trúc tái sử dụng cho game tương lai, tự động mở rộng mà không cần server, chi phí vận hành thấp, nền tảng dữ liệu 1 năm cho nghiên cứu AI.  
