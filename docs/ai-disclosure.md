# BẢNG KHAI BÁO SỬ DỤNG CÔNG CỤ AI HỖ TRỢ
**Học phần:** Chuyên đề Tốt nghiệp 1 (Specialized Graduation Topic I)  
**Học kỳ:** HK1, Năm học 2026 – 2027  
**Bài nộp:** [x] Bài tập 1 (BT1)   [ ] Bài tập 2 (BT2)   [ ] Bài tập 3 (BT3)  

---

### I. THÔNG TIN SINH VIÊN
- **Họ và tên:** Phan Văn Hoàng Long
- **Mã số sinh viên:** 2374802010283
- **Chuyên ngành (Track):** [x] SE   [ ] DA   [ ] AI
- **Luồng nghiệp vụ đã chọn:** L2 – Tiếp nhận và phân loại yêu cầu bảo hành

---

### II. BẢNG KHAI BÁO CHI TIẾT

| Công cụ AI | Dùng vào việc gì | Áp dụng ở phần nào (File / Mục) | Đã kiểm chứng & Chỉnh sửa thế nào |
| :--- | :--- | :--- | :--- |
| **Claude** | Cung cấp tài liệu Case Study (Mekong Mobile) để nhờ Claude phân tích, trích xuất yêu cầu và sinh bản nháp SRS, User Story, API Contract. | Mục 1, 2, 3 và file `api-contract.md` | Em đã đọc lại toàn bộ kết quả AI sinh ra, đối chiếu chéo với 15 quy tắc nghiệp vụ (đặc biệt là QT-04 tính hạn cam kết và QT-05 xác định bảo hành). Có yêu cầu AI viết lại nhiều lần để loại bỏ các chức năng vượt phạm vi (như duyệt phiếu, phân công kỹ thuật viên). |
| **Claude** | Sinh mã nguồn DBML để thiết kế CSDL và gợi ý kiến trúc hệ thống (phân lớp). | Mục 3 (Kiến trúc) và Mục 4 (`schema.dbml`) | Em đã đưa code DBML của Claude lên `dbdiagram.io` để render ra ERD trực quan, kiểm tra lại thấy đủ 6 bảng, đủ khóa ngoại và đạt chuẩn 3NF. Về kiến trúc, em đã tự đối chiếu lại 3 câu lập luận của AI với các yêu cầu NFR để đảm bảo logic. |
| **Claude** | Phân tích quan hệ `<<include>>`, `<<extend>>` cho Use Case và gợi ý các thành phần (components) cần có trên Wireframe. | Mục 2 (Use Case Diagram) và Mục 5 (Wireframe) | Từ cấu trúc XML/text do Claude sinh ra, em đưa vào Draw.io để điều chỉnh lại layout, nối lại các đường nét đứt cho đúng chuẩn UML. Với Wireframe, em kiểm tra lại để đảm bảo mọi trường dữ liệu (field) trên màn hình đều map đúng với bảng CSDL ở Mục 4. |
| **Không dùng** | Setup cấu trúc Git Repo, viết commit và đóng gói báo cáo PDF. | GitHub Repository (`.gitignore`, `README.md`) | Em tự tạo cấu trúc thư mục, tự push code theo chuẩn Conventional Commits và tự kiểm tra các mục checklist trước khi nộp. |

---

### III. CAM KẾT VỀ LIÊM CHÍNH HỌC THUẬT

> **"Tôi xác nhận đã đọc, hiểu và chịu trách nhiệm về toàn bộ nội dung nộp."**

- **Chữ ký / Họ tên sinh viên:** Phan Văn Hoàng Long  
- **MSSV:** 2374802010283  
- **Ngày khai báo:** 10/10/2026