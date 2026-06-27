# Data Dictionary — VanArsdel

> **Bước 03 — Discovery.** Cầu nối giữa Business và Technical: giúp Data Analyst hiểu dataset có những bảng nào, mỗi cột mang ý nghĩa nghiệp vụ gì, và các bảng liên kết ra sao — mà không cần mở trực tiếp file dữ liệu.
>
> Nguồn tham chiếu: dataset đã chuẩn hóa kỹ thuật tại `dataset/clean/` (xem [01-data-analysis.md](01-data-analysis.md)). 

## Quy ước
- **Model column:** tên cột trong mô hình. **Source:** bảng/sheet nguồn. **Type:** Text / Whole Number / Decimal / Date. **Hidden:** Yes/No (gợi ý ẩn khỏi báo cáo, ví dụ khóa kỹ thuật hoặc cột hằng). **Description:** ý nghĩa nghiệp vụ.
- 7 bảng: `Date`, `Campaign`, `Customer`, `Product`, `Geo`, `Sales`, `Budget_Forecast`.

---

### Table: `Date`
**Business Purpose:** Trục thời gian chuẩn để theo dõi và so sánh kết quả kinh doanh theo kỳ.
**Grain:** Một dòng = một ngày dương lịch.
**Primary Key:** `Date`.
**Foreign Key:** Không có.

| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| `Date` | Actuals — Date | Date | No | Ngày lịch cụ thể; dùng làm trục thời gian và nối với Sales. |
| `MonthNo` | Actuals — Date | Whole Number | Yes | Số thứ tự tháng (1–12); dùng để sắp xếp tháng đúng thứ tự. |
| `MonthName` | Actuals — Date | Text | No | Tên tháng viết tắt (Jan…Dec) để hiển thị. |
| `MonthID` | Actuals — Date | Whole Number | Yes | Mã năm+tháng (YYYYMM); dùng sắp xếp/so sánh tháng xuyên năm. |
| `Month` | Actuals — Date | Text | No | Nhãn tháng kèm năm (vd "Jan-16") để hiển thị. |
| `Quarter` | Actuals — Date | Text | No | Quý trong năm (Q1–Q4). *Need Business Confirmation:* không kèm năm nên khi phân tích xuyên năm phải ghép với `Year`. |
| `Year` | Actuals — Date | Whole Number | No | Năm (2016–2021). |

---

### Table: `Campaign`
**Business Purpose:** Danh mục chiến dịch marketing, gắn doanh số với kênh và thiết bị tiếp cận.
**Grain:** Một dòng = một chiến dịch marketing.
**Primary Key:** `CampaignID`.
**Foreign Key:** Không có.

| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| `CampaignID` | Actuals — Campaign | Whole Number | Yes | Mã định danh chiến dịch; dùng để nối với Sales. |
| `TrafficChannel` | Actuals — Campaign | Text | No | Kênh tiếp cận khách của chiến dịch (vd Organic Search, Email, Affiliate, Mail). |
| `Device` | Actuals — Campaign | Text | No | Thiết bị/hình thức khách tương tác (Desktop, Mobile, Tablet, Paper). |

---

### Table: `Customer`
**Business Purpose:** Danh mục khách hàng mua hàng; gắn doanh số với khách và (qua ZIP) với địa lý.
**Grain:** Một dòng = một khách hàng.
**Primary Key:** `CustomerID`.
**Foreign Key:** `ZipCode` → `Geo.Zip`.

| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| `CustomerID` | Actuals — Customer | Text | Yes | Mã định danh khách hàng (giữ số 0 đầu); dùng để nối với Sales. |
| `ZipCode` | Actuals — Customer | Text | Yes | Mã ZIP nơi khách cư trú; khóa nối tới Geo để lấy thông tin địa lý. |
| `Email Name` | Actuals — Customer | Text | No | Trường ghép gồm email và họ tên khách. *Need Business Confirmation:* ràng buộc bảo mật (PII) khi hiển thị; có cần tách email/tên riêng không. |

---

### Table: `Product`
**Business Purpose:** Danh mục sản phẩm VanArsdel bán ra, kèm phân loại và giá.
**Grain:** Một dòng = một sản phẩm.
**Primary Key:** `ProductID`.
**Foreign Key:** Không có.

| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| `ProductID` | Actuals — Product | Whole Number | Yes | Mã định danh sản phẩm; dùng để nối với Sales và định danh sản phẩm. |
| `Product` | Actuals — Product | Text | No | Tên sản phẩm. *Need Business Confirmation:* tên có thể trùng giữa các mã; định danh nên dùng `ProductID`. |
| `Category` | Actuals — Product | Text | No | Nhóm sản phẩm cấp cao (cấp cha của Segment). |
| `Segment` | Actuals — Product | Text | No | Phân khúc sản phẩm cấp trung gian (thuộc một Category). |
| `ManufacturerID` | Actuals — Product | Whole Number | Yes | Mã nhà sản xuất; hiện chỉ một giá trị. |
| `Manufacturer` | Actuals — Product | Text | Yes | Tên nhà sản xuất; hằng số "VanArsdel" → dữ liệu chỉ gồm hàng của chính công ty. |
| `Unit Cost` | Actuals — Product | Decimal | No | Giá vốn cho một đơn vị sản phẩm. |
| `Unit Price` | Actuals — Product | Decimal | No | Giá bán cho một đơn vị sản phẩm. |

---

### Table: `Geo`
**Business Purpose:** Phân cấp địa lý theo mã ZIP, cho phép phân tích doanh số theo địa bàn.
**Grain:** Một dòng = một mã ZIP.
**Primary Key:** `Zip`.
**Foreign Key:** Không có.

| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| `Zip` | Actuals — Geo | Text | Yes | Mã ZIP (đơn vị địa lý nhỏ nhất); khóa nối từ Customer. |
| `City` | Actuals — Geo | Text | No | Tên thành phố (đang ở dạng ghép "City, ST, USA"). |
| `State` | Actuals — Geo | Text | No | Bang (viết tắt 2 ký tự). |
| `Region` | Actuals — Geo | Text | No | Vùng địa lý cấp cao (East/Central/West). |
| `District` | Actuals — Geo | Text | No | Khu vực địa bàn được đánh số. *Need Business Confirmation:* là địa giới hành chính, vùng bán hàng hay đơn vị tổ chức nội bộ. |
| `Country` | Actuals — Geo | Text | Yes | Quốc gia; hằng số "USA" → toàn bộ thị trường tại Mỹ. |

---

### Table: `Sales`
**Business Purpose:** Ghi nhận hoạt động bán hàng thực tế (Actual) — sự kiện trung tâm tạo doanh thu.
**Grain:** Một dòng = một lần bán một đơn vị của một sản phẩm, cho một khách, gắn một chiến dịch, tại một ngày.
**Primary Key:** Không có (dataset không có mã giao dịch). *Need Business Confirmation:* tồn tại dòng trùng hoàn toàn — cách xử lý do business quyết định.
**Foreign Key:** `ProductID` → `Product`; `CustomerID` → `Customer`; `CampaignID` → `Campaign`; `Date` → `Date`.

| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| `ProductID` | Actuals — Sales | Whole Number | Yes | Sản phẩm được bán; nối tới Product. |
| `Date` | Actuals — Sales | Date | Yes | Ngày bán; nối tới Date. *Need Business Confirmation:* dữ liệu bán năm 2015 nằm ngoài bảng Date. |
| `CustomerID` | Actuals — Sales | Text | Yes | Khách hàng mua (giữ số 0 đầu); nối tới Customer. |
| `CampaignID` | Actuals — Sales | Whole Number | Yes | Chiến dịch gắn với lần bán; nối tới Campaign. |
| `Units` | Actuals — Sales | Whole Number | No | Số đơn vị của lần bán; hiện luôn bằng 1. |

---

### Table: `Budget_Forecast`
**Business Purpose:** Mục tiêu kế hoạch (Budget) và dự báo (Forecast) doanh thu theo tháng và nhóm sản phẩm, để đối chiếu với kết quả thực tế.
**Grain:** Một dòng = một (Type × Year × Month × Category × Segment).
**Primary Key:** Khóa tổ hợp (`Type`, `Year`, `Month`, `Category`, `Segment`).
**Foreign Key:** Không có khóa vật lý (nối logic — xem Relationships).

| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| `Type` | Budget_Forecast | Text | No | Loại số liệu kế hoạch: "Budget" (mục tiêu) hoặc "Forecast" (dự báo). |
| `Year` | Budget_Forecast | Whole Number | No | Năm của kế hoạch (Budget: 2020–2021; Forecast: 2021). |
| `Month` | Budget_Forecast | Text | No | Tháng của kế hoạch (Jan…Dec). |
| `Category` | Budget_Forecast | Text | No | Nhóm sản phẩm cấp cao (tách từ tiêu đề "Category-Segment"). |
| `Segment` | Budget_Forecast | Text | No | Phân khúc sản phẩm (tách từ tiêu đề "Category-Segment"). |
| `Value` | Budget_Forecast | Decimal | No | Giá trị tiền kế hoạch/dự báo cho tổ hợp tương ứng. |

---

## Relationships

| From Table | From Column | To Table | To Column | Cardinality | Description |
|---|---|---|---|---|---|
| `Sales` | `ProductID` | `Product` | `ProductID` | * : 1 | Mỗi lần bán gắn với một sản phẩm; nhiều lần bán cùng một sản phẩm. |
| `Sales` | `CustomerID` | `Customer` | `CustomerID` | * : 1 | Mỗi lần bán gắn với một khách hàng. |
| `Sales` | `CampaignID` | `Campaign` | `CampaignID` | * : 1 | Mỗi lần bán gắn với một chiến dịch marketing. |
| `Sales` | `Date` | `Date` | `Date` | * : 1 | Mỗi lần bán xảy ra vào một ngày. *Need Business Confirmation:* dữ liệu bán 2015 không có ngày tương ứng trong `Date`. |
| `Customer` | `ZipCode` | `Geo` | `Zip` | * : 1 | Mỗi khách thuộc một mã ZIP; doanh số tới địa lý đi theo đường Sales → Customer → Geo. |

**Budget_Forecast — không có relationship vật lý.** Bảng kế hoạch không chứa khóa nối trực tiếp tới các bảng khác. Về nghiệp vụ, nó được đối chiếu với `Sales` ở mức **Category–Segment theo tháng** (qua `Category`,`Segment` ↔ `Product`; `Year`,`Month` ↔ `Date`). Cách nối cụ thể là **quyết định mô hình hóa của Data Analyst** và phụ thuộc các điểm cần xác nhận (kỳ so sánh, nhóm thiếu kế hoạch) — *Need Business Confirmation*, không khẳng định relationship tại đây.

---
*Tài liệu Business Analysis, ưu tiên ý nghĩa nghiệp vụ. Không mô tả chi tiết ETL/triển khai kỹ thuật. Các điểm "Need Business Confirmation" sẽ được tổng hợp tại Data Gap (bước 04).*
