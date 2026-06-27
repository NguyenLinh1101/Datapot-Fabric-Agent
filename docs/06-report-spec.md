# Report Specification — VanArsdel Executive Dashboard

> Đặc tả dashboard dựa trên [05-BRD](05-business-requirements-document.md) (KPI & câu hỏi), [03-data-dictionary](03-data-dictionary.md) (bảng/cột/quan hệ), [04-data-gap](04-data-gap.md) (điểm chưa xác nhận)

## 1. Purpose & Audience

- **Audience:** CEO, Sales Director, Marketing Director, Finance Director.
- **Decisions Supported:** theo dõi sức khỏe kinh doanh tổng thể, mức đạt kế hoạch, đóng góp theo sản phẩm/vùng, hiệu quả marketing — để định hướng chiến lược, phân bổ nguồn lực, kiểm soát kế hoạch.
- **Refresh Frequency:** dữ liệu hiện ở dạng tệp tĩnh (Actual đến 30/6/2020; Budget/Forecast 2020–2021). **Không có lịch refresh tự động**; cập nhật khi VanArsdel cung cấp dữ liệu mới. (Không đặc tả ETL.)

## 2. Key Business Questions

Lấy từ BRD:
1. Doanh thu hiện tại và xu hướng theo thời gian?
2. Sản lượng bán ra (Units Sold)?
3. Có đạt Budget không, đạt bao nhiêu %?
4. Thực tế cao hơn hay thấp hơn Forecast?
5. Category / Segment nào đóng góp nhiều nhất, nhóm nào kém?
6. Region nào tốt / kém?
7. Campaign / Traffic Channel nào mang lại nhiều doanh thu?
8. Tăng trưởng YoY ra sao?

## 3. KPI Specification

| KPI | Measure (DA tạo) | Target / Benchmark | Notes |
|---|---|---|---|
| Revenue | `[Revenue]` | Budget | Assumption DG-01 (Gross/Net, thuế/chiết khấu/trả hàng); DG-02 (tiền tệ) |
| Units Sold | `[Units Sold]` | None | Assumption DG-06 (mỗi dòng = 1 đơn vị) |
| Average Selling Price | `[Avg Selling Price]` | None | Phụ thuộc `[Revenue]` (DG-01) |
| Gross Profit | `[Gross Profit]` | None | Assumption DG-01 |
| Gross Margin % | `[Gross Margin %]` | None | Assumption DG-01 |
| Budget | `[Budget]` | None | — |
| Forecast | `[Forecast]` | None | — |
| Budget Attainment % | `[Budget Attainment %]` | Budget | Assumption DG-03 (chỉ kỳ Actual & Budget cùng tồn tại) |
| Forecast Variance | `[Forecast Variance]` | Forecast | Assumption DG-03 (chỉ kỳ Forecast có Actual) |
| YoY Growth % | `[YoY Growth %]` | Prior Year | Tin cậy 2016–2019; 2015 & 2020 lệch (DG-03, DG-09) |
| Active Customers | `[Active Customers]` | None | Assumption DG-06 |

> Mọi measure tiền tệ phụ thuộc DG-01/DG-02; measure so-kế-hoạch phụ thuộc DG-03. Định nghĩa nghiệp vụ của từng KPI xem BRD §4.

## 4. Grain & Dimensions

### Fact Grain
- **Sales** = transaction level (một dòng = một lần bán một đơn vị).
- **Budget_Forecast** = Type × Year × Month × Category × Segment.

### Dimensions dùng trong dashboard
- **Date** (Year, Quarter, Month) — từ bảng `Date`.
- **Region, State, District** — từ bảng `Geo` (qua `Customer`).
- **Category, Segment, Product** — từ bảng `Product`.
- **Campaign, Traffic Channel, Device** — từ bảng `Campaign`.
- **Customer** — từ bảng `Customer` (chỉ dùng để đếm Active Customers, không hiển thị cấp cá nhân).

> Budget_Forecast nối với phần thực tế ở mức **Category–Segment × Month** (DG-03); không nối ở cấp transaction.

## 5. Dashboard Pages

### Page 1 — Executive Overview
*Mục đích trang: bức tranh tổng thể cho lãnh đạo, trả lời câu hỏi 1–4, 6.*

- **KPI Cards** — Metric: `[Revenue]`, `[Budget Attainment %]`, `[Units Sold]`, `[Gross Margin %]` · Dimension: tổng kỳ (theo filter) · Purpose: tóm tắt nhanh sức khỏe kinh doanh & mức đạt mục tiêu.
- **Revenue Trend** — Metric: `[Revenue]` · Dimension: Date (Month) · Purpose: xu hướng doanh thu theo thời gian.
- **Actual vs Budget** — Metric: `[Revenue]` & `[Budget]` · Dimension: Date (Month) · Purpose: so sánh thực tế với kế hoạch theo kỳ (Assumption DG-03).
- **Actual vs Forecast** — Metric: `[Revenue]` & `[Forecast]` · Dimension: Date (Month) · Purpose: thực tế so dự báo (Assumption DG-03).
- **Revenue by Category** — Metric: `[Revenue]` · Dimension: Category · Purpose: đóng góp doanh thu theo nhóm sản phẩm lớn.
- **Revenue by Region** — Metric: `[Revenue]` · Dimension: Region · Purpose: xác định vùng tốt/kém.

### Page 2 — Product & Sales Performance
*Mục đích trang: phân tích sản phẩm & hiệu quả bán, trả lời câu hỏi 2, 5, 8.*

- **Revenue by Category** — Metric: `[Revenue]` · Dimension: Category · Purpose: đóng góp theo Category.
- **Revenue by Segment** — Metric: `[Revenue]` · Dimension: Segment · Purpose: nhóm chi tiết hơn, phát hiện Segment kém.
- **Units Sold by Category/Segment** — Metric: `[Units Sold]` · Dimension: Category, Segment · Purpose: sản lượng tiêu thụ theo nhóm.
- **Gross Margin by Category** — Metric: `[Gross Margin %]` · Dimension: Category · Purpose: hiệu quả lợi nhuận theo nhóm (Assumption DG-01).
- **Top Products** — Metric: `[Revenue]` · Dimension: Product · Purpose: xếp hạng sản phẩm đóng góp nhiều nhất.
- **YoY Growth** — Metric: `[YoY Growth %]` · Dimension: Date (Year), Category · Purpose: tăng trưởng so cùng kỳ (DG-03/09).

### Page 3 — Marketing & Customer
*Mục đích trang: hiệu quả marketing & độ rộng khách hàng, trả lời câu hỏi 7.*

- **Revenue by Campaign** — Metric: `[Revenue]` · Dimension: Campaign · Purpose: chiến dịch nào hiệu quả (Assumption DG-08).
- **Revenue by Traffic Channel** — Metric: `[Revenue]` · Dimension: Traffic Channel · Purpose: kênh tiếp cận mang lại nhiều doanh thu.
- **Revenue by Device** — Metric: `[Revenue]` · Dimension: Device · Purpose: nền tảng gắn với doanh số tốt hơn.
- **Active Customers Trend** — Metric: `[Active Customers]` · Dimension: Date (Month) · Purpose: độ rộng tập khách theo thời gian.
- **Customer Distribution by Region** — Metric: `[Active Customers]` · Dimension: Region · Purpose: phân bố khách hàng theo vùng.

## 6. Filters

**Report-level** (áp dụng mọi trang):
- Year
- Quarter
- Month
- Region

**Page-level:**
- Page 2: Category, Segment
- Page 3: Campaign, Traffic Channel, Device

## 7. Design Notes

- Đây là **Executive Dashboard** — ưu tiên đọc nhanh, tổng hợp; không đưa chi tiết giao dịch.
- **KPI cards đặt trên cùng**; **Trend đặt trước breakdown**.
- Hạn chế số màu; giữ **consistency** về màu/định dạng/đặt tên giữa các trang.
- Dùng **drill-down** ở các hierarchy sẵn có: Category → Segment → Product; Region → State → District; Year → Quarter → Month.
- Hiển thị **display name** dễ đọc (không dùng tên cột thô).
- Visual có KPI dựa trên Assumption (DG-xx) nên giữ chú thích/nhãn để người đọc biết đang chờ xác nhận.
- Power BI có thể điều chỉnh loại visual/bố cục nhưng **không thay đổi business meaning** (metric, dimension, mục đích) đã đặc tả.

---
*Report Specification để DA build trực tiếp. Mọi KPI/visual phụ thuộc Data Gap đều được đánh dấu Assumption (DG-xx); không xác nhận giả định thành sự thật.*
