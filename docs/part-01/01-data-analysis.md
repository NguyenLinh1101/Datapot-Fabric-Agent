# Data Analysis (Data Investigation & Data Quality Assessment)

> Mục tiêu là hiểu dataset, đánh giá chất lượng dữ liệu và xác định những giới hạn của dữ liệu trước khi diễn giải nghiệp vụ.

---

## Mục tiêu

- Hiểu cấu trúc và nội dung của dataset.
- Đánh giá chất lượng dữ liệu (đầy đủ, nhất quán, hợp lệ).
- Xác định dữ liệu có thể và không thể hỗ trợ yêu cầu kinh doanh.
- Chuẩn bị dataset ở mức kỹ thuật để phục vụ các bước phân tích tiếp theo.

---

## Phạm vi

Bao gồm hai hoạt động:

1. **Data Investigation**
   - Khám phá cấu trúc dataset.
   - Xác định bảng, cột và mối quan hệ dữ liệu.

2. **Data Quality Assessment**
   - Kiểm tra dữ liệu thiếu, trùng lặp, sai kiểu dữ liệu và các bất thường.
   - Ghi nhận các vấn đề ảnh hưởng đến việc phân tích.

> Dataset chỉ được chuẩn hóa về mặt kỹ thuật trong giai đoạn này. Những vấn đề nghiệp vụ chưa đủ căn cứ sẽ được ghi nhận tại **Data Gap**.

---

## Đầu ra

- Hiểu rõ dataset.
- Đánh giá chất lượng dữ liệu.
- Dataset đã được chuẩn hóa kỹ thuật (lưu trong thư mục `dataset/clean/`).
- Đầu vào cho Business Glossary và Data Dictionary.

---

## Nguồn dữ liệu

| Hạng mục | Chi tiết |
|---|---|
| Vị trí gốc | `dataset/raw/Data lab & assignment/` (giữ nguyên, không chỉnh sửa) |
| Workbook 1 | `VanArsdel_Actuals.xlsx` — 6 sheet: Date, Campaign, Customer, Product, Geo, Sales |
| Workbook 2 | `VanArsdel_Budget_Forecast.xlsx` — 1 sheet dạng **wide** (Budget & Forecast) |
| File hỗ trợ | `Actuals_Path.txt`, `Budget_Path.txt` (Power Query M nạp dữ liệu), `Number_Days.txt` (hàm tính day-of-year) |

Từ các file M: Actuals nạp từ Excel workbook; Budget nạp như **CSV** (delimiter `,`, encoding 1252); `Number_Days` trả về số thứ tự ngày trong năm — gợi ý nhu cầu so sánh theo ngày-trong-năm (YTD).

---

# A. Data Investigation

## A.1 Cấu trúc & quan hệ

| Bảng | Vai trò | Số dòng | Grain (một dòng = …) |
|---|---|--:|---|
| `Sales` | **Fact** (thực tế) | 675,368 | một lần bán 1 đơn vị của 1 Product, cho 1 Customer, 1 Campaign, tại 1 Date |
| `Budget`/`Forecast` | **Fact** (kế hoạch) | 36 dòng wide | một (Type × Year × Month) với 9 giá trị Category-Segment |
| `Customer` | Dimension | 282,597 | một khách hàng |
| `Geo` | Dimension | 39,948 | một mã ZIP |
| `Product` | Dimension | 212 | một sản phẩm |
| `Campaign` | Dimension | 22 | một chiến dịch marketing |
| `Date` | Dimension | 2,192 | một ngày |

**Primary Key / Foreign Key:**

| Bảng | Primary Key | Foreign Key |
|---|---|---|
| `Sales` | **Không có** (không có mã giao dịch) | `ProductID`→Product, `CustomerID`→Customer, `CampaignID`→Campaign, `Date`→Date |
| `Customer` | `CustomerID` | `ZipCode`→Geo.`Zip` |
| `Geo` | `Zip` | — |
| `Product` | `ProductID` | — |
| `Campaign` | `CampaignID` | — |
| `Date` | `Date` | — |
| `Budget`/`Forecast` | khóa tổ hợp logic (Type, Year, Month, Category, Segment) | nối logic (Category,Segment)↔Product; (Year,Month)↔Date |

**Quan hệ & cardinality (đã kiểm chứng toàn vẹn khóa):**

| Quan hệ | Cardinality | Toàn vẹn |
|---|---|---|
| `Sales` → `Product` | n : 1 | ✅ 0 dòng lạ |
| `Sales` → `Customer` | n : 1 | ✅ 0 dòng lạ |
| `Sales` → `Campaign` | n : 1 | ✅ 0 dòng lạ |
| `Sales` → `Date` | n : 1 | ⚠️ 112,202 dòng Sales (toàn bộ 2015) không có ngày trong Date dim |
| `Customer` → `Geo` | n : 1 | ✅ 0 ZipCode khách thiếu trong Geo |
| `Budget/Forecast` ↔ `Sales` | — | ⚠️ không có khóa trực tiếp; chỉ ghép Category-Segment × tháng |

```
   ┌─────────┐   ┌─────────┐   ┌─────────┐
   │ Product │   │Campaign │   │  Date   │
   └────▲────┘   └────▲────┘   └────▲────┘
        │             │             │
        └──────┐ ┌────┴────┐ ┌──────┘
               │ │  Sales  │ │              [ Budget / Forecast ]
               └─┤ (fact)  ├─┘              nối logic qua Category-Segment × tháng
                 └────┬────┘
                      │ (CustomerID)
                 ┌────▼────┐   (ZipCode→Zip)   ┌──────┐
                 │Customer ├───────────────────► Geo  │
                 └─────────┘                   └──────┘
```

> Địa lý gắn với **vị trí khách hàng**, không có thực thể cửa hàng/chi nhánh.

## A.2 Data Profiling theo bảng

Ký hiệu kiểu dữ liệu suy luận từ giá trị: `Text`, `Whole Number`, `Decimal`, `Date`.

### Sales — 675,368 dòng
| Cột | Kiểu | Null | Distinct | Khoảng giá trị | Ghi chú |
|---|---|--:|--:|---|---|
| `ProductID` | Whole Number (FK) | 0 | 212 | 392–691 | |
| `Date` | Date (FK) | 0 | 2,002 | **1/1/2015 → 30/6/2020** | |
| `CustomerID` | Text (FK) | 0 | 282,596 | có số 0 đầu | |
| `CampaignID` | Whole Number (FK) | 0 | 22 | 1–22 | |
| `Units` | Whole Number | 0 | **1** | **1–1** | luôn = 1; không có cột doanh thu |
Phân bố theo năm: 2015: 112,202 · 2016: 116,895 · 2017: 124,791 · 2018: 130,215 · 2019: 127,067 · **2020: 64,198 (chỉ tới 30/6)**. Có **133 dòng trùng hoàn toàn**.

### Date — 2,192 dòng
`Date` (PK, 1/1/2016→31/12/2021), `MonthNo` (1–12), `MonthName`, `MonthID` (YYYYMM), `Month` ("Jan-16"), `Quarter` (Q1–Q4, **không kèm năm**), `Year` (2016–2021). Lịch liên tục, đủ ngày mỗi năm.

### Customer — 282,597 dòng
`CustomerID` (PK, text "000001"), `ZipCode` (FK→Geo, text), `Email Name` (trường ghép `"(email): Last, First "`, có khoảng trắng cuối ở mọi dòng).

### Geo — 39,948 dòng
`Zip` (PK, text, **88 mã <1000 nghi ngờ**), `City` (ghép "City, ST, USA"), `State` (**49** giá trị), `Region` (3: East/Central/West), `District` (39: "District #01"…), `Country` (**1**: USA).

### Product — 212 dòng
`ProductID` (PK), `Product` (**173** distinct → tên lặp), `Category` (5), `Segment` (9), `ManufacturerID` (**1**=7), `Manufacturer` (**1**="VanArsdel"), `Unit Cost` (Decimal 15.31–149.46), `Unit Price` (Decimal 20.97–204.74; > Unit Cost ở mọi sản phẩm). 10 tổ hợp Category-Segment.

### Campaign — 22 dòng
`CampaignID` (PK), `TrafficChannel` (8 kênh, có trailing space + typo "Affliliate"), `Device` (5: Desktop, Mobile, Tablet, **"Deskop"** typo, Paper).

### Budget & Forecast — 36 dòng wide
Banner ở dòng 1; header thật: `Type | Year | Month | + 9 cột Category-Segment`. Type∈{Budget, Forecast}; Year∈{2020, 2021}; Month: Jan–Dec. Phân rã: Budget 2020 (12) + Budget 2021 (12) + Forecast 2021 (12). **Budget: 2020–2021; Forecast: chỉ 2021.** Thiếu tổ hợp **Rural-Productivity** (Product có, Budget/Forecast không).

---

# B. Data Quality Assessment

Mức độ: 🔴 Cao (ảnh hưởng tính đúng đắn KPI/quan hệ) · 🟡 TB (sai lệch khi nhóm/so sánh) · 🟢 Thấp (dư thừa, ít rủi ro).
Trạng thái: **[Fixed]** đã chuẩn hóa kỹ thuật trong `dataset/clean/` · **[Business]** giữ nguyên, chờ stakeholder quyết định · **[Doc]** chỉ ghi nhận.

| ID | Mức | Vấn đề (bằng chứng) | Trạng thái |
|---|---|---|---|
| DQ-01 | 🔴 | Lệch khung thời gian: Sales 2015→30/6/2020 vs Date dim 2016→2021; 112,202 dòng Sales 2015 ngoài Date dim | **[Business]** — chốt kỳ báo cáo |
| DQ-02 | 🔴 | Budget/Forecast (2020–2021) gần như không trùng kỳ Actual (2015–2020); overlap chỉ 2020 H1 | **[Business]** |
| DQ-03 | 🔴 | Sales không có cột doanh thu; `Units` luôn = 1 → Revenue phải suy ra | **[Business]** — chờ định nghĩa Revenue |
| DQ-04 | 🔴 | Sales không có khóa chính; 133 dòng trùng hoàn toàn | **[Business]** — KHÔNG tự khử trùng |
| DQ-05 | 🟡 | Budget/Forecast wide + banner + header phụ | **[Fixed]** — unpivot → `Budget_Forecast.csv` dạng dài |
| DQ-06 | 🟡 | Thiếu tổ hợp Category-Segment "Rural-Productivity" trong Budget/Forecast | **[Business]** |
| DQ-07 | 🟡 | `Device` có "Deskop" (typo của Desktop) | **[Fixed]** — sửa "Deskop"→"Desktop" |
| DQ-08 | 🟡 | `TrafficChannel` thừa khoảng trắng + typo "Affliliate" | **[Fixed]** — trim + "Affliliate"→"Affiliate" |
| DQ-09 | 🟡 | `Email Name` ghép + khoảng trắng cuối ở 100% dòng | **[Fixed một phần]** — trim; tách email/tên là khuyến nghị cho khâu sau |
| DQ-10 | 🟡 | ID/Zip có số 0 đầu (rủi ro mất khi ép số) | **[Fixed]** — giữ dạng Text trong clean |
| DQ-11 | 🟡 | `Geo.Zip` có 88 giá trị <1000 nghi ngờ | **[Business]** — chưa loại |
| DQ-12 | 🟢 | `Geo.Country` hằng "USA"; `City` ghép trùng State/Country | **[Doc]** |
| DQ-13 | 🟢 | `Geo.State` chỉ 49 (US có 50+DC) | **[Doc]** — xác nhận thực tế hay thiếu |
| DQ-14 | 🟢 | `Date.Quarter` không kèm năm | **[Doc]** — ghép Year khi dùng |
| DQ-15 | 🟢 | `Product.Product` 173 tên/212 mã → tên lặp | **[Doc]** — dùng ProductID định danh |
| DQ-16 | 🟢 | `Unit Cost`/`Unit Price` độ chính xác thập phân cao bất thường | **[Doc]** |
| DQ-17 | 🟢 | Không có cột chỉ định tiền tệ | **[Business]** — xác nhận USD? |
| DQ-18 | 🟢 | `Product.Manufacturer(ID)` hằng (chỉ VanArsdel) | **[Doc]** — xác nhận chỉ bán hàng của mình |

---

# C. Technical Data Cleaning đã thực hiện

> Chỉ chuẩn hóa **kỹ thuật an toàn**, không làm thay đổi ý nghĩa nghiệp vụ. Output: `dataset/clean/` (CSV mỗi bảng). Dataset gốc `dataset/raw/` **giữ nguyên**.

| Bảng (output) | Dòng (trước → sau) | Xử lý kỹ thuật |
|---|---|---|
| `Date.csv` | 2,192 → 2,192 | Bỏ BOM ở header. Giữ nguyên. |
| `Campaign.csv` | 22 → 22 | Trim TrafficChannel/Device; "Affliliate"→"Affiliate"; "Deskop"→"Desktop". |
| `Customer.csv` | 282,597 → 282,597 | Trim khoảng trắng cuối `Email Name`; giữ CustomerID/ZipCode dạng Text. |
| `Product.csv` | 212 → 212 | Giữ nguyên. |
| `Geo.csv` | 39,948 → 39,948 | Trim khoảng trắng; giữ `Zip` dạng Text; **giữ toàn bộ dòng** (kể cả zip<1000). |
| `Sales.csv` | 675,368 → 675,368 | **Giữ toàn bộ dòng** (kể cả 133 dòng trùng); giữ IDs dạng Text. |
| `Budget_Forecast.csv` | 36 wide → **324 long** | Unpivot 9 cột; tách "Category-Segment" → `Category` + `Segment`; cột chuẩn: `Type, Year, Month, Category, Segment, Value`. |

**Nguyên tắc đã tuân thủ:** không xóa dòng, không khử trùng, không loại giá trị nghi ngờ, không suy diễn doanh thu. Các điều đó là **quyết định nghiệp vụ** → giữ ở mục Data Quality (trạng thái [Business]) và sẽ tổng hợp tại **Data Gap**.

---

# D. Business Observation (sơ bộ)

- **Nghiệp vụ:** bán lẻ sản phẩm của chính VanArsdel tại thị trường Mỹ; theo dõi kết quả bán hàng so với kế hoạch (Budget/Forecast).
- **Fact:** `Sales` (thực tế), `Budget`/`Forecast` (kế hoạch). **Dimension:** Product, Customer, Geo, Campaign, Date.
- **KPI khả thi (phụ thuộc xác nhận):** Doanh thu (= Units × Unit Price — *giả định*), Số lượng bán (chắc chắn có), Biên lợi nhuận (cần Unit Cost), Số khách mua, Mức đạt Budget (chỉ vùng overlap 2020 H1), Tăng trưởng YoY (2016–2019 đủ tin), doanh thu theo Category/Segment/Region/Campaign/Channel.
- **Giới hạn để hiểu đầy đủ nghiệp vụ:** định nghĩa Revenue, tiền tệ, kỳ so sánh Actual–Plan, bản chất District, ngữ nghĩa Campaign — tổng hợp ở **Data Gap**.

---

## Assumptions (cần stakeholder xác nhận)
| # | Giả định | Lý do |
|---|---|---|
| A1 | ID/Zip là mã định danh dạng Text (giữ số 0 đầu) | Giá trị có số 0 đầu, không phải số đo |
| A2 | Doanh thu = Units × Unit Price (gross) | Không có cột doanh thu sẵn |
| A3 | Tiền tệ là USD | Thị trường Mỹ (Country=USA) |
| A4 | Geo phản ánh vị trí khách hàng | Geo nối qua Customer; không có cửa hàng |
| A5 | Tiêu đề "Category-Segment": phần trước=Category, sau=Segment | Khớp với Product |

> Bước tiếp theo: **Business Glossary** và **Data Dictionary**, sử dụng dataset đã chuẩn hóa tại `dataset/clean/`. Mọi điểm [Business]/Assumption ở trên sẽ được đẩy sang **Data Gap** để stakeholder xác nhận, không tự kết luận.
