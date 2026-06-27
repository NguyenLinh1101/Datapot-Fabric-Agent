# Data Investigation — VanArsdel

> **Bước 1 của workflow** · Vai trò: **Business Analyst** · Ngày: 2026-06-27
> Phạm vi: **chỉ** Data Investigation & Data Profiling. Không tạo glossary/dictionary/BRD/spec/mockup, không clean, không sửa dữ liệu.
> Nguyên tắc: **Evidence-first** — mọi con số trong tài liệu này lấy trực tiếp từ profiling dữ liệu gốc; mọi suy luận chưa chứng minh được đưa vào *Assumptions* hoặc *Questions for Stakeholder*.

---

## 0. Nguồn dữ liệu & cách tiếp cận

| Hạng mục | Chi tiết |
|---|---|
| Vị trí | `dataset/raw/Data lab & assignment/` |
| Workbook 1 | `VanArsdel_Actuals.xlsx` — 6 sheet (Date, Campaign, Customer, Product, Geo, Sales) |
| Workbook 2 | `VanArsdel_Budget_Forecast.xlsx` — 1 sheet (Budget & Forecast), dạng **wide** |
| File hỗ trợ | `Actuals_Path.txt`, `Budget_Path.txt` (Power Query M nạp dữ liệu), `Number_Days.txt` (hàm M tính day-of-year) |
| Công cụ khảo sát | Excel COM xuất từng sheet ra CSV tạm (trong scratchpad, **không** ghi đè dữ liệu gốc), sau đó profiling bằng script Node |

**Đọc từ file M (`*.txt`):**
- `Actuals_Path.txt`: Actuals được nạp từ **Excel workbook** qua một biến đường dẫn `Path` + tên file `Actuals_File` (hỗ trợ cả đường dẫn web lẫn local).
- `Budget_Path.txt`: Budget được nạp như **CSV** (`Delimiter=","`, `Encoding=1252`) — tức bản gốc của khách hàng vốn là file CSV, không phải Excel.
- `Number_Days.txt`: hàm tiện ích trả về số thứ tự ngày trong năm (1–366) từ một ngày giao dịch — gợi ý nhu cầu phân tích theo **mốc ngày-trong-năm** (so sánh cùng kỳ YTD).

---

## 1. Cấu trúc dữ liệu

### 1.1 Tổng quan các bảng

| Bảng | Workbook | Số dòng (data) | Số cột | Vai trò dự kiến | Grain (một dòng = …) |
|---|---|--:|--:|---|---|
| `Sales` | Actuals | 675,368 | 5 | **Fact** | một dòng bán = 1 đơn vị của 1 Product, cho 1 Customer, 1 Campaign, tại 1 Date |
| `Customer` | Actuals | 282,597 | 3 | Dimension (lớn) | một khách hàng |
| `Geo` | Actuals | 39,948 | 6 | Dimension (địa lý, nối qua Customer) | một mã ZIP |
| `Product` | Actuals | 212 | 8 | Dimension | một sản phẩm (ProductID) |
| `Campaign` | Actuals | 22 | 3 | Dimension | một chiến dịch marketing |
| `Date` | Actuals | 2,192 | 7 | Dimension (lịch) | một ngày |
| `Budget & Forecast` | Budget | 36 | 12 (wide) | **Fact (kế hoạch)** | một (Type × Year × Month) với 9 cột giá trị Category-Segment |

> *Lưu ý số dòng:* số trên là **dòng dữ liệu** (đã trừ header). Budget có thêm 1 dòng banner tiêu đề + 1 dòng header phụ (xem [data-quality-report.md](data-quality-report.md) — DQ-05).

### 1.2 Primary Key / Foreign Key

| Bảng | Primary Key (bằng chứng) | Foreign Key |
|---|---|---|
| `Sales` | **Không có PK** — không có cột định danh giao dịch; 133 dòng trùng hoàn toàn (DQ-04) | `ProductID`→Product, `CustomerID`→Customer, `CampaignID`→Campaign, `Date`→Date |
| `Customer` | `CustomerID` (282,597 distinct / 282,597 dòng = 100% duy nhất) | `ZipCode`→Geo.`Zip` |
| `Geo` | `Zip` (39,948 distinct / 39,948 dòng = 100% duy nhất) | — |
| `Product` | `ProductID` (212 / 212 = 100%) | — (ManufacturerID là hằng số = 7) |
| `Campaign` | `CampaignID` (22 / 22 = 100%) | — |
| `Date` | `Date` (2,192 / 2,192 = 100%) | — |
| `Budget & Forecast` | Khóa tổ hợp logic (Type, Year, Month) — chỉ có sau khi unpivot | Nối **gián tiếp** với Actuals qua (Category, Segment, Year, Month); không có khóa vật lý |

### 1.3 Quan hệ & Cardinality (đã kiểm chứng toàn vẹn khóa)

| Quan hệ | Loại | Cardinality | Toàn vẹn (bằng chứng) |
|---|---|---|---|
| `Sales` → `Product` | nhiều–một | 675,368 : 212 | ✅ 0 dòng Sales có ProductID lạ |
| `Sales` → `Customer` | nhiều–một | 675,368 : 282,597 | ✅ 0 dòng lạ (282,596/282,597 khách có phát sinh mua — 1 khách chưa mua) |
| `Sales` → `Campaign` | nhiều–một | 675,368 : 22 | ✅ 0 dòng lạ |
| `Sales` → `Date` | nhiều–một | 675,368 : 2,192 | ⚠️ **112,202 dòng Sales (toàn bộ 2015) KHÔNG có ngày tương ứng trong Date dim** (DQ-01) |
| `Customer` → `Geo` | nhiều–một | 282,597 : 39,948 | ✅ 0 ZipCode khách thiếu trong Geo |
| `Budget` ↔ `Actuals` | — | — | ⚠️ Không có khóa trực tiếp; chỉ ghép ở mức Category-Segment × tháng, và một tổ hợp lệch (DQ-06) |

**Sơ đồ luồng quan hệ (dạng snowflake):**

```
                 ┌─────────┐
   ┌────────────►│ Product │  (Category, Segment, Unit Cost, Unit Price)
   │             └─────────┘
   │             ┌─────────┐
   ├────────────►│ Campaign│  (TrafficChannel, Device)
   │             └─────────┘
┌──┴───┐         ┌─────────┐         ┌──────┐
│ Sales├────────►│  Date   │         │ Geo  │ (City, State, Region, District)
└──┬───┘         └─────────┘         └──▲───┘
   │             ┌─────────┐            │ (ZipCode → Zip)
   └────────────►│Customer ├────────────┘
                 └─────────┘

[ Budget & Forecast ] — đứng riêng, nối logic với Sales→Product (Category,Segment) theo tháng/năm
```

> Quan sát quan trọng: **địa lý gắn với khách hàng**, không phải cửa hàng (không tồn tại thực thể cửa hàng/chi nhánh). Mọi phân tích "theo vùng" thực chất là theo **vị trí của khách hàng**.

---

## 2. Data Profiling theo từng bảng

Ký hiệu: *uniq%* = distinct / số dòng non-null. Kiểu dữ liệu là **suy luận từ giá trị** (dữ liệu gốc là text/CSV).

### 2.1 `Sales` (Fact) — 675,368 dòng · 5 cột

| Cột | Kiểu | Null | Distinct | Min–Max | Ghi chú |
|---|---|--:|--:|---|---|
| `ProductID` | int (FK) | 0 | 212 | 392–691 | nối Product |
| `Date` | date (text M/D/YYYY) | 0 | 2,002 | **1/1/2015 → 6/30/2020** | nối Date dim (gãy ở 2015) |
| `CustomerID` | text (FK, có số 0 đầu) | 0 | 282,596 | 1–282,597 | nối Customer; lưu dạng "070283" |
| `CampaignID` | int (FK) | 0 | 22 | 1–22 | nối Campaign |
| `Units` | int | 0 | **1** | **1–1** | **luôn = 1** — không có cột doanh thu |

- **Không có cột tiền tệ/amount.** Doanh thu phải suy ra (xem Assumptions, DQ-03).
- 133 dòng trùng hoàn toàn (DQ-04).
- **Phân bố theo năm (bằng chứng):** 2015: 112,202 · 2016: 116,895 · 2017: 124,791 · 2018: 130,215 · 2019: 127,067 · **2020: 64,198 (chỉ tới 30/6 — năm khuyết)**.
- Ứng viên **Fact/đo lường**: `Units` (đếm số đơn vị bán = đếm dòng); doanh thu/giá vốn là *derived* qua Product.
- Ứng viên **Dimension key**: các cột FK ở trên.

### 2.2 `Customer` (Dimension) — 282,597 dòng · 3 cột

| Cột | Kiểu | Null | Distinct | Ghi chú |
|---|---|--:|--:|---|
| `CustomerID` | text (PK, số 0 đầu) | 0 | 282,597 (100%) | "000001"… |
| `ZipCode` | int/text (FK→Geo) | 0 | 29,190 | 1001–99950; nên giữ text vì zip có số 0 đầu |
| `Email Name` | text (ghép) | 0 | 250,857 (88.8%) | định dạng `"(email): Last, First "` — **mọi dòng có khoảng trắng cuối** (DQ-09) |

- Dimension rất lớn (≈282K khách). `Email Name` là trường ghép (email + họ tên) — không phải tên/email thuần.
- Ứng viên Dimension: thuộc tính khách (qua Geo để có vùng).

### 2.3 `Geo` (Dimension địa lý) — 39,948 dòng · 6 cột

| Cột | Kiểu | Null | Distinct | Ghi chú |
|---|---|--:|--:|---|
| `Zip` | int/text (PK) | 0 | 39,948 (100%) | 1–99950; **88 giá trị < 1000** nghi ngờ (DQ-11) |
| `City` | text (ghép) | 0 | 28,575 | định dạng `"City, ST, USA"` — trùng thông tin với State/Country (DQ-12) |
| `State` | text | 0 | **49** | thiếu so với 50 bang + DC (DQ-13) |
| `Region` | text | 0 | 3 | East / Central / West |
| `District` | text | 0 | 39 | "District #01" … "District #39" |
| `Country` | text | 0 | **1** | chỉ "USA" — cột hằng (DQ-12) |

- Phân cấp địa lý: `Zip → City → District → State → Region → Country`.
- Ứng viên Dimension/slicer: Region, State, District (phân tích địa lý).

### 2.4 `Product` (Dimension) — 212 dòng · 8 cột

| Cột | Kiểu | Null | Distinct | Min–Max | Ghi chú |
|---|---|--:|--:|---|---|
| `ProductID` | int (PK) | 0 | 212 (100%) | 392–691 | |
| `Product` | text | 0 | **173** | — | 39 tên lặp ở ID khác nhau (DQ-15) |
| `Category` | text | 0 | 5 | — | Accessory, Mix, Rural, Urban, Youth |
| `Segment` | text | 0 | 9 | — | Accessory, All Season, Productivity, Select, Convenience, Extreme, Moderation, Regular, Youth |
| `ManufacturerID` | int | 0 | **1** | 7–7 | hằng số |
| `Manufacturer` | text | 0 | **1** | — | chỉ "VanArsdel" |
| `Unit Cost` | decimal | 0 | 162 | 15.31–149.46 | độ chính xác cao bất thường (DQ-16) |
| `Unit Price` | decimal | 0 | 162 | 20.97–204.74 | **Unit Price > Unit Cost ở 100% dòng** (biên dương) |

- **Phân cấp dự kiến:** Category → Segment → Product. 10 tổ hợp Category-Segment thực tế: *Accessory-Accessory, Mix-All Season, Mix-Productivity, **Rural-Productivity**, Rural-Select, Urban-Convenience, Urban-Extreme, Urban-Moderation, Urban-Regular, Youth-Youth*.
- **Chỉ có sản phẩm của chính VanArsdel** (Manufacturer hằng) → dữ liệu không chứa sản phẩm đối thủ.
- Ứng viên Dimension: Category, Segment, Product. Ứng viên cho **đo lường derived**: Unit Price (→ doanh thu), Unit Cost (→ giá vốn, biên lợi nhuận).

### 2.5 `Campaign` (Dimension) — 22 dòng · 3 cột

| Cột | Kiểu | Null | Distinct | Ghi chú |
|---|---|--:|--:|---|
| `CampaignID` | int (PK) | 0 | 22 (100%) | |
| `TrafficChannel` | text | 0 | 8 | có **khoảng trắng thừa** (6/22) và sai chính tả "Affliliate" (DQ-08) |
| `Device` | text | 0 | 5 | Desktop, Mobile, Tablet, **"Deskop" (typo)**, Paper (DQ-07) |

- Kênh (TrafficChannel) gồm: Organic Search, SEO, SEM, Banner, Email, SMO, Mail, Affiliate (sau khi chuẩn hóa).
- Ứng viên Dimension/slicer: TrafficChannel, Device.

### 2.6 `Date` (Dimension lịch) — 2,192 dòng · 7 cột

| Cột | Kiểu | Distinct | Ghi chú |
|---|---|--:|---|
| `Date` | date (text M/D/YYYY) | 2,192 (100%) | **1/1/2016 → 12/31/2021** |
| `MonthNo` | int | 12 | 1–12 |
| `MonthName` | text | 12 | Jan…Dec |
| `MonthID` | int | 72 | 201601–202112 (YYYYMM) |
| `Month` | text | 72 | "Jan-16"…"Sep-21" |
| `Quarter` | text | 4 | chỉ Q1–Q4, **không kèm năm** (DQ-14) |
| `Year` | int | 6 | **2016–2021** |

- **Phân bố theo năm:** 2016: 366 · 2017: 365 · 2018: 365 · 2019: 365 · 2020: 366 · 2021: 365 → lịch liên tục 2016–2021.
- Ứng viên Dimension thời gian / slicer cho mọi time-intelligence.

### 2.7 `Budget & Forecast` (Fact kế hoạch, wide) — 36 dòng dữ liệu · 12 cột

Cấu trúc thực tế sau khi đọc raw:
- Dòng 0: banner tiêu đề `"Budget & Forecast"` (các cột còn lại trống).
- Dòng 1: header phụ thật → `Type | Year | Month | Accessory-Accessory | Mix-All Season | Mix-Productivity | Rural-Select | Urban-Convenience | Urban-Extreme | Urban-Moderation | Urban-Regular | Youth-Youth`.
- Dòng 2–37: 36 dòng dữ liệu.

| Trục | Giá trị (bằng chứng) |
|---|---|
| `Type` | Budget, Forecast |
| `Year` | 2020, 2021 |
| `Month` | Jan…Dec (12) |
| 9 cột giá trị | mỗi cột là một tổ hợp **Category-Segment**; giá trị là số tiền (decimal) |

**Phân rã 36 dòng:** Forecast 2021 (12 tháng) + Budget 2021 (12 tháng) + Budget 2020 (12 tháng).
→ **Có Budget cho 2020 & 2021; Forecast chỉ cho 2021. Không có Budget/Forecast cho 2015–2019.**

- Grain (sau unpivot): Type × Year × Month × (Category-Segment).
- Ứng viên Fact/đo lường: giá trị tiền là **kế hoạch doanh thu** theo nhóm Category-Segment.
- **Khác biệt với Product:** Budget có 9 tổ hợp, Product có 10 — thiếu **Rural-Productivity** trong Budget (DQ-06).

---

## 3. Cột nào có khả năng làm gì (tổng hợp)

| Vai trò | Cột / nguồn |
|---|---|
| **Dimension – Thời gian** | `Date.*` (Year, Quarter, Month, MonthID, MonthName) |
| **Dimension – Sản phẩm** | `Product.Category`, `Product.Segment`, `Product.Product` |
| **Dimension – Khách hàng/Địa lý** | `Geo.Region`, `Geo.State`, `Geo.District`, `Geo.City`, `Customer` |
| **Dimension – Marketing** | `Campaign.TrafficChannel`, `Campaign.Device` |
| **Fact – đo trực tiếp** | `Sales.Units` (= số đơn vị bán = đếm dòng) |
| **Fact – đo derived** | Doanh thu = Units × `Product.Unit Price`; Giá vốn = Units × `Product.Unit Cost` |
| **Fact – kế hoạch** | `Budget & Forecast` (giá trị tiền theo Category-Segment × tháng) |
| **KPI khả thi** | xem mục 4.5 |

---

## 4. Business Observation (góc nhìn Business Analyst)

### 4.1 Dataset mô tả nghiệp vụ gì
Dữ liệu mô tả hoạt động **bán lẻ sản phẩm của VanArsdel tại thị trường Mỹ (USA)**: từng đơn vị sản phẩm bán ra, gắn với khách hàng, chiến dịch marketing và thời gian; song song có **kế hoạch (Budget) và dự báo (Forecast)** doanh thu theo nhóm sản phẩm. Đây là bộ dữ liệu phục vụ theo dõi **kết quả kinh doanh so với kế hoạch**.

### 4.2 Các đối tượng nghiệp vụ chính
- **Sản phẩm** (Product → Segment → Category) — đều do chính VanArsdel sản xuất.
- **Khách hàng** (Customer) gắn với **địa lý** (Geo: Zip → City → District → State → Region).
- **Chiến dịch marketing** (Campaign) gắn **kênh** (TrafficChannel) và **thiết bị** (Device).
- **Thời gian** (Date).
- **Giao dịch bán** (Sales) — sự kiện trung tâm.
- **Kế hoạch/Dự báo** (Budget & Forecast).

### 4.3 Luồng dữ liệu
Mỗi giao dịch bán (`Sales`) tham chiếu tới Sản phẩm, Khách hàng, Chiến dịch, Ngày. Địa lý của doanh số đi qua **Khách hàng → Geo**. Kết quả thực tế (Sales) được đặt cạnh **Budget/Forecast** ở mức Category-Segment theo tháng để đo mức đạt kế hoạch.

### 4.4 Fact & Dimension
- **Fact:** `Sales` (thực tế, grain đơn vị bán); `Budget & Forecast` (kế hoạch, grain Category-Segment × tháng).
- **Dimension:** `Product`, `Customer`, `Geo`, `Campaign`, `Date`.

### 4.5 KPI có khả năng xây dựng (nghiệp vụ trước, công thức sau)
> Mọi công thức dưới đây là **đề xuất phụ thuộc xác nhận** vì Sales không có cột doanh thu sẵn (DQ-03, Assumptions A2).

| Nhu cầu nghiệp vụ | Diễn giải | Phụ thuộc dữ liệu |
|---|---|---|
| **Doanh thu** | Tổng tiền bán ra | = Σ(`Units` × `Unit Price`) — *derived* |
| **Số lượng bán** | Bao nhiêu đơn vị đã bán | = Σ`Units` (= đếm dòng Sales) — **chắc chắn có** |
| **Giá vốn / Lợi nhuận gộp / Biên LN** | Hiệu quả lợi nhuận | cần Unit Cost; LN gộp = DT − giá vốn |
| **Số khách hàng phát sinh mua** | Độ rộng tập khách | = distinct `CustomerID` trong Sales — **có** |
| **Mức đạt kế hoạch (Budget Attainment)** | Thực tế / Kế hoạch | **chỉ khả thi ở vùng overlap 2020 tháng 1–6** (DQ-02) |
| **Độ chính xác dự báo (Forecast Accuracy)** | Thực tế / Dự báo | **không khả thi** — không có Actual 2021 (DQ-02) |
| **Tăng trưởng YoY** | So cùng kỳ năm trước | đủ tin cậy cho 2016–2019; 2015 & 2020 lệch/khuyết |
| **Doanh thu theo chiều** | Category/Segment, Region/State, Campaign/Channel/Device, Product | **có** (qua các dimension) |

### 4.6 Những điểm còn thiếu để hiểu đầy đủ nghiệp vụ
→ Chi tiết & phân loại mức độ trong [data-quality-report.md](data-quality-report.md). Tóm tắt câu hỏi nghiệp vụ ở mục 6 dưới đây.

---

## 5. Assumptions (giả định — **cần stakeholder xác nhận**, chưa dùng làm sự thật)

| # | Giả định | Vì sao đặt ra | Rủi ro nếu sai |
|---|---|---|---|
| A1 | Mỗi dòng `Sales` = bán **1 đơn vị** 1 sản phẩm cho 1 khách, trong 1 chiến dịch, tại 1 ngày | `Units` luôn = 1 | Hiểu sai grain → đếm sai số lượng/giao dịch |
| A2 | Doanh thu = `Units` × `Unit Price` (gross, **chưa** thuế/chiết khấu) | Không có cột doanh thu; Product có Unit Price | Sai toàn bộ KPI tiền tệ |
| A3 | Tiền tệ là **USD** | `Geo.Country` chỉ "USA" | Hiển thị sai đơn vị tiền |
| A4 | `Geo` phản ánh **vị trí khách hàng**, không phải cửa hàng | Geo nối qua Customer; không có thực thể cửa hàng | Diễn giải sai "doanh thu theo vùng" |
| A5 | Nối `Sales`↔`Date` bằng cột `Date` (text M/D/YYYY) | Không có DateKey số | Lỗi quan hệ/định dạng ngày |
| A6 | Budget/Forecast khớp Actuals ở mức **Category-Segment × tháng** | Cấu trúc cột budget = Category-Segment | So sánh sai cấp độ |

## 6. Questions for Stakeholder (cần làm rõ trước khi thiết kế dashboard)

1. **Doanh thu** được định nghĩa thế nào? Có sẵn ở đâu không, hay dùng `Units × Unit Price`? Có **thuế/chiết khấu**, doanh thu **net vs gross**?
2. **Đơn vị tiền tệ** là gì (USD)? Quy ước định dạng/đơn vị hiển thị?
3. Vì sao `Units` luôn = 1 — mỗi dòng luôn là 1 đơn vị? Có trường hợp mua >1 đơn vị/giao dịch không?
4. **Phạm vi thời gian báo cáo** chuẩn là gì? Xử lý ra sao với 2015 (thiếu trong Date dim) và 2020 (chỉ tới 30/6)?
5. Vì sao **Budget/Forecast chỉ có 2020–2021** trong khi Actual là 2015–2020? Khách muốn so sánh Actual–Budget cho **kỳ nào**?
6. Ngữ nghĩa quan hệ **Campaign ↔ Sales**: một giao dịch gắn 1 chiến dịch nghĩa là giao dịch *đến từ* chiến dịch đó? `TrafficChannel`/`Device` đại diện cho điều gì trong hành trình mua?
7. Có thực thể **cửa hàng/chi nhánh** không, hay địa lý chỉ theo khách hàng?
8. Tổ hợp **Rural-Productivity** có trong Product nhưng **không có dòng budget** — đúng chủ đích hay thiếu dữ liệu?
9. Bộ khách hàng dùng email `@xyza.com` — đây là **dữ liệu mẫu/ẩn danh**? Có ràng buộc PII nào không?
10. Có cần phân tích theo **District/Region** (đơn vị tổ chức của VanArsdel) không, và định nghĩa District là gì?

---

*Bước tiếp theo (sau khi bạn duyệt): Business Glossary + Data Dictionary. Tài liệu này chưa thực hiện bất kỳ thay đổi nào lên dữ liệu.*
