# Business Context - Multi-Room Booking & Purchasing CMS

## Business Model

Công ty hoạt động theo mô hình:

1. Khách hàng đặt phòng/căn hộ trên OTA (Agoda, Trip.com, Booking.com, ...)
2. Công ty nhận booking từ OTA.
3. Công ty đi thuê/mua phòng từ Host hoặc Supplier.
4. Công ty bán lại cho khách và hưởng phần chênh lệch.

CMS đang được xây dựng để thay thế quy trình vận hành hiện tại trên Lark Base.

---

## Core Problem

Một booking từ OTA có thể chứa nhiều phòng.

Ví dụ:

* Studio × 2
* 1BR × 1

Hiện tại khi dữ liệu đổ vào Lark Base:

* Nhân viên phải copy record thủ công.
* Nhiều record chứa cùng thông tin booking.
* Chỉ khác phần purchasing và supplier.
* Việc quản lý trở nên khó khăn khi số lượng booking tăng.

CMS cần xử lý hoàn toàn tự động trường hợp này.

---

## Booking Structure

Một booking chỉ tồn tại duy nhất một lần trong hệ thống.

Booking bao gồm:

* Thông tin khách hàng
* OTA Order ID
* Check-in / Check-out
* Giá bán
* Chính sách hủy
* Danh sách phòng khách yêu cầu

Ví dụ:

Booking #123

* Studio × 2
* 1BR × 1

Đây vẫn là một booking duy nhất.

Không tạo nhiều booking con chỉ để phục vụ purchasing.

---

## Purchasing Model

Khách mua "room type".

Công ty mua "room thực tế".

Ví dụ:

Khách đặt:

* Studio × 2

Purchasing:

* Supplier A → Room 101
* Supplier B → Room 203

Do đó hệ thống phải quản lý:

Booking
→ Room Requests
→ Purchasing Records
→ Actual Rooms

---

## Supplier Model

Một booking có thể được mua từ nhiều supplier.

Ví dụ:

Booking #123

Studio × 2

* Supplier A cung cấp 1 phòng
* Supplier B cung cấp 1 phòng

Accounting và purchasing cần theo dõi riêng từng supplier.

---

## Purchasing Workflow

Purchasing không diễn ra ngay khi booking được tạo.

Mỗi property có cấu hình:

Purchase Lead Time

Ví dụ:

* Property A: 5 ngày trước check-in
* Property B: 7 ngày trước check-in

Khi đến thời điểm này:

* CMS tạo notification.
* Nhân viên purchasing xử lý.
* CMS không tự động mua phòng.

---

## Purchasing Completion Rules

Purchasing được xem là hoàn tất khi:

* Toàn bộ phòng trong booking đã được mua.
* Toàn bộ phòng đã được gán supplier.
* Toàn bộ phòng đã được gán room thực tế.

Không được hoàn tất purchasing nếu còn thiếu bất kỳ phòng nào.

---

## Booking Changes

Trong thực tế có thể xảy ra:

Khách yêu cầu:

* Studio × 2

Nhưng purchasing chỉ tìm được:

* Studio × 1
* 2BR × 1

Nhân viên được phép thay đổi booking.

Tất cả thay đổi phải được audit.

Không được mất lịch sử.

Audit phải ghi nhận:

* Người thay đổi
* Thời gian
* Giá trị cũ
* Giá trị mới
* Lý do

---

## Booking Modification Rules

### Trước khi Purchasing hoàn tất

Cho phép:

* Tăng số lượng phòng
* Giảm số lượng phòng
* Đổi room type
* Đổi ngày check-in
* Đổi ngày check-out

---

### Sau khi Purchasing hoàn tất

Không cho phép:

* Tăng số lượng phòng
* Đổi room type
* Đổi ngày check-in
* Đổi ngày check-out

Cho phép:

* Giảm số lượng phòng

Tuy nhiên khách vẫn có thể bị charge theo chính sách vận hành.

Có thể charge tới 100% nếu purchasing đã hoàn tất.

---

## Cancellation Policy

Cancellation policy được cấu hình ở cấp Property.

Ví dụ:

Property A:

* Free cancellation trước 5 ngày

Property B:

* Free cancellation trước 7 ngày

CMS phải sử dụng policy của property để:

* Tính phí hủy
* Xác định mức charge khách

Không hỗ trợ policy ở cấp room type.

---

## OTA Updates

OTA đôi khi gửi cập nhật booking.

Trong thực tế nghiệp vụ hiện tại:

* Thường hủy booking
* Hoặc từ chối thay đổi

Không tự động áp dụng thay đổi lên booking đã purchasing.

---

## Audit Requirements

Không cho phép hard delete:

* Booking
* Purchasing Records
* Room Assignments

Nếu người dùng xóa dữ liệu:

* Chỉ đánh dấu trạng thái removed/cancelled.
* Vẫn giữ toàn bộ lịch sử.

Mọi thay đổi đều phải có audit trail.

---

## Future Scalability

Hiện tại:

* Thường tối đa khoảng 5 phòng mỗi room type.

Tuy nhiên hệ thống phải được thiết kế:

* Không giới hạn số lượng phòng.
* Không giới hạn số supplier.
* Không phụ thuộc vào cấu trúc hiện tại của OTA.

CMS phải hỗ trợ mở rộng quy mô kinh doanh trong tương lai mà không cần thay đổi data model cốt lõi.
