# SRS rút gọn – Smart CRM Mekong Mobile – Luồng L2: Tiếp nhận và phân loại yêu cầu bảo hành

Sinh viên: Phan Văn Hoàng Long – MSSV 2374802010283 – Track SE.

## 1. Giới thiệu và phạm vi

**Bối cảnh:** Mekong Mobile có 6 trung tâm bảo hành, 12 nhân viên tiếp nhận, khoảng 260 yêu cầu bảo hành mỗi tháng. Phiếu giấy mỗi nơi lưu một cách, khoảng 15% phiếu quá hạn mà không được cảnh báo (V2); mô tả lỗi ghi tự do nên không thống kê được (V8).

**Phạm vi:** Nhân viên tiếp nhận nhập số điện thoại để tra cứu khách hàng, ghi nhận thiết bị và mô tả lỗi, chọn nhóm sự cố và mức ưu tiên; hệ thống tự xác định tình trạng bảo hành, tính hạn cam kết (SLA) và lưu phiếu bảo hành ở trạng thái "Mới".

**Chủ ý KHÔNG làm (WON'T):** đăng nhập và xác thực thật (vai trò chỉ truyền kèm yêu cầu để mô phỏng); quản lý phê duyệt phiếu "Chưa xác minh bảo hành"; phân công kỹ thuật viên và lịch hẹn (L4); kho linh kiện (L5); phân loại tự động bằng AI (L10); khảo sát hài lòng (L8); các chuyển trạng thái sau "Mới"; phân quyền theo tài khoản (QT-14).

**Bảng thuật ngữ** (theo Bảng 3.1 của case study)

| Thuật ngữ | Tên trong CSDL | Giải nghĩa |
| :--- | :--- | :--- |
| Khách hàng | customer | Cá nhân đã mua hoặc dùng dịch vụ của Mekong Mobile |
| Thiết bị | device | Một máy cụ thể, xác định bằng số serial/IMEI |
| Phiếu bảo hành | ticket | Một yêu cầu bảo hành được ghi nhận, có mã duy nhất |
| Trạng thái phiếu | ticket.status | Vị trí của phiếu trong vòng đời; phiếu mới là "Mới" |
| Hạn cam kết (SLA) | ticket.due_date | Thời điểm chậm nhất phải hoàn tất phiếu, tính từ lúc tiếp nhận theo mức ưu tiên |
| Nhóm sự cố | issue_category | Màn hình, Pin, Sạc, Phần mềm, Nước vào, Khác |
| Mức ưu tiên | ticket.priority | Cao, Trung bình, Thấp |
| Trung tâm bảo hành | service_center | Nơi tiếp nhận phiếu |
| Tình trạng bảo hành | ticket.warranty_status | Còn bảo hành, Hết bảo hành, Chưa xác minh bảo hành |
| Lịch sử trạng thái | ticket_status_log | Bản ghi mỗi lần đổi trạng thái, kèm thời điểm và người thực hiện |

## 2. Các bên liên quan và vai trò người dùng

| Vai trò | Được làm | Không được làm |
| :--- | :--- | :--- |
| Nhân viên tiếp nhận (12 người) | Tra cứu khách, tạo khách mới, tạo phiếu, chọn nhóm sự cố và mức ưu tiên, xem danh sách phiếu | Xem số điện thoại đầy đủ (chỉ thấy dạng che), sửa hạn cam kết, xóa phiếu |
| Quản lý trung tâm (6 người) | Xem danh sách phiếu và phiếu quá hạn; xem số điện thoại đầy đủ | Sửa hạn cam kết, xóa phiếu |

## 3. Yêu cầu chức năng

| Mã | Yêu cầu chức năng (kiểm chứng được) |
| :--- | :--- |
| FR1 | Tra cứu khách hàng theo số điện thoại (đã chuẩn hóa) và hiển thị họ tên; nếu chưa có thì báo "Không tìm thấy khách hàng". |
| FR2 | Tạo khách hàng mới (số điện thoại, họ tên) khi số điện thoại chưa tồn tại; nếu đã tồn tại thì hiển thị hồ sơ có sẵn, không tạo mới. |
| FR3 | Tạo phiếu bảo hành với trường bắt buộc: khách hàng, thiết bị (model, serial/IMEI), trung tâm bảo hành, mô tả lỗi từ 10 ký tự; thiếu trường nào thì từ chối lưu và nêu rõ trường đó. Phiếu mới có mã duy nhất, trạng thái "Mới" và một dòng lịch sử trạng thái đầu tiên. |
| FR4 | Chọn một trong 6 nhóm sự cố và một trong 3 mức ưu tiên; mức ưu tiên được gợi ý sẵn theo nhóm sự cố, nhân viên có thể đổi. |
| FR5 | Tự tính hạn cam kết từ lúc tiếp nhận: Cao 24 giờ, Trung bình 72 giờ, Thấp 120 giờ, không tính Chủ nhật (QT-04). |
| FR6 | Hiển thị danh sách phiếu của một trung tâm, mới nhất ở đầu, lọc theo trạng thái phiếu, phân trang, đánh dấu phiếu quá hạn cam kết. |
| FR7 | Tự xác định tình trạng bảo hành khi tiếp nhận: Còn bảo hành, Hết bảo hành, hoặc Chưa xác minh bảo hành khi không có ngày mua (QT-05). |

| Mã | User Story | MoSCoW |
| :--- | :--- | :--- |
| US1 | Là nhân viên tiếp nhận, tôi muốn tra cứu khách theo số điện thoại để không phải hỏi lại tên và lịch sử mua của khách mỗi lần. | MUST |
| US2 | Là nhân viên tiếp nhận, tôi muốn tạo hồ sơ khách mới khi số điện thoại chưa có để vẫn tiếp nhận được khách lần đầu ngay tại quầy. | SHOULD |
| US3 | Là nhân viên tiếp nhận, tôi muốn tạo phiếu với thiết bị và mô tả lỗi trên hệ thống để thay phiếu giấy và không phải chép lại sang Excel cuối ngày (khoảng 40 phút mỗi ngày). | MUST |
| US4 | Là nhân viên tiếp nhận, tôi muốn chọn nhóm sự cố và mức ưu tiên (có gợi ý sẵn) để phân loại nhất quán và thống kê được nguyên nhân bảo hành phổ biến. | MUST |
| US5 | Là quản lý trung tâm, tôi muốn phiếu có hạn cam kết tự tính theo mức ưu tiên để không còn hẹn ngày theo cảm tính và biết phiếu nào sắp quá hạn. | MUST |
| US6 | Là quản lý trung tâm, tôi muốn xem danh sách phiếu kèm hạn cam kết và dấu quá hạn để biết hôm nay có bao nhiêu phiếu và phiếu nào cần xử lý gấp. | SHOULD |
| US7 | Là nhân viên tiếp nhận, tôi muốn biết ngay thiết bị còn bảo hành hay không để không nhận nhầm máy hết hạn hay ghi "còn bảo hành" tùy tiện. | SHOULD |

**Tiêu chí chấp nhận tiêu biểu (Given – When – Then)**
- US1: GIVEN số 0901234567 đã có, WHEN nhập số này, THEN hiển thị họ tên khách; GIVEN số chưa có, THEN báo "Không tìm thấy khách hàng".
- US3: GIVEN mô tả lỗi dưới 10 ký tự, WHEN bấm Lưu, THEN từ chối, nêu rõ lỗi, không lưu phiếu.
- US5: GIVEN phiếu mức Cao tiếp nhận 10:00 thứ Hai, THEN hạn cam kết là 10:00 thứ Ba; nếu tiếp nhận 10:00 thứ Bảy thì hạn là 10:00 thứ Hai (không tính Chủ nhật).

## 4. Yêu cầu phi chức năng

| Mã | Loại | Yêu cầu (có ngưỡng đo được) |
| :--- | :--- | :--- |
| NFR1 | Hiệu năng | Danh sách phiếu hiển thị dưới 2 giây với 10.000 phiếu trên máy 8 GB RAM; tạo một phiếu phản hồi dưới 1 giây. |
| NFR2 | Tin cậy | 100% phiếu đã báo "lưu thành công" vẫn còn sau khi khởi động lại hệ thống; một lần bấm Lưu không tạo quá 1 phiếu. |
| NFR3 | Bảo trì | Thêm một nhóm sự cố mới chỉ cần thêm 1 dòng dữ liệu cấu hình: 0 dòng mã nguồn bị sửa, không triển khai lại, dưới 5 phút. |
| NFR4 | Khả chuyển | Khởi động được trên Windows, macOS và Linux bằng đúng 2 lệnh (cài thư viện, chạy ứng dụng). |
| NFR5 | Khả dụng | Nhân viên tiếp nhận mới tạo được một phiếu đúng trong dưới 3 phút, không cần hỏi đồng nghiệp. |

## 5. Ràng buộc và quy tắc nghiệp vụ

| Mã | Quy tắc | Nguồn | FR |
| :--- | :--- | :--- | :--- |
| QT-01 | Số điện thoại khách duy nhất; nhập số đã có thì hiển thị hồ sơ có sẵn, không tạo mới. | Bảng 9.1 | FR1, FR2 |
| QT-02 | Số điện thoại chuẩn hóa về 10 chữ số bắt đầu bằng 0 trước khi lưu (các dạng +84…, 84…, dấu cách, dấu chấm đều quy về dạng chuẩn). | Bảng 9.1 | FR1, FR2, FR3 |
| QT-03 | Thiết bị xác định duy nhất bằng serial/IMEI; một thiết bị chỉ thuộc một khách hàng tại một thời điểm. | Bảng 9.1 | FR3 |
| QT-04 | Hạn cam kết sinh từ lúc tiếp nhận: Cao 24 giờ, Trung bình 72 giờ, Thấp 120 giờ; chỉ tính thứ Hai đến thứ Bảy, Chủ nhật không tính. | Bảng 9.1 | FR5 |
| QT-05 | Còn bảo hành nếu (ngày tiếp nhận − ngày mua) ≤ số tháng bảo hành (mặc định 12); không có ngày mua thì đánh dấu "Chưa xác minh bảo hành". | Bảng 9.1; Mục 6.1 bước 4 | FR7 |
| QT-06 | Mọi lần chuyển trạng thái phải ghi lịch sử; phiếu mới có trạng thái "Mới" kèm dòng lịch sử đầu tiên. | Bảng 9.1 | FR3 |
| QT-13 | Không xóa vật lý phiếu hoặc hồ sơ khách; hệ thống không có chức năng xóa. | Bảng 9.1 | Tất cả |
| QT-14 | Danh sách phiếu chỉ hiển thị phiếu của trung tâm được chọn; phân quyền theo tài khoản là WON'T. | Bảng 9.1 | FR6 |
| QT-15 | Số điện thoại hiển thị dạng che (ví dụ 090****567) với mọi vai trò trừ Quản lý trung tâm. | Bảng 9.1 | FR1, FR6 |
| PT-01 | Mô tả lỗi từ 10 ký tự trở lên (chặn mô tả cho có như "hư", "bể"). | Phân tích của em | FR3 |
| PT-02 | Nhóm sự cố chỉ nhận 6 giá trị đã nêu; mức ưu tiên chỉ nhận Cao, Trung bình, Thấp. | Hình 5.1; Bảng 3.1 | FR4 |

## 6. Bảng truy vết yêu cầu

| Mã FR | Yêu cầu chức năng | User Story | Use Case | MoSCoW | Test case (BT3) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| FR1 | Tra cứu khách theo số điện thoại | US1 | UC1 | MUST | TC01, TC02 |
| FR2 | Tạo khách mới / hiển thị hồ sơ có sẵn | US2 | UC3 | SHOULD | TC03, TC04, TC05 |
| FR3 | Tạo phiếu với trường bắt buộc | US3 | UC2 | MUST | TC06, TC07, TC08 |
| FR4 | Chọn nhóm sự cố, mức ưu tiên (gợi ý sẵn) | US4 | UC4 | MUST | TC09, TC10 |
| FR5 | Tự tính hạn cam kết | US5 | UC5 | MUST | TC11, TC12 |
| FR6 | Xem danh sách phiếu, đánh dấu quá hạn | US6 | UC6 | SHOULD | TC13, TC14 |
| FR7 | Xác định tình trạng bảo hành | US7 | UC7 | SHOULD | TC15, TC16, TC17 |