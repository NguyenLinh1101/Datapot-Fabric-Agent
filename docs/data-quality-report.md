# Data Quality Report — VanArsdel

> **Bước 1 của workflow** · Vai trò: **Business Analyst** · Ngày: 2026-06-27
> Đây là **bản ghi nhận** vấn đề chất lượng dữ liệu phát hiện trong quá trình profiling. **Không** xử lý, **không** clean, **không** sửa dữ liệu — chỉ liệt kê và khuyến nghị.
> Mọi vấn đề đều kèm **bằng chứng** từ dữ liệu gốc. Khuyến nghị xử lý là **đề xuất cho Data Analyst**, không phải hành động đã thực hiện.

## Quy ước mức độ nghiêm trọng

| Mức | Ý nghĩa |
|---|---|
| 🔴 **Cao** | Ảnh hưởng trực tiếp tính đúng đắn của KPI/quan hệ; phải làm rõ **trước khi** build |
| 🟡 **Trung bình** | Gây sai lệch khi nhóm/so sánh; cần chuẩn hóa trong khâu modeling |
| 🟢 **Thấp** | Dư thừa/khó chịu, ít rủi ro nghiệp vụ; xử lý khi tiện |

## Bảng tổng hợp

| ID | Mức | Bảng/Cột | Vấn đề |
|---|---|---|---|
| DQ-01 | 🔴 Cao | Sales.Date ↔ Date | Lệch khung thời gian Sales vs Date dimension |
| DQ-02 | 🔴 Cao | Budget vs Sales | Budget/Forecast và Actual gần như không trùng kỳ |
| DQ-03 | 🔴 Cao | Sales | Không có cột doanh thu; `Units` luôn = 1 |
| DQ-04 | 🔴 Cao | Sales | Không có khóa chính; 133 dòng trùng hoàn toàn |
| DQ-05 | 🟡 TB | Budget & Forecast | Wide table + banner + header phụ; chưa phân tích trực tiếp được |
| DQ-06 | 🟡 TB | Budget ↔ Product | Thiếu tổ hợp Category-Segment "Rural-Productivity" trong Budget |
| DQ-07 | 🟡 TB | Campaign.Device | Sai chính tả "Deskop" tách giá trị dimension |
| DQ-08 | 🟡 TB | Campaign.TrafficChannel | Khoảng trắng thừa + sai chính tả "Affliliate" |
| DQ-09 | 🟡 TB | Customer.Email Name | Trường ghép + khoảng trắng cuối ở 100% dòng |
| DQ-10 | 🟡 TB | nhiều ID/Zip | Số 0 đầu — rủi ro mất khi ép kiểu số |
| DQ-11 | 🟡 TB | Geo.Zip | 88 mã ZIP < 1000 nghi ngờ không hợp lệ |
| DQ-12 | 🟢 Thấp | Geo.Country, Geo.City | Cột hằng "USA" + trường City ghép trùng thông tin |
| DQ-13 | 🟢 Thấp | Geo.State | Chỉ 49 bang (thiếu so với 50 + DC) |
| DQ-14 | 🟢 Thấp | Date.Quarter | "Q1–Q4" không kèm năm → không duy nhất xuyên năm |
| DQ-15 | 🟢 Thấp | Product.Product | 39 tên sản phẩm lặp ở ProductID khác nhau |
| DQ-16 | 🟢 Thấp | Product.Unit Cost/Price | Độ chính xác thập phân cao bất thường |
| DQ-17 | 🟢 Thấp | toàn bộ | Không có cột chỉ định tiền tệ |
| DQ-18 | 🟢 Thấp | Product.Manufacturer(ID) | Cột hằng (chỉ VanArsdel) |

---

## Chi tiết

### DQ-01 🔴 Lệch khung thời gian Sales vs Date dimension
- **Mô tả:** `Sales.Date` trải **1/1/2015 → 30/6/2020**. `Date` dimension trải **1/1/2016 → 31/12/2021**. → **112,202 dòng Sales (toàn bộ năm 2015) không có ngày tương ứng trong Date dim**; ngược lại Date dim chứa nửa cuối 2020 và cả 2021 không có giao dịch.
- **Bằng chứng:** Sales year-dist {2015: 112202, …, 2020: 64198}; Date dim year-dist {2016…2021}; kiểm tra FK: 112,202 dòng Sales.Date không khớp Date dim.
- **Nguyên nhân (giả định):** Date dimension được tạo cho kỳ kế hoạch (2016–2021) lệch với kỳ dữ liệu thực tế.
- **Khuyến nghị:** Xác nhận phạm vi báo cáo với stakeholder; hoặc mở rộng Date dim về 2015, hoặc thống nhất chỉ phân tích 2016–2020. **Không tự cắt/bỏ dữ liệu khi chưa xác nhận.**

### DQ-02 🔴 Budget/Forecast và Actual gần như không trùng kỳ
- **Mô tả:** Budget cho **2020 & 2021**, Forecast cho **2021**; trong khi Actual chỉ **2015–30/6/2020**. → Vùng so sánh Actual vs Budget duy nhất là **2020 tháng 1–6** (và 2020 còn là năm khuyết). Không có Actual cho 2021 để đánh giá Forecast; không có Budget cho 2015–2019.
- **Bằng chứng:** Budget raw 36 dòng = Forecast 2021 (12) + Budget 2021 (12) + Budget 2020 (12); Sales max date 30/6/2020.
- **Nguyên nhân (giả định):** Hai nguồn được chuẩn bị cho các mục đích/kỳ khác nhau.
- **Khuyến nghị:** Hỏi stakeholder kỳ so sánh mong muốn; xin bổ sung Budget 2015–2019 và/hoặc Actual 2020 H2–2021 nếu cần KPI Budget Attainment đầy đủ.

### DQ-03 🔴 Không có cột doanh thu; `Units` luôn = 1
- **Mô tả:** Bảng Sales không có cột tiền/amount. `Units` có **distinct = 1, min = max = 1**. Doanh thu, giá vốn, lợi nhuận đều phải **suy ra** (join Product: Unit Price/Unit Cost).
- **Bằng chứng:** profiling `Units` distinct=1; Sales chỉ 5 cột (ProductID, Date, CustomerID, CampaignID, Units).
- **Nguyên nhân (giả định):** Mỗi dòng là một line-item một đơn vị; doanh thu được tính ở tầng model.
- **Khuyến nghị:** Chốt định nghĩa "Revenue" với stakeholder (gross/net, thuế/chiết khấu) trước khi DA viết measure. Ghi công thức rõ trong BRD/Spec ở bước sau.

### DQ-04 🔴 Sales không có khóa chính; 133 dòng trùng hoàn toàn
- **Mô tả:** Không có cột định danh giao dịch. Có **133 dòng trùng 100%** trên cả 5 cột. Không phân biệt được giao dịch trùng hợp lệ (2 khách… cùng mua) với lỗi nhân đôi.
- **Bằng chứng:** full-row duplicate count = 133 / 675,368.
- **Nguyên nhân (giả định):** Thiếu transaction ID ở nguồn; hoặc trùng hợp lệ do grain ở mức đơn vị.
- **Khuyến nghị:** Xác nhận grain & tính hợp lệ của dòng trùng. **Không khử trùng khi chưa xác nhận** (có thể là doanh số thật).

### DQ-05 🟡 Budget & Forecast là wide table chưa chuẩn
- **Mô tả:** Dòng 0 là banner "Budget & Forecast" (các ô khác trống); dòng 1 mới là header thật (`Type | Year | Month | 9 cột Category-Segment`); 9 cột giá trị nằm ngang. Cần **unpivot** thành dạng dài (Type, Year, Month, Category, Segment, Value) để dùng.
- **Bằng chứng:** dump raw 38 dòng workbook; header chuẩn nằm ở dòng thứ 2.
- **Nguyên nhân:** File khách hàng vốn là CSV trình bày cho người đọc, không tối ưu cho phân tích.
- **Khuyến nghị (cho DA):** Trong Power Query bỏ dòng banner, promote header, unpivot 9 cột, tách "Category-Segment" thành 2 cột. (Ghi nhận để DA xử lý ở bước modeling.)

### DQ-06 🟡 Thiếu tổ hợp Category-Segment trong Budget
- **Mô tả:** Product có **10** tổ hợp Category-Segment, gồm **Rural-Productivity**; Budget chỉ có **9** cột và **không có Rural-Productivity** (chỉ có Rural-Select). → Doanh số nhóm Rural-Productivity không có ngân sách đối ứng.
- **Bằng chứng:** Product combos (10) vs Budget header (9 cột); chênh đúng "Rural-Productivity".
- **Nguyên nhân (giả định):** Nhóm này không được lập kế hoạch, hoặc gộp vào nhóm khác.
- **Khuyến nghị:** Xác nhận với stakeholder cách xử lý nhóm thiếu budget khi tính Budget Attainment.

### DQ-07 🟡 Sai chính tả "Deskop" trong Campaign.Device
- **Mô tả:** `Device` có 5 giá trị: Desktop, Mobile, Tablet, **Deskop** (sai chính tả của Desktop), Paper. "Deskop" sẽ tách thành một nhóm thiết bị riêng khi tổng hợp.
- **Bằng chứng:** distinct Device = {Desktop, Mobile, Tablet, Deskop, Paper}.
- **Nguyên nhân:** Lỗi nhập liệu.
- **Khuyến nghị:** Chuẩn hóa "Deskop" → "Desktop" ở khâu modeling (ghi nhận, chưa sửa).

### DQ-08 🟡 Khoảng trắng thừa + sai chính tả trong Campaign.TrafficChannel
- **Mô tả:** 6/22 dòng có khoảng trắng cuối (vd "Organic Search  ", "Affliliate  "); giá trị "Affliliate" sai chính tả (đúng: "Affiliate"). → Cùng một kênh có thể bị tách khi nhóm.
- **Bằng chứng:** blankWs=6/22; giá trị distinct chứa "Affliliate".
- **Nguyên nhân:** Lỗi nhập liệu/định dạng nguồn.
- **Khuyến nghị:** Trim + sửa chính tả ở khâu modeling.

### DQ-09 🟡 Customer.Email Name là trường ghép + khoảng trắng cuối
- **Mô tả:** Định dạng `"(email): Last, First "`; **100% dòng** có khoảng trắng cuối. Không phải tên hay email thuần.
- **Bằng chứng:** blankWs = 282,597/282,597; mẫu `"(Aaron.Alexander@xyza.com): Alexander, Aaron "`.
- **Nguyên nhân:** Trường được ghép từ nguồn.
- **Khuyến nghị:** Nếu cần hiển thị tên/email, tách trường + trim ở khâu modeling.

### DQ-10 🟡 ID/Zip có số 0 đầu — rủi ro khi ép kiểu số
- **Mô tả:** `Customer.CustomerID` ("000001"), `Sales.CustomerID` ("070283"), `Geo.Zip`/`Customer.ZipCode` ("00001") có số 0 đầu. Nếu ép kiểu số sẽ mất số 0 và có thể gãy khóa nối.
- **Bằng chứng:** mẫu giá trị có số 0 đầu ở các cột trên.
- **Nguyên nhân:** ID/Zip mang tính mã định danh, không phải số đo.
- **Khuyến nghị:** Giữ **kiểu text** cho mọi cột ID/Zip xuyên suốt model để khóa nối nhất quán.

### DQ-11 🟡 Geo.Zip có 88 giá trị < 1000
- **Mô tả:** 88 mã ZIP có giá trị < 1000 (vd 00001–00009), min = 1. ZIP Mỹ hợp lệ thường ≥ 00501; các mã rất nhỏ nghi ngờ là placeholder/không hợp lệ.
- **Bằng chứng:** count(zip<1000)=88; ví dụ 00001…00011.
- **Nguyên nhân (giả định):** Dữ liệu địa lý có bản ghi rác/đệm.
- **Khuyến nghị:** Kiểm tra xem có customer/sales nào gắn các zip này không; xác nhận với stakeholder trước khi loại.

### DQ-12 🟢 Geo dư thừa: Country hằng + City ghép
- **Mô tả:** `Geo.Country` chỉ "USA" (cột hằng). `Geo.City` định dạng `"City, ST, USA"` trùng thông tin với cột `State` và `Country`.
- **Bằng chứng:** Country distinct=1; mẫu City "Star Tannery, VA, USA".
- **Khuyến nghị:** Có thể tách phần tên thành phố thuần khi cần; cột Country không dùng làm slicer.

### DQ-13 🟢 Geo.State chỉ 49 giá trị
- **Mô tả:** Chỉ 49 bang distinct (Mỹ có 50 bang + DC = 51). Có "DC" trong dữ liệu nhưng thiếu một số bang.
- **Bằng chứng:** State distinct=49.
- **Nguyên nhân (giả định):** Không có khách/zip ở vài bang.
- **Khuyến nghị:** Xác nhận đây là thực tế (không có doanh số ở vài bang) hay thiếu dữ liệu.

### DQ-14 🟢 Date.Quarter không kèm năm
- **Mô tả:** `Quarter` chỉ "Q1–Q4", không gắn năm → không duy nhất khi cắt xuyên nhiều năm.
- **Bằng chứng:** Quarter distinct=4.
- **Khuyến nghị:** Khi phân tích theo quý, ghép với `Year` (hoặc tạo cột Year-Quarter).

### DQ-15 🟢 Tên sản phẩm lặp ở ProductID khác nhau
- **Mô tả:** 212 ProductID nhưng chỉ 173 tên `Product` distinct → 39 tên lặp. Tên sản phẩm không định danh duy nhất.
- **Bằng chứng:** Product distinct=173 / 212 dòng.
- **Khuyến nghị:** Dùng `ProductID` làm khóa; hiển thị tên kèm phân biệt nếu cần.

### DQ-16 🟢 Unit Cost/Price độ chính xác cao bất thường
- **Mô tả:** Giá trị như 37.2710625, 119.7617925 — nhiều chữ số thập phân, có vẻ là giá trị tính toán hơn là giá niêm yết.
- **Bằng chứng:** mẫu Unit Cost/Price.
- **Khuyến nghị:** Xác nhận đơn vị/quy ước làm tròn; định dạng hiển thị ở khâu báo cáo.

### DQ-17 🟢 Không có cột chỉ định tiền tệ
- **Mô tả:** Không trường nào nêu đơn vị tiền tệ. `Geo.Country` = "USA" gợi ý USD nhưng không khẳng định.
- **Khuyến nghị:** Xác nhận tiền tệ (Assumptions A3); ghi rõ trong glossary/BRD ở bước sau.

### DQ-18 🟢 Product.Manufacturer(ID) là cột hằng
- **Mô tả:** `ManufacturerID`=7 và `Manufacturer`="VanArsdel" ở 100% dòng. Cột hằng (ít giá trị phân tích) nhưng đồng thời **xác nhận dữ liệu chỉ gồm sản phẩm của chính VanArsdel** (không có đối thủ) — một thông tin nghiệp vụ quan trọng.
- **Bằng chứng:** Manufacturer distinct=1.
- **Khuyến nghị:** Không dùng làm slicer; ghi nhận như một sự thật nghiệp vụ trong glossary.

---

*Không có thay đổi nào được thực hiện lên dữ liệu gốc. Tất cả khuyến nghị xử lý dành cho các bước modeling sau và cần stakeholder/Data Analyst quyết định.*
