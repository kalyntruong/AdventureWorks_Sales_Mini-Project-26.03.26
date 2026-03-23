# 🚲 Phân tích Hiệu quả Kinh doanh & Hành vi Khách hàng - AdventureWorks

## 📌 Tổng quan dự án (Project Overview)
Mục tiêu chính dự án là phân tích hiệu quả kinh doanh toàn diện của doanh nghiệp, tối ưu hóa hiệu suất bán hàng và thấu hiểu hành vi khách hàng thông qua mô hình RFM. Từ dữ liệu phân tích được để đề xuất các chiến lược kinh doanh sắp tới cho công ty. 
* **Toàn bộ Source Code SQL:** [Vui lòng xem chi tiết tại file SQL_Queries.sql trong Repository này]

---

### PHẦN I: PHÂN TÍCH TOÀN DIỆN TÌNH HÌNH KINH DOANH CỦA DOANH NGHIỆP 
**Task 1: Phân tích xu hướng Doanh thu & Lợi nhuận theo từng tháng / năm (Time Series Analysis)**
* **Insight:** Doanh thu của AdventureWorks có sự biến động qua các năm. Ví dụ cụ thể, tháng 5 năm 2022 tổng số đơn hàng chỉ có 47 nhưng đến năm 2023 đã tăng lên 259 đơn trong tháng 5. Ngoài ra, nhìn chung có thể thấy số lượng tổng đơn hàng có sự tăng lên qua từng năm, đây là một dấu hiệu tăng trưởng tốt của doanh nghiệp.
<img width="920" height="513" alt="image" src="https://github.com/user-attachments/assets/16ee24ed-7d42-4950-91a2-e1e732c09377" />

### PHẦN II: TỐI ƯU HÓA VẬN HÀNH (Operations & Profitability)
**Task 2: Tìm ra Sản phẩm chủ lực **
* **Insight:** Dữ liệu chứng minh Nhóm sản phẩm "Bikes" là "xương sống" mang lại phần lớn doanh thu (chiếm 86.17% Tổng doanh thu từ trước đến nay) 
<img width="929" height="490" alt="image" src="https://github.com/user-attachments/assets/05bb938a-fa2a-46d6-abad-59f4e9a5f180" />  

**Task 3: Xếp hạng nhân sự theo doanh số đạt được **
* **Insight:** Về nhân sự, nhân viên Linda Mitchell là người có tỷ lệ đóng góp  cao nhất (Contribution_Pct = 12.88%) trên tổng doanh số từ trước đến nay.
<img width="920" height="511" alt="image" src="https://github.com/user-attachments/assets/8e16d391-ec50-4a62-a64f-7607bca19ba9" />

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
