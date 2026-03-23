# 🚲 Phân tích Hiệu quả Kinh doanh & Hành vi Khách hàng - AdventureWorks

## 📌 Tổng quan dự án (Project Overview)
Dự án này ứng dụng SQL (T-SQL) để khai thác và phân tích cơ sở dữ liệu bán lẻ AdventureWorks. Mục tiêu cốt lõi là đánh giá "sức khỏe" tài chính toàn diện của doanh nghiệp, tối ưu hóa hiệu suất bán hàng và thấu hiểu hành vi khách hàng thông qua mô hình RFM. Từ đó, chuyển hóa dữ liệu thô thành các đề xuất chiến lược (Data-driven decisions) cho ban giám đốc.

* **Công cụ:** SQL Server (SSMS), GitHub.
* **Kỹ năng áp dụng:** CTEs, Window Functions (`NTILE`, `LAG`, `OVER`), Self-Join, Aggregations, `CASE WHEN` Logic.
* **Toàn bộ Source Code SQL:** [Vui lòng xem chi tiết tại file SQL_Queries.sql trong Repository này]

---

## 📊 Cấu trúc Phân tích & Insights nổi bật (Key Insights)

### PHẦN I: BỨC TRANH VĨ MÔ (Macro Performance)
**Task 1: Phân tích xu hướng Doanh thu & Lợi nhuận (Time Series Analysis)**
* **Insight:** Doanh thu của AdventureWorks có sự biến động qua các năm. Cụ thể, [Điền tóm tắt xu hướng ví dụ: năm 2013 đạt đỉnh với doanh thu X, nhưng có dấu hiệu giảm nhẹ vào đầu 2014...]. Có tính mùa vụ rõ rệt vào các tháng [Điền tháng].

### PHẦN II: TỐI ƯU HÓA VẬN HÀNH (Operations & Profitability)
**Task 2 & 3: Phân tích Sản phẩm & Nhân sự chủ lực (Pareto Principle)**
* **Insight:** Dữ liệu chứng minh nguyên lý 80/20. Nhóm sản phẩm [Điền tên Category hoặc SubCategory] là "xương sống" mang lại phần lớn doanh thu. Về nhân sự, nhân viên [Điền tên nhân viên Top 1] là người có tỷ lệ đóng góp (Contribution Margin) cao nhất toàn hệ thống.

**Task 4: Phân loại Lợi nhuận theo Vùng (Profitability Margin Status)**
* Bằng cách đồng bộ Lịch sử Giá vốn (ProductCostHistory), hệ thống đã bóc tách được biên lợi nhuận thực tế (Gross Margin).
* **Insight:** Khu vực [Điền tên Vùng điểm cao, VD: Australia] đang ở mức **High Performance** (>[Điền %]%). Ngược lại, khu vực [Điền tên vùng thấp] rơi vào nhóm **Low Performance**, yêu cầu ban quản trị rà soát lại chi phí vận hành và logistics tại đây.

### PHẦN III: THẤU HIỂU HÀNH VI KHÁCH HÀNG (Customer Behavior Analysis)
**Task 5: Phân tích Chu kỳ tái mua sắm (Customer Time Gap)**
* **Insight:** Trung bình một khách hàng sẽ quay lại mua đơn tiếp theo sau **[Điền số ngày trung bình] ngày**. 
* **Actionable:** Đây là "điểm rơi" lý tưởng để thiết lập hệ thống gửi Email Remarketing tự động nhằm kích cầu mua sắm.

**Task 6: Khám phá "Cặp bài trùng" (Market Basket Analysis)**
* **Insight:** Khách hàng có xu hướng mua chung [Điền Tên SP A] và [Điền Tên SP B] cao nhất với **[Điền số lần] lần** xuất hiện cùng nhau trên 1 hóa đơn. 
* **Actionable:** Đề xuất tạo Combo giảm giá 5-10% cho cặp sản phẩm này để tăng giá trị trung bình trên mỗi đơn hàng (AOV).

**Task 7: Chân dung Khách hàng VIP qua mô hình RFM (Segmentation)**
* Áp dụng Window Function `NTILE(5)` để chấm điểm Recency, Frequency, Monetary một cách khách quan.
* **Insight:** Hệ thống đã tự động nhận diện thành công nhóm **Champions** mang lại giá trị cao nhất và đặc biệt là nhóm **At Risk** (từng chi đậm nhưng đã lâu không quay lại).
* **Actionable:** Chuyển ngay danh sách "At Risk" cho phòng Marketing để thực hiện chiến dịch Win-back (khuyến mãi lớn) trước khi họ rời bỏ hoàn toàn sang đối thủ.

---

## 🚀 Lời kết
Dự án không chỉ dừng lại ở việc trích xuất số liệu bằng SQL, mà còn xây dựng một luồng tư duy mạch lạc: từ việc rà soát tài chính tổng thể, tìm ra "nút thắt" vận hành, cho đến việc cá nhân hóa chiến lược chăm sóc khách hàng.
