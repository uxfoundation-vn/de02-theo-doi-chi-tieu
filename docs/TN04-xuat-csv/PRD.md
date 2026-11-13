> **Mã:** DE02-TN04-PRD · **Trạng thái:** nháp · **Người viết:** Design (mock) · **SRS:** [SRS.md](SRS.md)

# PRD Xuất CSV (App theo dõi chi tiêu)

> **Bản mock.** Con số đánh dấu `[giả định]` dùng để dựng flow, cần PO xác nhận trước khi dùng.

## 1. Bối cảnh và vấn đề

### 1.1 Bối cảnh

Dữ liệu của app chỉ nằm trên thiết bị (xem [README](../../README.md)). Người dùng cần một cách mang dữ liệu ra ngoài: để sao lưu, để phân tích thêm trên bảng tính, hoặc để gửi cho người cùng quản lý chi tiêu.

### 1.2 Vấn đề

| **Vấn đề** | **Hệ quả** |
| --- | --- |
| Dữ liệu bị khoá trong app | Đổi máy hoặc xoá app là mất hết |
| Muốn phân tích sâu hơn báo cáo có sẵn | Phải chép tay sang bảng tính |
| File xuất mở ra bị lỗi font tiếng Việt | Người dùng nghĩ file hỏng |
| Số tiền có dấu ngăn nghìn trong file | Bảng tính không cộng được |
| Không biết file được lưu ở đâu | Xuất xong không tìm thấy |

### 1.3 Mục tiêu

- Xuất được dữ liệu trong ≤ 3 chạm kể từ màn xuất `[giả định]`.
- File mở đúng tiếng Việt trên Excel và Google Sheets, số tiền cộng được ngay.
- ≥ 10% người dùng hoạt động xuất ít nhất 1 lần trong 3 tháng `[giả định]`.

## 2. Người dùng

| **Nhóm** | **Nhu cầu với tính năng này** |
| --- | --- |
| Người mới đi làm | Sao lưu dữ liệu trước khi đổi điện thoại |
| Người quản lý chi tiêu gia đình | Gửi file tháng cho vợ, chồng; tổng hợp cả năm trên bảng tính |
| Freelancer | Lấy danh sách thu chi để kê khai, đối chiếu |

## 3. Phạm vi

### 3.1 Trong phạm vi (MVP)

- Chọn khoảng thời gian: tháng này, tháng trước, năm nay, tất cả, tuỳ chọn.
- Chọn loại giao dịch: tất cả, chỉ chi, chỉ thu.
- Xem trước số giao dịch sẽ xuất.
- Tạo file CSV và mở bảng chia sẻ của hệ điều hành để lưu hoặc gửi.

### 3.2 Ngoài phạm vi

- Nhập dữ liệu từ CSV → phase sau.
- Xuất Excel (.xlsx), PDF.
- Sao lưu tự động lên đám mây.
- Xuất ngân sách và danh mục thành file riêng.

## 4. Nguyên tắc sản phẩm

### 4.1 Dữ liệu là của người dùng

Xuất không giới hạn số lần, không cần tài khoản, không qua máy chủ.

### 4.2 Mở ra là dùng được

File mở đúng tiếng Việt, cột rõ ràng, số tiền là số thuần để cộng, lọc được ngay.

### 4.3 Biết trước sẽ nhận được gì

Trước khi xuất, người dùng thấy khoảng thời gian và số giao dịch sẽ có trong file.

### 4.4 Người dùng chọn nơi lưu

App không tự lưu vào chỗ khó tìm. File được đưa cho bảng chia sẻ của hệ điều hành để người dùng tự chọn lưu hay gửi.

## 5. Quy tắc nghiệp vụ

| **Quy tắc** | **Giá trị** `[giả định]` |
| --- | --- |
| Định dạng | CSV, mã hoá UTF-8 có BOM, dấu phẩy ngăn cột |
| Cột | Ngày, Loại, Danh mục, Số tiền, Ghi chú |
| Ngày | dd/mm/yyyy |
| Số tiền | Số nguyên, không dấu ngăn nghìn, không ký hiệu tiền; luôn dương, phân biệt bằng cột Loại |
| Sắp xếp | Ngày tăng dần |
| Tên file | chi-tieu_{yyyymmdd}-{yyyymmdd}.csv theo khoảng thời gian xuất |
| Khoảng tuỳ chọn | Ngày bắt đầu ≤ ngày kết thúc ≤ hôm nay |

- Các giá trị trên là **cấu hình**, không cố định trong code.

## 6. Luồng trải nghiệm

```mermaid
flowchart LR
  A["Cài đặt hoặc Báo cáo → Xuất"] --> B["Chọn khoảng thời gian"]
  B --> C["Chọn loại giao dịch"]
  C --> D["Xem số giao dịch sẽ xuất"]
  D --> E["Bấm Xuất CSV"]
  E --> F["Bảng chia sẻ của hệ điều hành"]
  F --> G["Lưu vào Tệp hoặc gửi qua ứng dụng khác"]
```

| **Bước** | **Giai đoạn** | **Trải nghiệm** | **Đầu ra** |
| --- | --- | --- | --- |
| 1 | Mở màn xuất | Từ Cài đặt hoặc nút Xuất ở Báo cáo (điền sẵn tháng đang xem) | Màn xuất |
| 2 | Chọn khoảng thời gian | Lối tắt hoặc tự chọn từ ngày, đến ngày | Khoảng thời gian |
| 3 | Chọn loại | Tất cả, chỉ chi, chỉ thu | Bộ lọc |
| 4 | Xem trước | "Sẽ xuất {n} giao dịch" cập nhật theo lựa chọn | Biết trước kết quả |
| 5 | Xuất | Tạo file, mở bảng chia sẻ | File CSV |
| 6 | Lưu, gửi | Người dùng chọn nơi lưu hoặc ứng dụng nhận | File ở nơi mong muốn |

## 7. Use Cases, User Stories và Acceptance Criteria

### UC1. Xuất dữ liệu

#### US1.1 Chọn dữ liệu cần xuất

User Story: Là người dùng, tôi muốn chọn khoảng thời gian cần xuất để chỉ lấy dữ liệu mình cần.

AC01: Có lối tắt Tháng này, Tháng trước, Năm nay, Tất cả và Tuỳ chọn.

AC02: Tuỳ chọn cho chọn từ ngày, đến ngày; không chọn được ngày tương lai; ngày bắt đầu không sau ngày kết thúc.

AC03: Chọn loại: Tất cả (mặc định), Chỉ chi, Chỉ thu.

AC04: Số giao dịch sẽ xuất cập nhật ngay khi đổi lựa chọn.

AC05: Không có giao dịch nào khớp → nút Xuất disabled, nói rõ lý do.

#### US1.2 Nhận file

User Story: Là người dùng, tôi muốn nhận file mở được ngay trên bảng tính để tiếp tục xử lý.

AC01: Bấm Xuất → tạo file rồi mở bảng chia sẻ của hệ điều hành.

AC02: File mở trên Excel và Google Sheets hiện đúng tiếng Việt.

AC03: Cột Số tiền là số thuần, cộng được bằng hàm tổng.

AC04: Ghi chú có dấu phẩy, dấu ngoặc kép, xuống dòng không làm lệch cột.

AC05: Tên file thể hiện khoảng thời gian đã xuất.

#### US1.3 Xử lý khi có sự cố

User Story: Là người dùng, tôi muốn được báo rõ khi xuất file gặp sự cố để biết cách thử lại.

AC01: Đang tạo file → hiện tiến trình, không cho bấm Xuất lần hai.

AC02: Tạo file thất bại → báo lỗi, cho thử lại, giữ nguyên lựa chọn.

AC03: Người dùng đóng bảng chia sẻ mà không lưu → không báo lỗi, ở lại màn xuất.

## 8. Chỉ số theo dõi `[giả định]`

| **Chỉ số** | **Định nghĩa** | **Mục tiêu MVP** |
| --- | --- | --- |
| Tỉ lệ dùng xuất | Người dùng xuất ≥ 1 lần trong 3 tháng / người dùng hoạt động | ≥ 10% |
| Tỉ lệ hoàn tất | Lượt hoàn tất chia sẻ / lượt bấm Xuất | ≥ 80% |
| Tỉ lệ lỗi tạo file | Lượt lỗi / lượt bấm Xuất | ≤ 1% |
| Khoảng thời gian phổ biến | Phân bố lối tắt được chọn | Theo dõi |
| Thời gian tạo file | Từ bấm Xuất đến khi bảng chia sẻ mở | ≤ 3 giây với 10.000 giao dịch |

## 9. Phụ thuộc và rủi ro

| **Mục** | **Mô tả** | **Hướng xử lý** |
| --- | --- | --- |
| Lỗi font trên Excel | Excel đọc CSV sai mã hoá nếu thiếu BOM | UTF-8 có BOM |
| Dấu ngăn cột theo vùng | Excel cài đặt tiếng Việt có thể chờ dấu chấm phẩy | Kiểm thử trên Excel và Google Sheets; ghi hướng dẫn ngắn nếu cần |
| Dữ liệu nhạy cảm | File chứa toàn bộ thu chi, gửi nhầm người | Nhắc một dòng trước khi xuất |
| Chèn công thức | Ghi chú bắt đầu bằng "=" bị bảng tính chạy như công thức | Thêm dấu nháy đơn trước ô bắt đầu bằng =, +, -, @ |
| File tạm | File tạm còn lại trên máy | Xoá file tạm sau khi bảng chia sẻ đóng |

## 10. Câu hỏi mở

> Mọi câu hỏi dưới đây đã được PO chốt ngày 13/11/2026; quyết định ghi sau mỗi câu và đã cập nhật vào SRS.

- Số tiền chi có nên ghi âm (−) thay vì dùng cột Loại? → **Đã chốt:** Giữ cột Loại, số tiền luôn dương (SRS BR-32).
- Có cần xuất thêm file danh mục và ngân sách? → **Đã chốt:** Có. Tuỳ chọn kèm file danh mục và ngân sách (SRS BR-37).
- Có làm nhập từ CSV ở phase sau để khôi phục dữ liệu không? → **Đã chốt:** Để phase sau, ngoài phạm vi MVP.
- Dấu ngăn cột dùng dấu phẩy hay dấu chấm phẩy cho người dùng Excel tiếng Việt? → **Đã chốt:** Dấu phẩy, UTF-8 có BOM (SRS BR-31).
