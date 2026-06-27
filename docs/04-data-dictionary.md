# Data Dictionary — VanArsdel

> Mục đích: bàn giao cho **Data Analyst** làm cơ sở xây dựng dashboard. Mô tả ở **góc nhìn nghiệp vụ** 
> Nguyên tắc **Evidence-first**: mọi mô tả & ví dụ lấy từ profiling dữ liệu thực tế (xem [data-investigation.md](data-investigation.md), [data-quality-report.md](data-quality-report.md)). Điều chưa rõ → ghi ở **Data Gap**.

## Quy ước
- **Tên bảng & tên cột** giữ nguyên tiếng Anh đúng như nguồn.
- **Data Type** dùng từ nghiệp vụ phổ thông: `Text`, `Whole Number`, `Decimal`, `Date`. (Đây là kiểu *quan sát từ dữ liệu*, không phải khai báo kỹ thuật của model.)
- **Key:** PK = khóa chính, FK = khóa ngoại.
- Tham chiếu vấn đề chất lượng dữ liệu theo mã **DQ-xx** trong [data-quality-report.md](data-quality-report.md).

## Bản đồ bảng & quan hệ (tóm tắt)

| Bảng | Loại | Số dòng | Nối tới |
|---|---|--:|---|
| `Sales` | Fact (thực tế) | 675,368 | Product, Customer, Campaign, Date |
| `Budget` | Fact (kế hoạch) | 24 (logic) | Product (Category+Segment), Date (Year+Month) — gián tiếp |
| `Forecast` | Fact (dự báo) | 12 (logic) | Product (Category+Segment), Date (Year+Month) — gián tiếp |
| `Product` | Dimension | 212 | ← Sales |
| `Customer` | Dimension | 282,597 | ← Sales; → Geo |
| `Geo` | Dimension | 39,948 | ← Customer |
| `Campaign` | Dimension | 22 | ← Sales |
| `Date` | Dimension | 2,192 | ← Sales |

> **Lưu ý nguồn Budget/Forecast:** cả hai cùng đến từ **một sheet wide duy nhất** (`VanArsdel_Budget_Forecast`), phân biệt bằng cột `Type`. Cấu trúc cột mô tả dưới đây là dạng **logic sau khi tháo bảng wide** (unpivot) — xem Business Rules của từng bảng và DQ-05.

---

## 1. Table: `Date`  *(Dimension)*

1. **Business Purpose:** Trục thời gian chuẩn của doanh nghiệp, cho phép theo dõi xu hướng và so sánh theo kỳ (tháng/quý/năm).
2. **Grain:** một dòng = **một ngày dương lịch**.
3. **Primary Key:** `Date`.
4. **Foreign Key:** không có.
5. **Relationship:** `Date` 1 — n `Sales` (qua cột `Date`). Phủ thời gian **2016-01-01 → 2021-12-31**.

| Column Name | Business Meaning | Data Type | Key | Example Value |
|---|---|---|---|---|
| `Date` | Ngày lịch cụ thể | Date | PK | `1/1/2016` |
| `MonthNo` | Số thứ tự tháng trong năm (1–12) | Whole Number | | `1` |
| `MonthName` | Tên tháng viết tắt | Text | | `Jan` |
| `MonthID` | Mã tháng dạng năm+tháng, dùng để sắp xếp/so sánh tháng xuyên năm | Whole Number | | `201601` |
| `Month` | Nhãn tháng kèm năm để hiển thị | Text | | `Jan-16` |
| `Quarter` | Quý trong năm | Text | | `Q1` |
| `Year` | Năm | Whole Number | | `2016` |

7. **Business Rules:**
   - Lịch liên tục, đủ ngày từng năm 2016–2021 (2016 & 2020 là năm nhuận, 366 ngày).
   - `Quarter` không kèm năm → khi phân tích theo quý xuyên nhiều năm cần ghép với `Year`.
8. **Assumptions:** Dùng **năm dương lịch** (chưa có dấu hiệu năm tài chính) — *cần xác nhận*.
9. **Data Gap:**
   - Phạm vi `Date` (2016–2021) **không khớp** phạm vi `Sales` (2015–2020) → cần stakeholder chốt kỳ báo cáo và năm dương lịch vs năm tài chính (DQ-01).

---

## 2. Table: `Campaign`  *(Dimension)*

1. **Business Purpose:** Mô tả các chiến dịch marketing để gắn doanh số với nỗ lực tiếp thị (kênh, thiết bị).
2. **Grain:** một dòng = **một chiến dịch marketing**.
3. **Primary Key:** `CampaignID`.
4. **Foreign Key:** không có.
5. **Relationship:** `Campaign` 1 — n `Sales` (qua `CampaignID`).

| Column Name | Business Meaning | Data Type | Key | Example Value |
|---|---|---|---|---|
| `CampaignID` | Mã định danh chiến dịch | Whole Number | PK | `1` |
| `TrafficChannel` | Kênh tiếp cận khách hàng của chiến dịch | Text | | `Organic Search`, `Email`, `Affiliate` |
| `Device` | Thiết bị/hình thức khách tương tác | Text | | `Desktop`, `Mobile`, `Paper` |

7. **Business Rules:** Mỗi chiến dịch gắn với một kênh và một thiết bị. Có 22 chiến dịch, 8 kênh, 5 nhóm thiết bị.
8. **Assumptions:** Không có.
9. **Data Gap:**
   - Chưa rõ quan hệ Campaign–Sales là **attribution** (doanh số *đến từ* chiến dịch) hay chỉ là chiến dịch đang chạy (Glossary G-8) → ảnh hưởng cách diễn giải KPI marketing.
   - Danh mục `TrafficChannel`/`Device` cần stakeholder xác nhận **danh mục chuẩn** nếu dùng làm slicer; sai khác chính tả/khoảng trắng đã ghi ở [data-quality-report.md](data-quality-report.md).

---

## 3. Table: `Customer`  *(Dimension)*

1. **Business Purpose:** Danh mục khách hàng mua sản phẩm VanArsdel; gắn doanh số với khách và (qua mã ZIP) với địa lý.
2. **Grain:** một dòng = **một khách hàng**.
3. **Primary Key:** `CustomerID`.
4. **Foreign Key:** `ZipCode` → `Geo`.`Zip`.
5. **Relationship:** `Customer` 1 — n `Sales` (qua `CustomerID`); `Customer` n — 1 `Geo` (qua `ZipCode`).

| Column Name | Business Meaning | Data Type | Key | Example Value |
|---|---|---|---|---|
| `CustomerID` | Mã định danh khách hàng | Text | PK | `000001`, `070283` |
| `ZipCode` | Mã ZIP nơi khách hàng cư trú (liên kết địa lý) | Text | FK | `90250` |
| `Email Name` | Trường ghép gồm email và họ tên khách | Text | | `(Aaron.Alexander@xyza.com): Alexander, Aaron` |

7. **Business Rules:** Mỗi khách hàng gắn với đúng một mã ZIP, và mã ZIP luôn tra cứu được trong `Geo` → quan hệ `Customer` → `Geo` an toàn để dùng.
8. **Assumptions:** `CustomerID` và `ZipCode` được hiểu là **mã định danh dạng Text** (có số 0 đầu), không phải số để tính toán (DQ-10).
9. **Data Gap:**
   - `Email Name` là **trường ghép** (email + họ tên) và có khoảng trắng cuối ở mọi dòng (DQ-09) → nếu cần hiển thị tên/email riêng phải tách (việc xử lý thuộc khâu sau).
   - Chưa rõ dữ liệu khách là **thật hay mẫu/ẩn danh** và ràng buộc PII (Glossary G-11).

---

## 4. Table: `Product`  *(Dimension)*

1. **Business Purpose:** Danh mục sản phẩm VanArsdel bán ra, kèm phân loại (Category/Segment), giá bán và giá vốn — nền tảng phân tích theo mặt hàng và lợi nhuận.
2. **Grain:** một dòng = **một sản phẩm** (theo `ProductID`).
3. **Primary Key:** `ProductID`.
4. **Foreign Key:** không có.
5. **Relationship:** `Product` 1 — n `Sales` (qua `ProductID`). Liên kết **logic** với `Budget`/`Forecast` qua cặp (`Category`, `Segment`).

| Column Name | Business Meaning | Data Type | Key | Example Value |
|---|---|---|---|---|
| `ProductID` | Mã định danh sản phẩm | Whole Number | PK | `392` |
| `Product` | Tên sản phẩm | Text | | `Maximus UM-01` |
| `Category` | Nhóm sản phẩm cấp cao | Text | | `Urban`, `Rural`, `Youth`, `Mix`, `Accessory` |
| `Segment` | Phân khúc sản phẩm (cấp giữa, chi tiết hơn Category) | Text | | `Moderation`, `Convenience`, `Select` |
| `ManufacturerID` | Mã nhà sản xuất | Whole Number | | `7` |
| `Manufacturer` | Tên nhà sản xuất | Text | | `VanArsdel` |
| `Unit Cost` | Giá vốn cho một đơn vị sản phẩm | Decimal | | `37.2710625` |
| `Unit Price` | Giá bán cho một đơn vị sản phẩm | Decimal | | `51.05625` |

7. **Business Rules:**
   - Phân cấp sản phẩm: **Category → Segment → Product**.
   - Toàn bộ sản phẩm do **VanArsdel** sản xuất (Manufacturer chỉ một giá trị) → dữ liệu **không có sản phẩm đối thủ** (DQ-18).
   - `Unit Price` luôn lớn hơn `Unit Cost` ở mọi sản phẩm (giá bán cao hơn giá vốn).
   - Dùng `ProductID` làm định danh sản phẩm — tên `Product` có thể trùng giữa các mã.
8. **Assumptions:** `Unit Price`/`Unit Cost` được hiểu là giá **niêm yết/định mức** trên một đơn vị; đơn vị tiền tệ chưa ghi rõ (Glossary G-2).
9. **Data Gap:**
   - Thiếu tổ hợp **Rural-Productivity** (có ở Product, không có dòng Budget/Forecast) → chặn việc đối chiếu đầy đủ Actual vs Plan (DQ-06).
   - Chưa rõ định nghĩa kinh doanh & tính cố định của phân cấp Category/Segment (Glossary G-5).

---

## 5. Table: `Geo`  *(Dimension)*

1. **Business Purpose:** Phân cấp địa lý (theo mã ZIP) gắn với khách hàng, cho phép phân tích doanh số theo địa bàn (vùng/bang/khu vực).
2. **Grain:** một dòng = **một mã ZIP**.
3. **Primary Key:** `Zip`.
4. **Foreign Key:** không có.
5. **Relationship:** `Geo` 1 — n `Customer` (qua `Zip` ↔ `Customer.ZipCode`). Doanh số tới địa lý đi theo đường **Sales → Customer → Geo**.

| Column Name | Business Meaning | Data Type | Key | Example Value |
|---|---|---|---|---|
| `Zip` | Mã ZIP (đơn vị địa lý nhỏ nhất) | Text | PK | `22654` |
| `City` | Trường ghép thành phố + bang + quốc gia | Text | | `Stephens City, VA, USA` |
| `State` | Bang (viết tắt 2 ký tự) | Text | | `VA` |
| `Region` | Vùng địa lý cấp cao | Text | | `East`, `Central`, `West` |
| `District` | Khu vực được đánh số (cách chia địa bàn) | Text | | `District #07` |
| `Country` | Quốc gia | Text | | `USA` |

7. **Business Rules:**
   - Phân cấp địa lý: **Zip → City → District → State → Region → Country**.
   - `Country` chỉ một giá trị "USA" → toàn bộ thị trường tại Mỹ.
8. **Assumptions:** `Zip` được hiểu là **mã định danh dạng Text** (có số 0 đầu), không phải số (DQ-10).
9. **Data Gap:**
   - Chưa rõ `District` là địa giới hành chính, vùng bán hàng hay đơn vị tổ chức nội bộ (Glossary G-7); và **không có thực thể cửa hàng/chi nhánh** trong dữ liệu (Glossary G-6) → ảnh hưởng cách thiết kế phân tích địa lý.
   - Các vấn đề chất lượng địa lý mức nhỏ (mã ZIP nghi ngờ, độ phủ bang, trường `City` ghép) đã ghi ở [data-quality-report.md](data-quality-report.md) — không lặp tại đây.

---

## 6. Table: `Sales`  *(Fact — kết quả thực tế / Actual)*

1. **Business Purpose:** Ghi nhận hoạt động bán hàng thực tế — sự kiện trung tâm tạo doanh thu; là "Actual" để đối chiếu với kế hoạch.
2. **Grain:** một dòng = **một lần bán một đơn vị** của một `Product`, cho một `Customer`, gắn một `Campaign`, tại một `Date`.
3. **Primary Key:** **Không có** khóa chính (không có mã giao dịch). → xem Business Rules & DQ-04.
4. **Foreign Key:** `ProductID` → `Product`; `Date` → `Date`; `CustomerID` → `Customer`; `CampaignID` → `Campaign`.
5. **Relationship:** `Sales` n — 1 với cả 4 dimension. Khóa nối tới Product/Customer/Campaign đều hợp lệ; riêng quan hệ tới `Date` **chỉ phủ 2016–2020 — dữ liệu bán năm 2015 nằm ngoài `Date` dimension** (xem Data Gap).

| Column Name | Business Meaning | Data Type | Key | Example Value |
|---|---|---|---|---|
| `ProductID` | Sản phẩm được bán | Whole Number | FK | `676` |
| `Date` | Ngày bán | Date | FK | `9/25/2015` |
| `CustomerID` | Khách hàng mua | Text | FK | `070283` |
| `CampaignID` | Chiến dịch gắn với lần bán | Whole Number | FK | `22` |
| `Units` | Số đơn vị của lần bán (hiện luôn = 1) | Whole Number | | `1` |

7. **Business Rules:**
   - Bảng **không có cột doanh thu**; doanh thu là khái niệm phải xác định lại từ hoạt động bán (Glossary Revenue, DQ-03).
   - `Units` luôn bằng 1 trên mọi dòng (DQ-03).
   - Vì không có khóa giao dịch, có thể tồn tại các dòng bán **giống hệt nhau**; **không tự khử trùng** khi chưa xác nhận tính hợp lệ với stakeholder (chi tiết số liệu ở DQ-04).
   - Phạm vi thời gian thực tế: **2015-01-01 → 2020-06-30** (năm 2020 chỉ tới tháng 6).
8. **Assumptions:**
   - Mỗi dòng tương ứng **một đơn vị bán** (vì `Units`=1) — *cần xác nhận có lần bán >1 đơn vị không* (Glossary G-3).
   - Nối `Sales`↔`Date` bằng cột `Date` dạng ngày.
9. **Data Gap:**
   - Không có mã giao dịch/đơn hàng → grain "giao dịch" chưa định danh được (DQ-04).
   - "Revenue" chưa có sẵn; định nghĩa chính thức cần stakeholder chốt (Glossary G-1) **trước khi** DA dựng số liệu doanh thu.
   - Lệch khung thời gian với Date dim và với Budget/Forecast (DQ-01, DQ-02).

---

## 7. Table: `Budget`  *(Fact — kế hoạch)*

> **Nguồn:** các dòng có `Type = Budget` trong sheet wide `VanArsdel_Budget_Forecast`. Cấu trúc cột dưới đây là dạng **logic sau khi tháo bảng wide** (Type/Year/Month + 9 cột Category-Segment → dạng dài). Việc tháo bảng thuộc khâu xử lý sau; ở đây mô tả để DA hiểu nội dung nghiệp vụ.

1. **Business Purpose:** Mục tiêu/ngân sách doanh thu kế hoạch theo tháng và nhóm sản phẩm, làm chuẩn để đánh giá mức đạt kế hoạch của thực tế.
2. **Grain:** một dòng = **một (Year × Month × Category × Segment)** với một giá trị kế hoạch.
3. **Primary Key:** khóa tổ hợp logic (`Year`, `Month`, `Category`, `Segment`) — **không có khóa vật lý** ở dạng nguồn.
4. **Foreign Key:** không có khóa vật lý. Liên kết **logic**: (`Category`,`Segment`) ↔ `Product`; (`Year`,`Month`) ↔ `Date`.
5. **Relationship:** Đối chiếu với `Sales` (Actual) ở mức Category-Segment theo tháng. Không nối trực tiếp ở cấp dòng.

| Column Name | Business Meaning | Data Type | Key | Example Value |
|---|---|---|---|---|
| `Type` | Loại số liệu kế hoạch — ở bảng này là "Budget" | Text | | `Budget` |
| `Year` | Năm kế hoạch | Whole Number | | `2020`, `2021` |
| `Month` | Tháng kế hoạch | Text | | `Jan` … `Dec` |
| `Category` | Nhóm sản phẩm (tách từ tiêu đề cột "Category-Segment") | Text | | `Urban` |
| `Segment` | Phân khúc sản phẩm (tách từ tiêu đề cột "Category-Segment") | Text | | `Moderation` |
| *(giá trị kế hoạch)* | Số tiền ngân sách doanh thu cho tổ hợp tương ứng | Decimal | | `241208.9513` |

7. **Business Rules:**
   - Budget được lập ở mức **Category-Segment × tháng**, không chi tiết tới từng sản phẩm/khách/vùng.
   - Phạm vi: **Year 2020 & 2021** (mỗi năm 12 tháng) → 24 tổ hợp tháng × 9 nhóm.
   - Nguồn là **bảng wide** kèm dòng banner tiêu đề; cần unpivot 9 cột Category-Segment và tách tên cột thành `Category` + `Segment` (DQ-05). *(Ghi nhận yêu cầu, không thực hiện ở đây.)*
8. **Assumptions:** Giá trị kế hoạch là **doanh thu** kế hoạch (cùng đơn vị với Revenue thực tế) — *cần xác nhận* (Glossary G-1, G-2).
9. **Data Gap:**
   - Thiếu tổ hợp **Rural-Productivity** (có ở Product, không có dòng Budget) (DQ-06).
   - Budget chỉ có 2020–2021, trong khi Actual là 2015–2020 → vùng so sánh rất hẹp (DQ-02). Cần stakeholder chốt kỳ so sánh.
   - Tên cột giá trị ở nguồn là tổ hợp "Category-Segment" gộp; cần xác nhận thứ tự/ý nghĩa tách (giả định phần trước là Category, phần sau là Segment, theo khớp với Product).

---

## 8. Table: `Forecast`  *(Fact — dự báo)*

> **Nguồn:** các dòng có `Type = Forecast` trong cùng sheet wide `VanArsdel_Budget_Forecast`. Cấu trúc logic giống `Budget`.

1. **Business Purpose:** Dự báo doanh thu kỳ vọng theo tháng và nhóm sản phẩm; phản ánh kỳ vọng cập nhật (khác với mục tiêu cố định Budget).
2. **Grain:** một dòng = **một (Year × Month × Category × Segment)** với một giá trị dự báo.
3. **Primary Key:** khóa tổ hợp logic (`Year`, `Month`, `Category`, `Segment`) — không có khóa vật lý.
4. **Foreign Key:** không có khóa vật lý. Liên kết logic: (`Category`,`Segment`) ↔ `Product`; (`Year`,`Month`) ↔ `Date`.
5. **Relationship:** Đối chiếu với `Sales` (Actual) ở mức Category-Segment theo tháng.

| Column Name | Business Meaning | Data Type | Key | Example Value |
|---|---|---|---|---|
| `Type` | Loại số liệu — ở bảng này là "Forecast" | Text | | `Forecast` |
| `Year` | Năm dự báo | Whole Number | | `2021` |
| `Month` | Tháng dự báo | Text | | `Jan` … `Dec` |
| `Category` | Nhóm sản phẩm | Text | | `Urban` |
| `Segment` | Phân khúc sản phẩm | Text | | `Moderation` |
| *(giá trị dự báo)* | Số tiền doanh thu dự báo cho tổ hợp tương ứng | Decimal | | `251155.7122` |

7. **Business Rules:**
   - Forecast lập ở mức **Category-Segment × tháng**.
   - Phạm vi: **chỉ Year 2021** (12 tháng × 9 nhóm).
   - Cùng nguồn wide với Budget, phân biệt bằng `Type`; cần unpivot tương tự (DQ-05).
8. **Assumptions:** Giá trị dự báo là **doanh thu** dự báo (cùng đơn vị Revenue) — *cần xác nhận*.
9. **Data Gap:**
   - Forecast chỉ có 2021 — **không có Actual 2021** để đối chiếu độ chính xác dự báo (DQ-02).
   - Cần phân biệt rõ ý nghĩa nghiệp vụ **Budget vs Forecast** (mục tiêu cứng vs dự báo mềm) (Glossary G-4).
   - Thiếu tổ hợp Rural-Productivity tương tự Budget (DQ-06).

---

## Tổng hợp Assumptions toàn tài liệu (cần stakeholder xác nhận)
| # | Giả định | Áp dụng cho |
|---|---|---|
| 1 | ID/Zip là mã định danh dạng **Text** (giữ số 0 đầu) | Customer, Geo, Sales |
| 2 | Giá trị Budget/Forecast là **doanh thu** kế hoạch/dự báo, cùng đơn vị với Revenue thực tế | Budget, Forecast |
| 3 | Mỗi dòng `Sales` = **một đơn vị** bán | Sales |
| 4 | Lịch dùng **năm dương lịch** | Date |
| 5 | Tiêu đề cột budget "Category-Segment": phần trước = Category, phần sau = Segment (khớp Product) | Budget, Forecast |

## Tổng hợp Data Gap ưu tiên (chặn việc build nếu chưa rõ)
1. **Định nghĩa Revenue** (G-1) + **Currency** (G-2) — nền tảng mọi số liệu tiền tệ.
2. **Kỳ so sánh Actual vs Budget/Forecast** (DQ-01, DQ-02) — quyết định phạm vi thời gian dashboard.
3. **Grain & dòng trùng của Sales** (DQ-04).
4. **Xử lý nhóm Rural-Productivity thiếu kế hoạch** (DQ-06).
5. **Bản chất District & có cửa hàng hay không** (G-6, G-7).

---
*Tài liệu Data Dictionary ở góc nhìn Business Analyst. Không xây semantic model, không DAX, không measure, không hidden column, không TMDL. Mọi khuyến nghị xử lý dữ liệu (unpivot, chuẩn hóa…) chỉ là ghi nhận yêu cầu cho khâu sau, chưa thực hiện.*
