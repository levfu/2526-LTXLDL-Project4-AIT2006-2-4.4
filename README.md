#  Inside Airbnb 
###  Dự án phân tích dữ liệu đặt phòng khách sạn theo thời gian / thành phố 
Mục tiêu: Phân tích xu hướng nguồn cung, giá cả, và hiệu suất hoạt động của thị trường Airbnb tại 3 thành phố Brussels , Berlin và Paris.

---

## Mục lục
1. [Giới thiệu](#giới-thiệu)
2. [Cấu trúc dự án](#cấu-trúc-dự-án)
3. [Yêu cầu cài đặt](#yêu-cầu-cài-đặt)
4. [Hướng dẫn chạy](#hướng-dẫn-chạy)
5. [Quy trình xử lý dữ liệu](#quy-trình-xử-lý-dữ-liệu)
6. [Danh sách biểu đồ](#danh-sách-biểu-đồ)
7. [Thành viên](#thành-viên)

---

## 1. Giới thiệu
* Dự án sử dụng dữ liệu từ **Inside Airbnb**, một trang web cung cấp các snapshot dữ liệu Airbnb theo thành phố và theo thời điểm thu thập (collection date). Các tệp thường ở định dạng CSV (đôi khi kèm gz), bao gồm listings (thông tin chỗ ở và giá), calendar (tính sẵn sàng và giá theo ngày), reviews (đánh giá) và neighbourhoods (ranh giới khu vực GeoJSON/CSV) tùy thành phố.

* Trang tải dữ liệu chính thức: https://insideairbnb.com/get-the-data/

* Tài liệu khám phá: https://insideairbnb.com/explore


* Dữ liệu sau khi được xử lý của các thành phố sẽ được dùng để trả lời cho các câu hỏi:
1. Nguồn cung và giá cho thuê biến động như thế nào theo thời gian?
2. Khu vực nào đắt đỏ nhất và khu vực nào tập trung nhiều phòng nhất?
3. Tỷ lệ lấp đầy và hiệu suất doanh thu ra sao?
4. Mức độ chuyên nghiệp hóa của chủ nhà.
5. Đánh giá của khách hàng.
6. Thông tin về vị trí và khu vực.

* Dữ liệu bao gồm các snapshot:
1. **Brussels, Belgium:** 22/12/2024, 16/03/2025, 21/06/2025.
2. **Berlin, Germany:** 21/12/2024, 15/03/2025, 20/06/2025, 23/09/2025.
3. **Paris, France:** 06/12/2024, 03/03/2025, 06/06/2025, 12/09/2025.

---

## 2. Cấu trúc dự án
```text
.
├── raw/                         # Dữ liệu thô (theo city/snapshot_date)
├── processed/                   # Dữ liệu sạch & bảng KPI
├── figures/                     # Hình trực quan hoá
├── reports/                     # Báo cáo kỹ thuật (PDF)
├── src/                         # Notebooks / scripts
├── README.md                    # Hướng dẫn chạy lại
└── requirements.txt             # Thư viện & phiên bản
```

---

## 3. Yêu cầu cài đặt
* Các thư viện cần thiết cho dự án được liệt kê trong requirements.txt.

* Cài đặt thư viện:
```text
pip install -r requirements.txt
```

* Nội dung file requirements.txt:
```text
pandas
numpy
matplotlib
seaborn
```

---

## 4. Hướng dẫn chạy
* Bước 1: Chuẩn bị dữ liệu
1. Tải các file listings.csv.gz, calendar.csv.gz, reviews.csv.gz và neighbourhoods.csv của 3 thành phố Brussels, Berlin và Paris từ Inside Airnb.
2. Đặt vào thư mục theo cấu trúc:
```text
/raw/<city_name>/<snapshot_date>/
(<city_name> - tên thành phố, <snapshot_date> - ngày thu thập dữ liệu)
```

* Bước 2: Xử lý dữ liệu và tính KPI
1. Chạy notebook của từng thành phố theo thứ tự Brussels -> Berlin -> Paris để xử lý dữ liệu, làm sạch và tạo các file KPI tổng hợp.
```text
# Xử lý dữ liệu, làm sạch và chuẩn hóa 
/src/<city_name>_EDA.ipynb/

# Tổng hợp và tính KPI                  
/src/<city_name>_Summary_KPI.ipynb/
         
(<city_name> - tên thành phố)
```
2. Input: Dữ liệu thô từ /raw/.
3. Output: Các file CSV sạch trong /processed/<city_name>/ và /reports/ (<city_name> - tên thành phố).
```text
Các file CSV trong /processed/<city_name>/
# Dữ liệu về các khu vực của thành phố
/processed/<city_name>/kpi_neighbourhood_<city_name>.csv/

# Dữ liệu về xu hướng đánh giá của thành phố
/processed/<city_name>/kpi_review_trends_<city_name>.csv/

# Dữ liệu về phân bố loại phòng của thành phố
/processed/<city_name>/kpi_room_type_<city_name>.csv/

# Dữ liệu về tỷ lệ lấp đầy của thành phố              
/processed/<city_name>/kpi_seasonality_<city_name>.csv/

# Dữ liệu cơ bản của thành phố                
/processed/<city_name>/kpi_summary_general_<city_name>.csv/            


Các file CSV trong /reports/
# Báo cáo về dữ liệu sau khi xử lý
/reports/qa_summary_<city_name>.csv
                                   
(<city_name> - tên thành phố)
```

* Bước 3: Trực quan hóa và so sánh
1. Chạy notebook của từng thành phố theo thứ tự Brussels -> Berlin -> Paris để vẽ biểu đồ cho từng thành phố và biểu đổ so sánh tổng hợp.
```text
# Vẽ biểu đồ cho từng thành phố
/src/<city_name>_Visualization.ipynb/

# Vẽ biểu đồ so sánh tổng hợp các thành phố               
/src/Summary_Visualization.ipynb/                   
```
2. Input: Các file KPI CSV trong /processed/.
3. Output: Các biểu đồ được lưu dưới dạng file PDF trong /figures/.


* Bước 4: Chạy lại toàn bộ dự án (Không bắt buộc)
1. Nếu bạn đã chạy các notebook trên thì không bắt buộc phải chạy lại dự án.
2. Chạy notebook chính để chạy lại pipeline dự án.
```text

# Notebook chính để chạy lại toàn bộ dự án
/src/MAIN.ipynb/
```


---

## 5. Quy trình xử lý dữ liệu
* Đọc dữ liệu /raw/, xử lý các cột tiền tệ,...
* Gắn cờ các dữ liệu ngoại lai, lọc bỏ nhiễu,...
* Tính toán các dữ liệu cơ bản cho từng thành phố.
* Phân tích sâu dữ liệu tính toán được để vẽ biểu đồ.

---

## 6. Danh sách biểu đồ
* Kết quả đầu ra trong /figures/ bao gồm:
1. Biểu đồ của thành phố Brussels:
```text
brussels_01_supply.pdf
brussels_02_price.pdf
brussels_03_multi_host.pdf
brussels_04_availability.pdf
brussels_05_occupancy.pdf
brussels_06_room_type.pdf
brussels_07_map_labeled.pdf
brussels_08_top10_supply.pdf
brussels_09_top10_price.pdf
brussels_10_price_distribution.pdf
brussels_11_price_heatmap_optimized.pdf
brussels_12_reviews_trend.pdf
```

2. Biểu đồ của thành phố Berlin:
```text
berlin_01_supply.pdf
berlin_02_price.pdf
berlin_03_multi_host.pdf
berlin_04_availability.pdf
berlin_05_occupancy.pdf
berlin_06_room_type.pdf
berlin_07_top10_supply.pdf
berlin_08_top10_price.pdf
berlin_09_price_distribution.pdf
berlin_10_property_type.pdf
berlin_11_price_segments.pdf
berlin_12_reviews_trend.pdf
```

3. Biểu đồ của thành phố Paris:
```text
paris_01_supply.pdf
paris_02_price.pdf
paris_03_multi_host.pdf
paris_04_availability.pdf
paris_05_occupancy.pdf
paris_06_room_type.pdf
paris_07_map_labeled.pdf
paris_08_top10_supply.pdf
paris_09_top10_price.pdf
paris_10_price_distribution.pdf
paris_11_price_heatmap.pdf
paris_12_reviews_trend.pdf
```

4. Biểu đồ so sánh tổng hợp 3 thành phố Brussels, Berlin, Paris:
```text
H01_Total_Supply_All_Snapshots.pdf
H02_Median_Price_Horizontal.pdf
H03_Availability_Trend.pdf
H04_MultiHost_Stacked.pdf
H05_Occupancy_Rate_LineChart.pdf
H06_Room_Type_GroupedBar.pdf
H07_Price_RoomType.pdf
H08_Price_Segments.pdf
H09_Quality_Scores.pdf
H10_Price_Quality_Boxplot.pdf
H11_Top15_Neighborhoods.pdf
H12_Reviews_Trend.pdf
H13_Radar_Summary.pdf
```

---

## 7. Thành viên
* Lê Việt Phú - 24022426
* Hoàng Huy Hoàng - 24022336
* Trần Trung Hiếu - 24022330












