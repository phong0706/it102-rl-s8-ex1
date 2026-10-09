# Báo cáo Dò vết & Sửa lỗi - SaaS Subscription Management

## A. Phân tích nguyên nhân lỗi kỹ thuật
Trong mã nguồn ban đầu do lập trình viên tiền nhiệm bàn giao, có 2 lỗi logic nghiêm trọng dẫn đến việc báo cáo doanh thu bị sai lệch:
1. **Cố định chỉ số mảng (`user_list[0]`):** Trong vòng lặp `for` duyệt qua danh sách tài khoản, câu lệnh điều kiện `if` và lệnh tính tổng `total_revenue` lại sử dụng cứng chỉ số `0` (`user_list[0].days_overdue` và `user_list[0].monthly_fee`). Điều này làm cho chương trình **chỉ kiểm tra duy nhất tài khoản đầu tiên** (`user_id = 1001`) cho toàn bộ 4 vòng lặp.
2. **Sai trạng thái hiển thị (`Hop le` / `Qua han`):** Do kiểm tra nhầm điều kiện của phần tử số 0, các tài khoản phía sau (như `1002`, `1003`, `1004`) bị gán nhãn trạng thái và cộng dồn doanh thu sai lệch hoàn toàn so với thực tế nghiệp vụ (ví dụ tài khoản quá hạn 5 ngày nhưng vẫn bị tính là hợp lệ dựa theo kết quả của tài khoản 0).

## B. Bảng Test Cases Đối Chứng

| Trường hợp kiểm thử | Dữ liệu đầu vào (`user_id`, `monthly_fee`, `days_overdue`) | Kết quả sai thực tế | Kết quả đúng mong đợi |
| :--- | :--- | :--- | :--- |
| **TC1: Tài khoản quá hạn** | `1002`, `90000`, `days_overdue = 5` | Hiển thị: "Hop le"<br>Cộng dồn doanh thu: `+90.000 VND` | Hiển thị: "Qua han"<br>Không cộng dồn doanh thu |
| **TC2: Tài khoản hợp lệ (trễ ít)** | `1003`, `260000`, `days_overdue = 1` | Hiển thị: "Hop le"<br>Cộng dồn doanh thu: `+180.000 VND` (bị lặp từ phần tử 0) | Hiển thị: "Hop le"<br>Cộng dồn doanh thu: `+260.000 VND` |