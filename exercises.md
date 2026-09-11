# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> 
Ở temperature 0.0, model gần như luôn trả về cùng một câu trả lời, diễn đạt an toàn và ít sáng tạo. Khi tăng lên 0.5 và 1.0, câu chữ bắt đầu đa dạng hơn, đôi khi chọn sự thật khác hoặc cách diễn đạt khác lạ hơn. Ở 1.5, phản hồi có thể trở nên lan man, thiếu mạch lạc hoặc thậm chí sai lệch thông tin do model "liều" chọn những từ ít khả năng hơn.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> 
Chatbot hỗ trợ khách hàng nên đặt temperature thấp (khoảng 0.0–0.3), vì cần câu trả lời nhất quán, chính xác, đúng chính sách công ty, tránh việc model "sáng tạo" ra thông tin sai hoặc mâu thuẫn giữa các lần trả lời.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> 
GPT-4o thường đắt hơn GPT-4o-mini khoảng 15–20 lần tùy thời điểm giá. Với workload 10.000 người dùng × 3 lần/ngày, chênh lệch chi phí là rất lớn nên cần cân nhắc kỹ. GPT-4o đáng dùng khi tác vụ đòi hỏi suy luận phức tạp, độ chính xác cao (ví dụ tư vấn pháp lý, phân tích tài chính); GPT-4o-mini phù hợp cho các tác vụ đơn giản, lặp lại như trả lời FAQ, phân loại câu hỏi.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
>
Số token thực tế theo tiktoken thường cao hơn ước lượng "số từ / 0.75" khoảng 20–40% đối với tiếng Việt. Lý do là tiếng Việt có dấu thanh và nhiều âm tiết đơn, mỗi âm tiết/dấu thường bị tách thành nhiều token con (do tokenizer được huấn luyện chủ yếu trên tiếng Anh), trong khi công thức 0.75 chỉ đúng với tiếng Anh vốn có cấu trúc từ khác biệt.
### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> 
Số token thực tế theo tiktoken thường cao hơn ước lượng "số từ / 0.75" khoảng 20–40% đối với tiếng Việt. Lý do là tiếng Việt có dấu thanh và nhiều âm tiết đơn, mỗi âm tiết/dấu thường bị tách thành nhiều token con (do tokenizer được huấn luyện chủ yếu trên tiếng Anh), trong khi công thức 0.75 chỉ đúng với tiếng Anh vốn có cấu trúc từ khác biệt.
---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi câu trả lời dài và người dùng cần cảm giác phản hồi tức thì, ví dụ chatbot trò chuyện hay trợ lý viết nội dung — nhìn chữ xuất hiện dần giúp giảm cảm giác chờ đợi. Ngược lại, non-streaming phù hợp hơn khi cần xử lý toàn bộ kết quả trước khi hiển thị, ví dụ khi output cần parse thành JSON hoặc dùng cho tác vụ nền không có giao diện tương tác.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff giúp giãn dần thời gian chờ giữa các lần retry, tránh việc liên tục "dội bom" request vào server đang quá tải. Nếu hàng nghìn client cùng retry với delay cố định giống nhau, tất cả sẽ gửi lại request cùng lúc theo chu kỳ, tạo ra các đợt "thundering herd" khiến server càng nghẽn nặng hơn thay vì phục hồi.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Ví dụ chọn persona "trợ lý học tập thân thiện, trả lời bằng tiếng Việt, ngắn gọn, dễ hiểu". System prompt: "Bạn là trợ lý học tập, luôn trả lời bằng tiếng Việt, giải thích ngắn gọn trong 3-4 câu, ưu tiên ví dụ thực tế." Yêu cầu "ngắn gọn" giúp tránh câu trả lời lan man gây khó theo dõi cho người mới học; chỉ định rõ "tiếng Việt" để tránh model tự chuyển sang tiếng Anh khi gặp thuật ngữ kỹ thuật.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất thường là lịch sử hội thoại chỉ lưu vài lượt gần nhất nên trợ lý dễ "quên" ngữ cảnh cũ. Cải thiện: thêm bộ nhớ dài hạn bằng cách tóm tắt định kỳ các đoạn hội thoại cũ thành vài câu ngắn rồi lưu vào một biến "context tóm tắt", đưa kèm vào system prompt ở mỗi lượt gọi API mới thay vì giữ nguyên toàn bộ lịch sử thô.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
