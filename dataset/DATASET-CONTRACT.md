# Dataset Contract — VanArsdel (Business-ready)

## 1. Tables

| Table | Role | Grain | Rows | Primary Key |
|---|---|---|--:|---|
| `Sales` | Fact (Actual) | một lần bán một đơn vị (Product × Date × Customer × Campaign) | 675,368 | — (không có khóa giao dịch) |
| `Budget_Forecast` | Fact (Plan) | Type × Year × Month × Category × Segment | 324 | (Type, Year, MonthID, Category, Segment) |
| `Date` | Dimension | một ngày | 2,192 | `Date` |
| `Product` | Dimension | một sản phẩm | 212 | `ProductID` |
| `Customer` | Dimension | một khách hàng | 282,597 | `CustomerID` |
| `Geo` | Dimension | một mã ZIP | 39,948 | `Zip` |
| `Campaign` | Dimension | một chiến dịch | 22 | `CampaignID` |

## 2. Datatypes & Keys (model)

- **Khóa/ID dạng Text** (giữ số 0 đầu): `CustomerID`, `ZipCode`/`Zip`. `ProductID`, `CampaignID` là Whole Number.
- **Date**: `Date.Date` và `Sales.Date` ở định dạng ISO `YYYY-MM-DD` → nối trực tiếp, không cần parse.
- **Decimal**: `Unit Cost`, `Unit Price`, `Budget_Forecast.Value`.
- **Sort keys**: `Date.MonthNo`, `Date.MonthID` (sắp xếp tháng đúng thứ tự).

## 3. Relationships (cho Semantic Model)

| From | To | Cardinality | Join key |
|---|---|---|---|
| `Sales` | `Product` | * : 1 | `ProductID` |
| `Sales` | `Customer` | * : 1 | `CustomerID` |
| `Sales` | `Campaign` | * : 1 | `CampaignID` |
| `Sales` | `Date` | * : 1 | `Date` |
| `Customer` | `Geo` | * : 1 | `ZipCode` → `Zip` |
| `Budget_Forecast` | `Date` | * : 1 | `MonthID` → `Date.MonthID`* |

\* `Budget_Forecast` không có khóa vật lý tới Product; đối chiếu với Actual ở mức **Category–Segment × tháng** (BRD Assumption DG-03). Quan hệ với `Date` ở cấp tháng qua `MonthID`. Cách nối cụ thể là quyết định mô hình hóa của DA.

## 4. Business Rules áp dụng (đã ghi trong BRD)

- **Revenue = `Units` × `Unit Price`** (BRD Assumption DG-01) — **tính ở tầng measure**, KHÔNG materialize vào dòng Sales (vì `Units` = 1, nhân ra rồi nhét vào fact là denormalize/thêm dữ liệu).
- **COGS = `Units` × `Unit Cost`**; **Gross Profit = Revenue − COGS** (DG-01).
- **Budget = Σ `Value` khi `Type = Budget`**; **Forecast = Σ `Value` khi `Type = Forecast`**.
- **Lịch dương lịch** (DG-09): `YearQuarter` = `Year` + `Quarter` đã thêm sẵn để cắt quý xuyên năm.

## 5. Chuẩn hóa Business-ready đã áp dụng (so với `dataset/clean/`)

| Table | Thay đổi | Lý do |
|---|---|---|
| `Date` | `Date` → ISO; thêm `YearQuarter` | Nối ngày sạch; Quarter không kèm năm (DG-09) |
| `Sales` | `Date` → ISO | Nối trực tiếp với `Date` |
| `Geo` | Tách `City` "City, ST, USA" → tên thành phố | Bỏ phần trùng State/Country, dễ đọc |
| `Budget_Forecast` | Thêm `MonthNo`, `MonthID` (suy từ Year+Month) | Nối Actual↔Plan theo tháng |
| `Campaign`, `Customer`, `Product` | Không đổi | Đã chuẩn ở clean |

> Mọi bảng **giữ nguyên số dòng**; không thêm/xóa/nội suy/sửa số liệu.

## 6. Hierarchies (gợi ý cho model)

- **Product:** Category → Segment → Product
- **Geography:** Region → State → District → City → Zip
- **Date:** Year → Quarter (`YearQuarter`) → Month → Date
- **Marketing:** Campaign → (Traffic Channel, Device)

## 7. Display-name gợi ý (tầng model, tên cột vật lý giữ nguyên để truy vết Data Dictionary)

`Zip` → "ZIP Code" · `Unit Cost` → "Unit Cost" · `Unit Price` → "Unit Price" · `Budget_Forecast.Value` → "Plan Amount" · `TrafficChannel` → "Traffic Channel" · `MonthNo` → ẩn (sort). DA có thể đặt display name; không đổi business meaning.

## 8. Outstanding Data Gaps — GIỮ NGUYÊN, chưa xử lý ở dataset

Các điểm sau **vẫn thuộc Data Gap** (chưa có Business Assumption) → dữ liệu để nguyên, chờ stakeholder ([04-data-gap](../../docs/part-01/04-data-gap.md)):

- **DG-01/02:** định nghĩa Revenue (Gross/Net, thuế/chiết khấu/trả hàng), tiền tệ → Revenue chỉ là measure theo assumption, chưa chốt.
- **DG-03:** Sales đến 30/6/2020 & năm 2015 nằm ngoài `Date` (2016–2021); Budget 2020–2021, Forecast chỉ 2021. **Không** thêm 2015 vào `Date`, **không** cắt dữ liệu → DA xử lý kỳ so sánh sau khi stakeholder chốt.
- **DG-06:** `Sales` có dòng trùng hoàn toàn & không có khóa giao dịch → **giữ nguyên**, không khử trùng.
- **DG-08:** ngữ nghĩa Campaign–Sales (attribution?) chưa chốt.
- **DG-10:** tổ hợp Rural-Productivity có ở Product nhưng không có trong Budget/Forecast → giữ nguyên.

---
*Dataset business-ready: sạch kỹ thuật, chuẩn hóa business có truy vết, nhất quán, sẵn sàng import Power BI để dựng Semantic Model. Mọi điểm chưa chốt nằm ở Outstanding Data Gaps, không tự suy diễn.*
