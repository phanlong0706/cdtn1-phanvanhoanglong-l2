# API Contract – L2 Tiếp nhận và phân loại yêu cầu bảo hành

Định dạng: JSON, UTF-8. Không có đăng nhập thật (WON'T). Vai trò truyền qua header `X-Role` (`NHAN_VIEN_TIEP_NHAN` là mặc định, hoặc `QUAN_LY`). Với vai trò khác `QUAN_LY`, số điện thoại trong mọi response được che (QT-15): `090****567`.

Giá trị mã dùng trong API: mức ưu tiên `CAO` / `TRUNG_BINH` / `THAP`; nhóm sự cố `MAN_HINH` / `PIN` / `SAC` / `PHAN_MEM` / `NUOC_VAO` / `KHAC`; trạng thái phiếu `MOI`; tình trạng bảo hành `CON_BAO_HANH` / `HET_BAO_HANH` / `CHUA_XAC_MINH`.

## 1. Danh sách endpoint

| Phương thức | Đường dẫn | Mục đích | FR |
| :--- | :--- | :--- | :--- |
| GET | /health | Kiểm tra hệ thống đang chạy | – |
| GET | /issue-categories | Danh sách nhóm sự cố kèm mức ưu tiên mặc định | FR4 |
| GET | /customers/{phone} | Tra cứu khách hàng theo số điện thoại | FR1 |
| POST | /customers | Tạo khách hàng mới (hoặc trả hồ sơ có sẵn) | FR2 |
| POST | /tickets | Tạo phiếu bảo hành | FR3, FR4, FR5, FR7 |
| GET | /tickets | Danh sách phiếu (lọc trung tâm, trạng thái; phân trang) | FR6 |
| GET | /tickets/{id} | Chi tiết một phiếu | FR6 |

## 2. Chi tiết từng endpoint

### GET /health
- 200: `{"status": "ok"}`

### GET /issue-categories
- 200:
```json
[{"category_id": 1, "category_code": "MAN_HINH", "category_name": "Màn hình", "default_priority": "CAO"}]
```

### GET /customers/{phone}
Số điện thoại trong đường dẫn được chuẩn hóa trước khi tra (QT-02).
- 200: `{"customer_id": 12, "phone": "090****567", "full_name": "Nguyễn Văn An"}`
- 400: `{"error": "Số điện thoại không hợp lệ"}`
- 404: `{"error": "Không tìm thấy khách hàng"}`

### POST /customers
Request:
```json
{"phone": "+84 901 234 567", "full_name": "Nguyễn Văn An"}
```
- 201 (tạo mới): `{"customer_id": 12, "phone": "090****567", "full_name": "Nguyễn Văn An"}`
- 200 (số đã tồn tại, trả hồ sơ có sẵn, không tạo mới – QT-01): cùng dạng như trên
- 400: `{"error": "Số điện thoại không hợp lệ"}`

### POST /tickets
Request:
```json
{
  "phone": "0901234567",
  "center_id": 3,
  "device": {"model_name": "iPhone 13", "serial_no": "SN12345678", "purchase_date": "2026-01-15"},
  "issue_desc": "Màn hình bị sọc ngang sau khi rơi",
  "category_code": "MAN_HINH",
  "priority": "CAO"
}
```
`priority` có thể bỏ trống: hệ thống dùng mức ưu tiên mặc định của nhóm sự cố. `purchase_date` có thể bỏ trống.

Response 201 (tiếp nhận 10:00 thứ Hai 12/10/2026, mức Cao):
```json
{
  "ticket_id": 101,
  "ticket_code": "BH-000101/2026",
  "status": "MOI",
  "warranty_status": "CON_BAO_HANH",
  "received_at": "2026-10-12T10:00:00",
  "due_date": "2026-10-13T10:00:00"
}
```
- 400: `{"error": "Mô tả lỗi quá ngắn, cần ít nhất 10 ký tự"}` (vi phạm bất kỳ quy tắc validation nào)
- 404: `{"error": "Không tìm thấy khách hàng"}` (cần gọi POST /customers trước)
- 409: `{"error": "Số serial/IMEI đang thuộc khách hàng khác"}` (QT-03)
- 500: `{"error": "Lỗi hệ thống, phiếu chưa được tạo"}`

### GET /tickets
Query: `center_id` (bắt buộc), `status` (tùy chọn), `page` (mặc định 1), `page_size` (mặc định 20, tối đa 100).
Response 200:
```json
{"page": 1, "page_size": 20, "total": 1,
 "items": [{"ticket_id": 101, "ticket_code": "BH-000101/2026", "full_name": "Nguyễn Văn An",
            "phone": "090****567", "model_name": "iPhone 13", "category_name": "Màn hình",
            "priority": "CAO", "status": "MOI", "warranty_status": "CON_BAO_HANH",
            "received_at": "2026-10-12T10:00:00", "due_date": "2026-10-13T10:00:00", "is_overdue": false}]}
```
- 400: `{"error": "Thiếu center_id hoặc page/page_size không hợp lệ"}`

### GET /tickets/{id}
- 200: một phiếu đầy đủ trường như một phần tử `items` ở trên, thêm `issue_desc`, `serial_no`, `purchase_date`
- 404: `{"error": "Không tìm thấy phiếu"}`

## 3. Bảng validation từng trường

| Trường | Bắt buộc | Kiểu | Quy tắc |
| :--- | :--- | :--- | :--- |
| phone | Có | string | Chuẩn hóa (bỏ dấu cách, dấu chấm, dấu gạch; +84 hoặc 84 đổi thành 0); sau đó đúng 10 chữ số, bắt đầu bằng 0 |
| full_name | Có (khi tạo khách) | string | 2–120 ký tự |
| center_id | Có | integer | Phải là trung tâm bảo hành tồn tại |
| device.model_name | Có | string | 1–100 ký tự |
| device.serial_no | Có | string | 5–50 ký tự chữ và số; duy nhất; không thuộc khách khác |
| device.purchase_date | Không | date (YYYY-MM-DD) | Không sau ngày tiếp nhận |
| issue_desc | Có | string | Từ 10 đến 1000 ký tự sau khi bỏ khoảng trắng đầu/cuối |
| category_code | Có | string | Một trong 6 nhóm sự cố đang sử dụng |
| priority | Không | string | CAO, TRUNG_BINH hoặc THAP; bỏ trống thì dùng mặc định của nhóm |
| status (lọc) | Không | string | MOI |
| page | Không | integer | ≥ 1 |
| page_size | Không | integer | 1–100 |

## 4. Quy tắc tính hạn cam kết (QT-04)
Hạn = thời điểm tiếp nhận + 24 giờ (CAO), 72 giờ (TRUNG_BINH) hoặc 120 giờ (THAP); các giờ nằm trong Chủ nhật không được tính.