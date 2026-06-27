# Dashboard Wireframe — VanArsdel Executive Dashboard

> Phác layout dashboard ở mức Business Analyst, chứng minh dashboard giải quyết các yêu cầu trong [05-BRD](05-business-requirements-document.md) và [06-report-spec](06-report-spec.md). 
>
> Mỗi visual truy vết: **Business Question → KPI → Visual → Decision Supported**. Visual không phục vụ câu hỏi/quyết định nào sẽ không xuất hiện.

**Business Questions (BRD §3), dùng làm mã BQ:**
BQ1 Doanh thu & xu hướng · BQ2 Sản lượng (Units Sold) · BQ3 Đạt Budget? · BQ4 Actual vs Forecast · BQ5 Category/Segment đóng góp/kém · BQ6 Region tốt/kém · BQ7 Campaign/Traffic Channel hiệu quả · BQ8 YoY Growth.

---

## Page 1 — Executive Overview

### 1. Purpose
Bức tranh tổng thể cho **CEO** và **Finance Director** đọc nhanh sức khỏe kinh doanh và mức đạt kế hoạch. Trả lời **BQ1, BQ2, BQ3, BQ4, BQ5, BQ6**.

### 2. ASCII Wireframe
```
┌──────────────────────────────────────────────────────────────────────┐
│  VanArsdel — Executive Overview        [Year][Quarter][Month][Region]  │ ← report filters
├──────────────┬──────────────┬──────────────┬──────────────────────────┤
│  Revenue     │ Budget Att.% │  Units Sold  │   Gross Margin %          │ ← KPI Cards
│  (card)      │  (card)      │  (card)      │   (card)                  │
├──────────────┴──────────────┴──────────────┴──────────────────────────┤
│  Revenue Trend (line, by Month)                                        │ ← Trend (full width)
├───────────────────────────────────┬────────────────────────────────────┤
│  Actual vs Budget (col+line, Month)│  Actual vs Forecast (col+line,Month)│ ← Plan comparison
├───────────────────────────────────┼────────────────────────────────────┤
│  Revenue by Category (bar)         │  Revenue by Region (bar/map)        │ ← Breakdown
└───────────────────────────────────┴────────────────────────────────────┘
```

### 3. Visual Specification
| Visual Name | Visual Type | Metric | Dimension | Business Purpose | BQ | Decision Supported |
|---|---|---|---|---|---|---|
| KPI Cards | Card ×4 | `[Revenue]`, `[Budget Attainment %]`, `[Units Sold]`, `[Gross Margin %]` | Tổng theo filter | Tóm tắt nhanh sức khỏe kinh doanh & mức đạt mục tiêu | BQ1,2,3 | CEO/Finance đánh giá tổng quan tức thì |
| Revenue Trend | Line | `[Revenue]` | Date (Month) | Xu hướng doanh thu theo thời gian | BQ1 | Phát hiện đà tăng/giảm để điều hành |
| Actual vs Budget | Column + line | `[Revenue]`, `[Budget]` | Date (Month) | So sánh thực tế với kế hoạch | BQ3 | Finance can thiệp khi hụt kế hoạch |
| Actual vs Forecast | Column + line | `[Revenue]`, `[Forecast]` | Date (Month) | Thực tế so dự báo | BQ4 | Finance điều chỉnh dự báo |
| Revenue by Category | Bar | `[Revenue]` | Category | Đóng góp theo nhóm sản phẩm lớn | BQ5 | CEO ưu tiên dòng sản phẩm |
| Revenue by Region | Bar / Map | `[Revenue]` | Region | Vùng tốt/kém | BQ6 | Sales/CEO phân bổ theo vùng |

### 4. Page Notes
- `[Revenue]`, `[Gross Margin %]` phụ thuộc **Assumption DG-01** (định nghĩa Revenue) & **DG-02** (tiền tệ).
- `[Budget Attainment %]`, Actual vs Budget/Forecast phụ thuộc **Assumption DG-03** (kỳ/cấp so sánh) — chỉ hiển thị đúng cho kỳ Actual & Plan cùng tồn tại.

---

## Page 2 — Product & Sales Performance

### 1. Purpose
Phân tích sản phẩm & hiệu quả bán cho **Sales Director** (và CEO). Trả lời **BQ2, BQ5, BQ8**.

### 2. ASCII Wireframe
```
┌──────────────────────────────────────────────────────────────────────┐
│  Product & Sales Performance   [Year][Quarter][Month][Region]          │ ← report filters
│                                [Category][Segment]                     │ ← page filters
├───────────────────────────────────┬────────────────────────────────────┤
│  Revenue by Category (bar)         │  Revenue by Segment (bar)           │ ← breakdown
├───────────────────────────────────┼────────────────────────────────────┤
│  Units Sold by Category/Segment    │  Gross Margin % by Category (bar)   │
│  (bar)                             │                                     │
├───────────────────────────────────┴────────────────────────────────────┤
│  Top Products (table: Product | Revenue | Units | Gross Margin %)       │ ← detail at bottom
├──────────────────────────────────────────────────────────────────────┤
│  YoY Growth % (line/col, by Year & Category)                            │
└──────────────────────────────────────────────────────────────────────┘
```

### 3. Visual Specification
| Visual Name | Visual Type | Metric | Dimension | Business Purpose | BQ | Decision Supported |
|---|---|---|---|---|---|---|
| Revenue by Category | Bar | `[Revenue]` | Category | Đóng góp theo Category | BQ5 | Ưu tiên/điều chỉnh dòng sản phẩm |
| Revenue by Segment | Bar | `[Revenue]` | Segment | Phát hiện Segment kém | BQ5 | Sales can thiệp nhóm yếu |
| Units Sold by Category/Segment | Bar | `[Units Sold]` | Category, Segment | Sản lượng tiêu thụ theo nhóm | BQ2 | Đánh giá nhu cầu theo nhóm |
| Gross Margin % by Category | Bar | `[Gross Margin %]` | Category | Hiệu quả lợi nhuận theo nhóm | BQ5 | Cân đối doanh thu vs lợi nhuận |
| Top Products | Table | `[Revenue]`, `[Units Sold]`, `[Gross Margin %]` | Product | Xếp hạng sản phẩm đóng góp nhiều nhất | BQ5 | Tập trung sản phẩm chủ lực |
| YoY Growth | Line / Column | `[YoY Growth %]` | Date (Year), Category | Tăng trưởng so cùng kỳ | BQ8 | Đánh giá đà tăng trưởng dài hạn |

### 4. Page Notes
- Mọi visual tiền tệ phụ thuộc **DG-01/DG-02**; `[Gross Margin %]` cần `Unit Cost` (DG-01).
- `[YoY Growth %]` tin cậy cho **2016–2019**; 2015 & 2020 lệch/khuyết (**DG-03, DG-09**) — nên chú thích kỳ áp dụng.

---

## Page 3 — Marketing & Customer

### 1. Purpose
Hiệu quả marketing & độ rộng khách hàng cho **Marketing Director**. Trả lời **BQ7** (và bổ trợ BQ1, BQ6 ở góc khách hàng).

### 2. ASCII Wireframe
```
┌──────────────────────────────────────────────────────────────────────┐
│  Marketing & Customer   [Year][Quarter][Month][Region]                 │ ← report filters
│                         [Campaign][Traffic Channel][Device]            │ ← page filters
├──────────────────────────┬──────────────────────┬──────────────────────┤
│ Revenue by Campaign (bar)│ Revenue by Traffic    │ Revenue by Device    │ ← marketing breakdown
│                          │ Channel (bar)         │ (bar/donut)          │
├──────────────────────────┴───────────┬──────────┴──────────────────────┤
│  Active Customers Trend (line, Month) │  Customer Distribution by Region │ ← customer
│                                       │  (bar/map)                       │
└───────────────────────────────────────┴──────────────────────────────────┘
```

### 3. Visual Specification
| Visual Name | Visual Type | Metric | Dimension | Business Purpose | BQ | Decision Supported |
|---|---|---|---|---|---|---|
| Revenue by Campaign | Bar | `[Revenue]` | Campaign | Chiến dịch nào hiệu quả | BQ7 | Marketing phân bổ ngân sách chiến dịch |
| Revenue by Traffic Channel | Bar | `[Revenue]` | Traffic Channel | Kênh tiếp cận mang lại nhiều doanh thu | BQ7 | Tối ưu đầu tư theo kênh |
| Revenue by Device | Bar / Donut | `[Revenue]` | Device | Nền tảng gắn với doanh số tốt hơn | BQ7 | Đầu tư trải nghiệm theo thiết bị |
| Active Customers Trend | Line | `[Active Customers]` | Date (Month) | Độ rộng tập khách theo thời gian | BQ1 (bổ trợ) | Theo dõi sức khỏe tập khách |
| Customer Distribution by Region | Bar / Map | `[Active Customers]` | Region | Phân bố khách theo vùng | BQ6 (bổ trợ) | Định hướng thị trường theo vùng |

### 4. Page Notes
- Revenue theo Campaign/Channel/Device phụ thuộc **Assumption DG-08** (Campaign–Sales là attribution hay chỉ chiến dịch đang chạy).
- `[Active Customers]` phụ thuộc **DG-06** (mỗi dòng Sales = một đơn vị/giao dịch).

---

## Global Section

### Navigation
1. Executive Overview
2. Product & Sales Performance
3. Marketing & Customer

### Global Filters (report-level, áp dụng mọi trang)
- Year
- Quarter
- Month
- Region

*(Page-level: Page 2 — Category, Segment; Page 3 — Campaign, Traffic Channel, Device.)*

### Design Principles
- **KPI Cards luôn đặt trên cùng** của trang tổng quan.
- **Trend trước, Breakdown sau, Detail/Table ở cuối trang.**
- **Layout nhất quán** giữa các page (vị trí filter, tiêu đề, thứ tự visual).
- Ưu tiên **Executive Summary**: đọc nhanh, ra quyết định; không hiển thị thông tin dư thừa.
- **Drill-down** theo hierarchy: Category → Segment → Product; Region → State → District; Year → Quarter → Month.
- Visual phải **dễ đọc và hỗ trợ ra quyết định**; hạn chế số màu; dùng display name dễ đọc.
- Visual phụ thuộc Assumption (DG-xx) giữ chú thích để người đọc biết đang chờ xác nhận.

---

## Coverage Check

| Business Question | KPI | Visual | Page | Covered |
|---|---|---|---|---|
| BQ1 — Doanh thu & xu hướng | Revenue | Revenue Trend; KPI Card; Active Customers Trend (bổ trợ) | 1, 3 | Yes |
| BQ2 — Sản lượng bán | Units Sold | KPI Card; Units Sold by Category/Segment | 1, 2 | Yes |
| BQ3 — Đạt Budget? | Budget Attainment %, Budget | KPI Card; Actual vs Budget | 1 | Yes |
| BQ4 — Actual vs Forecast | Forecast Variance, Forecast | Actual vs Forecast | 1 | Yes |
| BQ5 — Category/Segment đóng góp/kém | Revenue, Gross Margin % | Revenue by Category/Segment; Gross Margin %; Top Products | 1, 2 | Yes |
| BQ6 — Region tốt/kém | Revenue | Revenue by Region; Customer Distribution by Region | 1, 3 | Yes |
| BQ7 — Campaign/Traffic Channel hiệu quả | Revenue | Revenue by Campaign/Traffic Channel/Device | 3 | Yes |
| BQ8 — YoY Growth | YoY Growth % | YoY Growth | 2 | Yes |

**Kết luận Coverage:** Toàn bộ 8 Business Questions trong BRD đều được ít nhất một visual bao phủ. Không có visual "chỉ để làm đẹp". Không có Business Requirement nào chưa được wireframe bao phủ.

---
*Wireframe là cầu nối giữa Business Requirement và Implementation. Visual type chỉ là gợi ý; Data Analyst có thể điều chỉnh loại visual/bố cục nhưng không thay đổi metric, dimension, mục đích đã đặc tả. KPI/visual phụ thuộc Data Gap được đánh dấu Assumption (DG-xx), không xác nhận thành sự thật.*
