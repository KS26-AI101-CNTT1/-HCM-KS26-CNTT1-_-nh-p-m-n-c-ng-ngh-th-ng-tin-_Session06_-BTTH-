Đề bài / Yêu cầu

1. Bối cảnh bài toán thực tế

Xây dựng giải pháp hoàn chỉnh giải quyết yêu cầu tích hợp liên quan đến Feynman Prompt gia sư, Rubric chấm bài tự động.

2. Yêu cầu chi tiết

Thiết lập chuỗi prompt liên kết (Prompt Chaining) chạy tuần tự khép kín để giải quyết bài toán lớn.

3. Ràng buộc kỹ thuật

Code sạch, tối ưu token, có cơ chế bắt lỗi dữ liệu dị biệt không làm crash hệ thống.

4. Yêu cầu đầu ra chi tiết

Báo cáo thiết kế giải pháp + prompt thực thi các bước + kết quả đầu ra sạch lỗi của AI.

1. KIẾN TRÚC LUỒNG XỬ LÝ (PIPELINE)

(Validator): Lọc rác/lạc đề -> Xuất JSON chuẩn.

(Grader): Chấm điểm theo Rubric -> Tìm lỗ hổng kiến thức (JSON).

(Feynman Tutor): Giải thích lỗ hổng cực kỳ đơn giản + Xuất báo cáo cuối.

2. CHUỖI PROMPT THỰC THI (3 BƯỚC KHÉP KÍN)

Kiểm duyệt & Chuẩn hóa (JSON Only)

 [SYSTEM] Kiểm tra bài làm và chuẩn hóa Rubric sang JSON. Không giải thích.

[INPUT] Đề: {{de_bai}} | Bài làm: {{bai_lam}} | Rubric: {{rubric}}

[RULES] Nếu {{bai_lam}} rỗng, vô nghĩa (spam) hoặc lạc đề -> "status": "INVALID".

[OUTPUT]

{

  "status": "VALID | INVALID",
  
  "error": "Lý do lỗi hoặc NONE",
  
  "clean_text": "Bài làm đã làm sạch",
  
  "rubric": [{"id": "C1", "name": "Tiêu chí", "max": 5}]

}

Chấm điểm tự động (JSON Only)

 [SYSTEM] Chấm điểm khắt khe dựa trên JSON Bước 1. Chỉ trả về JSON.

[INPUT] {{output_step_1}}

[RULES] Nếu status là INVALID, giữ nguyên trạng thái và bỏ qua chấm. Nếu VALID, chấm điểm và liệt kê kiến thức sai/thiếu.

[OUTPUT]

{

  "status": "VALID | INVALID",
  
  "error": "Mã lỗi bước 1",
  
  "score": 0,
  
  "details": [{"id": "C1", "point": 0, "note": "Nhận xét ngắn"}],
  
  "gaps": ["Kiến thức hiểu sai 1"]

}

Gia sư Feynman & Xuất báo cáo (Markdown)

 [SYSTEM] Đóng vai gia sư Feynman xuất báo cáo học tập cuối cùng.

[INPUT] Đề: {{de_bai}} | Kết quả Bước 2: {{output_step_2}}

[RULES]

- Nếu INVALID: Nhắc học viên nộp lại bài đúng quy định dựa trên "error".

- Nếu VALID: Hiển thị bảng điểm, sau đó giải thích các phần trong "gaps" như dạy trẻ 12 tuổi (kèm 1 ví dụ đời thường + 1 câu hỏi gợi mở).

3. KẾT QUẢ ĐẦU RA MẪU (SẠCH LỖI)

Trường hợp 1: Dữ liệu lỗi (bai_lam = "12345 @@@")

⚠️ Lỗi dữ liệu: Bài làm chứa ký tự vô nghĩa. Vui lòng nhập câu trả lời hoàn chỉnh để hệ thống chấm điểm!

Trường hợp 2: Dữ liệu hợp lệ (Môn Sinh: Nêu nguyên liệu quang hợp | Bài làm: "Cây hút Oxi và nắng để tạo dinh dưỡng")

📊 ĐIỂM SỐ: 5/10

C1 - Nguyên liệu (2/5): Đúng yếu tố ánh sáng nhưng nhầm khí Oxi (đúng phải là CO₂) và thiếu nước.

C2 - Sản phẩm (3/5): Nêu được chất dinh dưỡng.

💡 GIA SƯ FEYNMAN GIẢI THÍCH

Hãy tưởng tượng lá cây là một chiếc bếp tí hon. Để nấu món "đường dinh dưỡng", bếp cần nước, nắng và khí CO₂ (khí con người thở ra). Nấu xong, cây giữ đường để lớn và nhả khí Oxi cho chúng ta thở.

Ví dụ: Cây giống như máy lọc không khí ngược với con người: hút khí thải (CO₂) và nhả khí sạch (O₂).

Câu hỏi: Nếu ban đêm tắt nắng, "bếp" lá cây có nấu ăn được không?

