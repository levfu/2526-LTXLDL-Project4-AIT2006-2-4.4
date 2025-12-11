<h1 align="center">BÁO CÁO KỸ THUẬT - DỰ ÁN PHÂN TÍCH DỮ LIỆU AIRBNB</h1>


## A. THÔNG TIN CƠ BẢN

* **Tên dự án:** Inside Airbnb - Phân tích dữ liệu đặt phòng khách sạn theo thời gian / thành phố.

* **Nhóm thực hiện:** Nhóm 2526-LTXLDL-Project4-AIT2006-2-4.4.

* **Thành viên:** 
1. Lê Việt Phú - 24022426.
2. Hoàng Huy Hoàng - 24022336.
3. Trần Trung Hiếu - 24022330.

* **Các thành phố được phân tích:**
1. **Brussels**, Belgium.
2. **Berlin**, Germany.
3. **Paris**, France.

* **Danh sách các snapshot sử dụng:**
1. **Brussels**, Belgium: 22/12/2024, 16/03/2025, 21/06/2025.
2. **Berlin**, Germany: 21/12/2024, 15/03/2025, 20/06/2025, 23/09/2025.
3. **Paris**, France: 06/12/2024, 03/03/2025, 06/06/2025, 12/09/2025.

* **Nguồn dữ liệu:**
1. Trang tải dữ liệu chính thức: https://insideairbnb.com/get-the-data/
2. Trang tải tài liệu khám phá: https://insideairbnb.com/explore

* **Ngày nộp:** 

* **Commit hash:**




## B. GIỚI THIỆU & DỮ LIỆU

### B.1 Giới thiệu

* Dự án sử dụng dữ liệu từ **Inside Airbnb** - một nền tảng cung cấp các snapshot dữ liệu Airbnb theo thành phố và theo thời điểm thu thập (collection date).

* **Dữ liệu sau khi được xử lý của các thành phố sẽ được dùng để phản ánh:**
1. Nguồn cung và giá cho thuê biến động như thế nào theo thời gian?
2. Khu vực nào đắt đỏ nhất và khu vực nào tập trung nhiều phòng nhất?
3. Tỷ lệ lấp đầy và hiệu suất doanh thu ra sao?
4. Mức độ chuyên nghiệp hóa của chủ nhà.
5. Đánh giá của khách hàng.
6. Thông tin về vị trí và khu vực.

* **Mục tiêu:** So sánh sự biến động của thị trường Airbnb theo **thời gian** và **không gian** giữa 3 thành phố: **Brussels**, **Berlin** và **Paris**.

---

### B.2 Dữ liệu

* **Phạm vi dữ liệu:** Dự án sử dụng dữ liệu được thu thập (snapshots) của mỗi thành phố. Mỗi snapshot bao gồm các tệp: `listings.csv.gz`, `calendar.csv.gz`, `reviews.csv.gz`, `neighbourhoods.csv`.

* **Cấu trúc các tệp dữ liệu đầu vào:**
1. `listings.csv.gz`: Thông tin chỗ ở và giá thuê.
2. `calendar.csv.gz`: Tính sẵn sàng và giá theo ngày.
3. `reviews.csv.gz`: Đánh giá của khách hàng.
4. `neighbourhoods.csv`: Ranh giới khu vực GeoJSON/CSV.

* **Các trường chính được sử dụng từ các tệp dữ liệu đầu vào:**
1. `listings.csv.gz`: `id`, `host_id`, `host_name`, `neighbourhood_cleansed`, `latitude`, `longitude`, `room_type`, `price`, `price_numeric`, `availability_90`, `host_since`. 
2. `calendar.csv.gz`: `date`, `available`, `price`, `price_numeric`.
3. `reviews.csv.gz`: `date`, `id`, `reviewer_id`, `comments`.
4. `neighbourhoods.csv`: `neighbourhood_group`

---

### C. QUY TRÌNH QA & LÀM SẠCH DỮ LIỆU

* **Chuẩn hóa giá:**
1. Tách giá trị số từ chuỗi (loại bỏ `$`, `,`, ký tự không phải số).  
2. Chuyển về dạng số thực `price_numeric`.  
3. Áp dụng cho cả `listings.csv.gz` và `calendar.csv.gz`.

* **Kiểm tra giá bất thường - QA001:**
1. Điều kiện: `price_numeric ≤ 0`.  
2. Xử lý: không xoá, chỉ **gắn cờ** (`qa_flag_price_zero = True`).  
3. Mục đích: Theo dõi các bản ghi sai lệch có thể ảnh hưởng thống kê.

* **Chuẩn hóa ngày và tọa độ:**
1. Chuẩn hoá định dạng ngày: `host_since`, `calendar.date`, `reviews.date`.  
2. Chuyển `latitude`, `longitude` sang số (`float`), xử lý lỗi bằng NaN.

* **Kiểm tra tọa độ ngoài ranh giới thành phố - QA002:**
1. Áp dụng bounding box cho từng thành phố.  
2. Các bản ghi nằm ngoài vùng quy định được gắn cờ (`qa_flag_out_of_city = True`).  
3. Không xoá để tránh mất dữ liệu ngoại lệ.

* **Kiểm tra ID trùng lặp - QA003:**
1. Phát hiện bản ghi có `id` trùng nhau.  
2. Giữ bản đầu tiên, xoá các bản ghi còn lại.  
3. Số lượng trùng được ghi lại trong báo cáo QA.

* **Loại bỏ các cột không cần thiết:**
1. Xoá `adjusted_price` (calendar).  
2. Xoá `neighbourhood_group` (neighbourhoods).

* **Lưu dữ liệu sạch:**
1. Lưu lại 4 file đã làm sạch trong thư mục: `processed/<city_name>/<snapshot_date>/`
2. Chú thích: `<city_name>` - tên thành phố, `<snapshot_date>` - ngày thu thập dữ liệu.

* **Xuất báo cáo QA tổng hợp:**
1. Xuất file: `reports/qa_summary_<city_name>.csv`.
2. Ghi lại số bản ghi bị ảnh hưởng bởi từng quy tắc QA.
3. Chú thích: `<city_name>` - tên thành phố.

* **Tóm tắt kết quả QA:**

| City | snapshot_date | rule_id | records_affected | handling_decision |
| :--- | :--- | :--- | ---: | ---: |
| **Brussels** | 16 March, 2025 | QA001_price_zero | 982 | Gắn cờ |
| **Brussels** | 16 March, 2025 | QA002_coords_out_of_bounds | 36 | Gắn cờ |
| **Brussels** | 16 March, 2025 | QA003_duplicate_ids | 0 | Xoá dòng trùng |
| **Brussels** | 21 June, 2025 | QA001_price_zero | 1,020 | Gắn cờ |
| **Brussels** | 21 June, 2025 | QA002_coords_out_of_bounds | 39 | Gắn cờ |
| **Brussels** | 21 June, 2025 | QA003_duplicate_ids | 0 | Xoá dòng trùng |
| **Brussels** | 22 December, 2024 | QA001_price_zero | 848 | Gắn cờ |
| **Brussels** | 22 December, 2024 | QA002_coords_out_of_bounds | 38 | Gắn cờ |
| **Brussels** | 22 December, 2024 | QA003_duplicate_ids | 0 | Xoá dòng trùng |
| **Berlin** | 15 March, 2025 | QA001_price_zero | 5,047 | Gắn cờ |
| **Berlin** | 15 March, 2025 | QA002_coords_out_of_bounds | 0 | Gắn cờ |
| **Berlin** | 15 March, 2025 | QA003_duplicate_ids | 0 | Xoá dòng trùng |
| **Berlin** | 20 June, 2025 | QA001_price_zero | 5,004 | Gắn cờ |
| **Berlin** | 20 June, 2025 | QA002_coords_out_of_bounds | 0 | Gắn cờ |
| **Berlin** | 20 June, 2025 | QA003_duplicate_ids | 0 | Xoá dòng trùng |
| **Berlin** | 21 December, 2024 | QA001_price_zero | 4,994 | Gắn cờ |
| **Berlin** | 21 December, 2024 | QA002_coords_out_of_bounds | 0 | Gắn cờ |
| **Berlin** | 21 December, 2024 | QA003_duplicate_ids | 0 | Xoá dòng trùng |
| **Berlin** | 23 September, 2025 | QA001_price_zero | 5,010 | Gắn cờ |
| **Berlin** | 23 September, 2025 | QA002_coords_out_of_bounds | 0 | Gắn cờ |
| **Berlin** | 23 September, 2025 | QA003_duplicate_ids | 0 | Xoá dòng trùng |
| **Paris** | 03 March, 2025 | QA001_price_zero | 30,409 | Gắn cờ |
| **Paris** | 03 March, 2025 | QA002_coords_out_of_bounds | 78 | Gắn cờ |
| **Paris** | 03 March, 2025 | QA003_duplicate_ids | 0 | Xoá dòng trùng |
| **Paris** | 06 December, 2024 | QA001_price_zero | 30,938 | Gắn cờ |
| **Paris** | 06 December, 2024 | QA002_coords_out_of_bounds | 63 | Gắn cờ |
| **Paris** | 06 December, 2024 | QA003_duplicate_ids | 0 | Xoá dòng trùng |
| **Paris** | 06 June, 2025 | QA001_price_zero | 30,092 | Gắn cờ |
| **Paris** | 06 June, 2025 | QA002_coords_out_of_bounds | 74 | Gắn cờ |
| **Paris** | 06 June, 2025 | QA003_duplicate_ids | 0 | Xoá dòng trùng |
| **Paris** | 12 September, 2025 | QA001_price_zero | 81,853 | Gắn cờ |
| **Paris** | 12 September, 2025 | QA002_coords_out_of_bounds | 71 | Gắn cờ |
| **Paris** | 12 September, 2025 | QA003_duplicate_ids | 0 | Xoá dòng trùng |

---

### D. QUY TRÌNH TÍNH TOÁN KPI

* **Tổng số listing hợp lệ (Total Listings):**
1. Định nghĩa: Số lượng listings không bị gắn cờ QA (không lỗi giá và không lệch tọa độ).
2. Công thức: 
$$
\text{TotalListings} = | \text{Listing}_{\text{valid}} |
$$
3. Ý nghĩa: Đo lường quy mô thị trường thực tế sau QA.

* **Tỷ lệ chủ nhà có nhiều hơn một căn (Multi-Host Rate):**
1. Định nghĩa: Tỷ lệ listings thuộc về các host có nhiều hơn 2 bất động sản cho thuê.
2. Công thức:
$$\text{MultiHostRate} = \frac{\sum \text{Host}_{\text{có } \ge 2 \text{ listing}}}{\text{TotalListings}} \times 100$$
3. Ý nghĩa: Dựa vào tỷ lệ để biết được thị trường đang thiên hướng chuyên nghiệp hay cá nhân.

* **Giá thuê trung vị toàn thành phố (Median Price):**
1. Định nghĩa: Giá cho thuê trung vị từ cột `price_numeric`.
2. Công thức: 
$$
\text{MedianPrice} = \text{median}(\text{price\_numeric})
$$
3. Ý nghĩa: Đại diện cho mặt bằng giá thực, không bị nhiễu bởi outliers.

* **Số ngày trống trong 3 tháng tới (Median Availability 90):**
1. Định nghĩa: Giá trị trung vị của cột `availability_90`.
2. Công thức: 
$$ 
\text{MedianAvailability90} = \text{median}(\text{availability\_90})
$$
3. Ý nghĩa: Dựa vào giá trị để biết được nhu cầu của khách hàng.

* **Tỷ lệ lấp đầy theo ngày (Daily Occupancy Rate):**
1. Định nghĩa: Dựa trên `calendar.csv.gz` để tính tỷ lệ lấp đầy theo ngày.
2. Công thức:
$$
\text{OccRate}(d) = \frac{\text{số listing booked}}{\text{tổng listing hợp lệ}}
$$
3. Ý nghĩa: Tạo chuỗi thời gian để phân tích theo mùa.

* **Tỷ lệ loại phòng (Room Type Distribution):**
1. Định nghĩa: Tỷ lệ từng loại phòng (Entire home/apt, Private room, Shared room, Hotel room).
2. Công thức: 
$$
\text{Pct}(\text{room\_type}) = \frac{\text{count}(\text{room\_type})}{\text{TotalListings}} \times 100
$$
3. Ý nghĩa: Giúp mô tả cấu trúc lưu trú của mỗi thành phố.

* **Lưu dữ liệu KPI:**
1. Xuất file `processed/<city_name>/kpi_summary_general_<city_name>.csv` (Total Listings, Multi-Host Rate, Median Price, Median Availability 90).
2. Xuất file `processed/<city_name>/kpi_seasonality_<city_name>.csv` (Daily Occupancy Rate).
3. Xuất file `processed/<city_name>/kpi_room_type_<city_name>.csv` (Room Type Distribution).
4. Chú thích `<city_name>` - tên thành phố.

* **Tóm tắt kết quả KPI:**







