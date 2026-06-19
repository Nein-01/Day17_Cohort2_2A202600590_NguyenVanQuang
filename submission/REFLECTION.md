# Reflection — Day 17 (≤ 200 words)

Answer briefly, in your own words. This is graded on reasoning, not length.

1. **The flywheel.** Day 13 emitted agent traces; today you turned them into an
   eval set and DPO pairs that Day 22 will train on. Which step in
   `traces → Bronze → datasets` would break most silently in production if you
   got it wrong — and how would you detect it?

2. **Decontamination.** Your run dropped 2 of 3 preference pairs because their
   prompts were in the eval set. What concretely goes wrong if you *skip* this
   step and train on those pairs? How would the lie show up in your metrics?

3. **Point-in-time.** The naive join leaked a future `lifetime_spend` into the
   training row. Describe one feature in a system you know that would be
   dangerous to join without an `ASOF`/point-in-time guard.

4. **Graph vs vector.** From `kg_demo.py`, name one question the knowledge graph
   answers well that flat chunk retrieval (`embed.py`) would struggle with, and
   one where the graph is overkill.

_Write your answers below._

1. Bước dễ hỏng âm thầm nhất là chuẩn hóa trace thành Bronze/dataset: sai schema, sai role, mất timestamp hoặc đảo chosen/rejected vẫn có thể chạy được nhưng tạo dữ liệu học sai. Phát hiện bằng cách kiểm tra contract, thống kê phân phối, sample audit thủ công và so sánh tỉ lệ lỗi/độ dài/role qua từng batch.

2. Nếu bỏ decontamination, model sẽ học trực tiếp prompt của eval. Điểm eval tăng giả vì nó đã thấy câu hỏi hoặc preference tương tự, không phải vì tổng quát hóa tốt hơn. Metrics sẽ có train-eval gap bất thường: eval win-rate/accuracy cao nhưng prompt mới hoặc holdout sạch giảm.

3. Trong hệ thống CRM, feature "tổng số lần mua trong 30 ngày sau đăng ký" rất nguy hiểm nếu join vào dòng dự đoán churn tại ngày đăng ký. Nó có chứa kết quả cần dự đoán ở tương lai, nên cần ASOF theo thời điểm dự đoán.

4. Graph trả lời tốt câu multi-hop: "widget ship from where?" vì nối được widget -> accessory -> Hanoi. Vector chunk retrieval phẳng khó vì không chunk nào chứa cả hai fact. Ngược lại, hỏi đoạn văn có nhắc chính sách đổi trả hoặc embedding/chunk text nào gần nhất thì graph là quá mức cần thiết.
