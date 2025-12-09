---
title: "AWS Security Blog"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Bảo mật Generative AI: Giới thiệu về Ma trận Phạm vi Bảo mật Generative AI (Generative AI Security Scoping Matrix)

Trí tuệ nhân tạo sinh (generative AI) đang thu hút sự chú ý của các tổ chức và thay đổi trải nghiệm khách hàng trên mọi quy mô. Sự phát triển mạnh mẽ này, được thúc đẩy bởi các mô hình ngôn ngữ lớn (LLM) với hàng tỷ tham số và mạng nơ-ron Transformer, mở ra các khả năng sáng tạo và cải thiện năng suất vượt bậc.

Khi tổ chức đánh giá và áp dụng generative AI, các nhóm an ninh mạng phải nhanh chóng xác định rủi ro, cơ chế quản trị và các biện pháp kiểm soát. Là những chuyên gia bảo mật làm việc với khách hàng lớn và phức tạp tại Amazon Web Services (AWS), chúng tôi thường xuyên được tham vấn về xu hướng, thực tiễn tốt nhất và các tác động bảo mật–quyền riêng tư của generative AI. Bài viết này chia sẻ các chiến lược giúp bạn tăng tốc hành trình bảo mật Generative AI.

Đây là bài đầu tiên trong loạt bài “Securing Generative AI”, trình bày mô hình tư duy để phân tích rủi ro và tác động bảo mật theo từng dạng workload generative AI. Các bài tiếp theo sẽ đi sâu vào thiết kế giải pháp, mô hình hóa mối đe dọa, đánh giá tuân thủ–quyền riêng tư, và cách tận dụng generative AI để nâng cao vận hành an ninh mạng.

---

## Bắt đầu từ đâu

Như mọi công nghệ mới, việc nắm vững nền tảng là yếu tố bắt buộc để hiểu phạm vi, rủi ro và yêu cầu tuân thủ. Nếu đang khám phá generative AI, hãy đọc về khái niệm, thuật ngữ và xem ví dụ ứng dụng thực tế.

Tin tốt: generative AI vẫn là một workload dựa trên dữ liệu, kế thừa nhiều phương pháp bảo mật truyền thống. Nếu tổ chức đã xây dựng thực tiễn cloud security tốt (như theo *Security Pillar*, *Well-Architected ML Lens*…), bạn đã đi được nửa chặng đường.

Các lĩnh vực bảo mật cốt lõi như IAM, bảo vệ dữ liệu, bảo mật ứng dụng, mô hình hóa mối đe dọa… vẫn giữ vai trò trọng yếu như mọi workload khác. Tuy nhiên, cần bổ sung nhận thức về rủi ro và tính chất đặc thù của generative AI.

---

## Xác định phạm vi

Khi tổ chức quyết định áp dụng generative AI, bước đầu tiên là xác định phạm vi bảo mật. Tùy use case, bạn có thể chọn sử dụng dịch vụ AI được quản lý hoàn toàn hoặc tự xây dựng mô hình.

AWS cung cấp nhiều lựa chọn:  
- **Amazon Bedrock**: serverless, API-driven với các mô hình nền (FMs) như AI21 Labs, Anthropic, Cohere, Meta, Stability.ai, Amazon Titan  
- **SageMaker JumpStart**: linh hoạt hơn nhưng vẫn dùng mô hình pretrained  
- **Tự xây dựng và huấn luyện mô hình trên Amazon SageMaker**  
- **Ứng dụng AI tiêu dùng qua web hoặc API**  

Mỗi loại dịch vụ mang theo mô hình dữ liệu, hạ tầng và mô hình bảo mật khác nhau. Để đồng nhất, AWS phân nhóm các dịch vụ theo phạm vi bảo mật – gọi là *Security Scopes*, đánh số từ 1–5 (ít quyền sở hữu nhất → nhiều quyền sở hữu nhất).

---

## Các phạm vi generative AI

### **Mua generative AI**

**Scope 1 – Ứng dụng tiêu dùng:**  
Dùng ứng dụng / API AI công khai. Không thấy dữ liệu huấn luyện, không thể tùy biến mô hình.  
*Ví dụ:* Nhân viên dùng ứng dụng chat AI để tạo ý tưởng marketing.

**Scope 2 – Ứng dụng doanh nghiệp:**  
Dùng ứng dụng doanh nghiệp có tích hợp generative AI, có quan hệ hợp đồng với nhà cung cấp.  
*Ví dụ:* Ứng dụng lập lịch họp dùng AI để gợi ý agenda.

---

### **Xây dựng generative AI**

**Scope 3 – Mô hình pretrained:**  
Tổ chức xây ứng dụng dựa trên một mô hình nền bên thứ ba.  
*Ví dụ:* Xây chatbot chăm sóc khách hàng dùng Claude thông qua Amazon Bedrock.

**Scope 4 – Mô hình fine-tuned:**  
Tổ chức tinh chỉnh (fine-tune) mô hình nền với dữ liệu doanh nghiệp.  
*Ví dụ:* Tinh chỉnh Titan để tạo content marketing phù hợp thương hiệu.

**Scope 5 – Mô hình tự huấn luyện:**  
Tổ chức xây và huấn luyện mô hình mới hoàn toàn từ dữ liệu tự sở hữu.  
*Ví dụ:* Tạo LLM ngành chuyên sâu để cấp phép cho khách hàng doanh nghiệp.

---

## Các lĩnh vực bảo mật trong Ma trận Phạm vi

5 lĩnh vực bảo mật cốt lõi cần xem xét:

- **Quản trị & tuân thủ (Governance & Compliance)**  
- **Pháp lý & quyền riêng tư (Legal & Privacy)**  
- **Quản lý rủi ro (Risk Management)**  
- **Kiểm soát bảo mật (Security Controls)**  
- **Khả năng sẵn sàng & phục hồi (Resilience)**  

---

## Ưu tiên bảo mật theo từng phạm vi

### **Quản trị, tuân thủ, pháp lý và quyền riêng tư**

Với các ứng dụng Off-the-shelf (Scope 1–2), cần xem xét kỹ:  
- Điều khoản dịch vụ, giấy phép, sovereignty dữ liệu  
- Loại dữ liệu được phép đưa vào hệ thống  
- Chính sách không đưa PII hoặc dữ liệu mật vào Scope 1 theo yêu cầu tổ chức  

Nếu cần khai thác dữ liệu nội bộ → cần phạm vi 3–5.

**RAG (Retrieval Augmented Generation)** là lựa chọn thay thế cho fine-tuning khi muốn mô hình dùng dữ liệu riêng mà không “nhồi” vào mô hình. RAG giúp cung cấp dữ liệu theo ngữ cảnh ngay trong prompt.

Ở Scope 4–5, mô hình phải được gán mức phân loại dữ liệu cao nhất từng dùng để huấn luyện. Nếu fine-tuning với PII → mô hình chứa PII → có nguy cơ bị trích xuất qua inference.

---

## Pháp lý

- Scope 1–4: tuân thủ EULA/TOS của nhà cung cấp dịch vụ và nhà cung cấp mô hình.  
- Scope 5: tổ chức phải tự soạn điều khoản khi cung cấp mô hình ra ngoài.  
- Cân nhắc GDPR “right to erasure”: cách duy nhất để xóa dữ liệu khỏi mô hình là huấn luyện lại mô hình mới.

---

## Quản lý rủi ro

LLM yêu cầu đánh giá rủi ro kỹ lưỡng hơn, vì tương tác đầu vào mở (free-form).

Cơ chế phổ biến:  
- **Risk assessment** cho Scope 1–2  
- **Threat modeling** cho Scope 3–5  

Ví dụ mối đe dọa đặc thù: **Prompt Injection**, tương tự các kiểu injection truyền thống (SQLi, JSONi…).

Các framework như NIST, MITRE, OWASP đã đưa prompt injection vào danh sách đe dọa hàng đầu.

---

## Kiểm soát bảo mật

Một ví dụ trọng tâm: **IAM & least privilege**.

Các mô hình (Scope 3–5) là **immutable** trong quá trình inference → không có kiểm soát truy cập theo embedding. Nếu được quyền truy cập mô hình, người dùng có thể truy cập toàn bộ khả năng inference trên dữ liệu mô hình đã huấn luyện.

Do đó cần:  
- Lớp ứng dụng phía trước để xác thực/ủy quyền  
- Chọn mô hình dựa trên quyền người dùng  
- Sử dụng RAG + phân quyền truy cập dữ liệu trong kho dữ liệu gắn kèm (Kendra, OpenSearch…)

IAM chỉ kiểm soát ở cấp “truy cập mô hình” chứ không kiểm soát “dữ liệu trong mô hình”.

---

## Khả năng sẵn sàng & phục hồi (Resilience)

Scope 1–2:  
- Xem SLA của nhà cung cấp  
- Tính toán tác động nếu API hoặc mô hình bị gián đoạn  
- Kiểm soát hạn mức sử dụng, chi phí

Scope 3–5:  
- Thiết lập timeout thích hợp  
- Theo dõi độ dài prompt  
- Mô hình hóa retry / backoff / circuit breaker  
- Cấu hình HA cho vector database  
- Multi-Region: kiểm tra khả năng hỗ trợ mô hình, dữ liệu fine-tuning, checkpoint cho training SageMaker

Hãy tham khảo AWS Resilience Hub & Well-Architected Framework (Reliability, Operational Excellence).

---

# Kết luận

Bài viết này mô tả cách các nguyên tắc bảo mật đám mây vẫn đóng vai trò nền tảng trong bảo mật generative AI. Bên cạnh các biện pháp an ninh quen thuộc, cần hiểu sâu các đặc tính của mô hình AI và mối đe dọa mới.

Hãy sử dụng **Generative AI Security Scoping Matrix** để xác định phạm vi và ưu tiên bảo mật theo từng lớp AT (attack surface). Sau khi xác định đúng phạm vi, bạn có thể tập trung triển khai những yêu cầu bảo mật quan trọng nhất để đảm bảo ứng dụng generative AI được sử dụng an toàn trong tổ chức.

---

**Các bài trong series Securing Generative AI:**  
- Phần 1 – Giới thiệu Ma trận Phạm vi Bảo mật Generative AI (bài này)  
- Phần 2 – Thiết kế workload Generative AI có khả năng chịu lỗi  
- Phần 3 – Áp dụng các cơ chế kiểm soát bảo mật liên quan  
- Phần 4 – Dữ liệu, tuân thủ và quyền riêng tư trong Generative AI
