# Business Requirements Document (BRD) — VanArsdel Executive Dashboard

> Chuyển kết quả Discovery thành yêu cầu nghiệp vụ để Data Analyst bắt đầu thiết kế dashboard. Thuật ngữ theo [02-business-glossary](02-business-glossary.md); cột/bảng theo [03-data-dictionary](03-data-dictionary.md); điểm chưa xác nhận theo [04-data-gap](04-data-gap.md).

## 1. Purpose

BRD xác định **dashboard cuối cùng phải phục vụ ai, hỗ trợ quyết định gì, trả lời câu hỏi nào và đo KPI nào**. Đây là đầu vào trực tiếp cho **Report Specification** (bước 06): Report Spec sẽ cụ thể hóa các yêu cầu ở đây thành trang, visual và bố cục. BRD không mô tả cách trình bày.

## 2. Business Users & Decisions

| User | Business Goal | Decisions Supported |
|---|---|---|
| **CEO** | Nắm sức khỏe kinh doanh tổng thể và mức đạt mục tiêu | Định hướng chiến lược; ưu tiên nguồn lực giữa dòng sản phẩm/vùng |
| **Sales Director** | Theo dõi doanh thu theo vùng và nhóm sản phẩm so với kế hoạch | Phân bổ mục tiêu/nguồn lực bán hàng; can thiệp vùng hoặc nhóm đang hụt kế hoạch |
| **Marketing Director** | Đánh giá đóng góp doanh thu theo chiến dịch và kênh | Phân bổ ngân sách marketing; điều chỉnh/dừng chiến dịch kém hiệu quả |
| **Finance Director** | Đối chiếu thực tế với ngân sách và dự báo theo kỳ | Kiểm soát mức đạt ngân sách; cảnh báo lệch kế hoạch sớm |

## 3. Business Questions

Dashboard phải trả lời:

1. Doanh thu hiện tại như thế nào, và xu hướng theo thời gian ra sao?
2. Đã bán được bao nhiêu sản lượng (số đơn vị)?
3. Thực tế có **đạt Budget** không, đạt bao nhiêu phần trăm?
4. Thực tế cao hơn hay thấp hơn **Forecast**?
5. **Category / Segment** nào đóng góp doanh thu nhiều nhất, nhóm nào kém?
6. **Region** nào hoạt động tốt / kém?
7. **Campaign** và **Traffic Channel** nào mang lại nhiều doanh thu?
8. Doanh thu tăng trưởng thế nào so với cùng kỳ năm trước (**YoY**)?

## 4. Business KPIs

> Công thức ở mức nghiệp vụ (không DAX). Nguồn truy vết về Data Dictionary. KPI dựa trên giả định được ghi rõ.

| KPI | Business Definition | Formula (Business) | Data Source |
|---|---|---|---|
| **Revenue** | Tổng giá trị tiền thu được từ bán hàng | Σ (`Units` × `Unit Price`) — *Assumption (DG-01): chưa rõ Gross/Net, thuế, chiết khấu, trả hàng* | `Sales` + `Product` |
| **Units Sold** | Tổng sản lượng bán ra | Σ `Units` | `Sales` |
| **Average Selling Price** | Giá bán bình quân trên một đơn vị | `Revenue` ÷ `Units Sold` | `Sales` + `Product` |
| **Gross Profit** | Lợi nhuận gộp từ bán hàng | `Revenue` − Σ (`Units` × `Unit Cost`) — *Assumption (DG-01)* | `Sales` + `Product` |
| **Gross Margin %** | Tỷ suất lợi nhuận gộp | `Gross Profit` ÷ `Revenue` | `Sales` + `Product` |
| **Budget** | Mục tiêu doanh thu kế hoạch | Σ `Value` khi `Type = Budget` | `Budget_Forecast` |
| **Forecast** | Doanh thu dự báo | Σ `Value` khi `Type = Forecast` | `Budget_Forecast` |
| **Budget Attainment %** | Mức đạt ngân sách | `Revenue` (Actual) ÷ `Budget` — *chỉ tính cho kỳ Actual & Budget cùng tồn tại (DG-03)* | `Sales`+`Product`, `Budget_Forecast` |
| **Forecast Variance** | Chênh lệch thực tế so dự báo | `Revenue` (Actual) − `Forecast` (và % so với `Forecast`) — *phụ thuộc kỳ có Actual (DG-03)* | `Sales`+`Product`, `Budget_Forecast` |
| **YoY Growth %** | Tăng trưởng doanh thu so cùng kỳ năm trước | (`Revenue` kỳ này − `Revenue` cùng kỳ năm trước) ÷ `Revenue` cùng kỳ năm trước — *tin cậy cho 2016–2019; 2015 & 2020 lệch (DG-03)* | `Sales` + `Product`, `Date` |
| **Active Customers** | Số khách hàng phát sinh mua trong kỳ | Số `CustomerID` riêng biệt trong `Sales` | `Sales` |

> Mọi KPI tiền tệ phụ thuộc **định nghĩa Revenue (DG-01)** và **đơn vị tiền tệ (DG-02)**. KPI so kế hoạch phụ thuộc **kỳ/cấp so sánh (DG-03)**. Các chiều phân tích (Category, Segment, Region, Campaign, Traffic Channel, Device, Time) lấy từ các bảng dimension tương ứng.

## 5. Business Assumptions

> Chỉ các giả định còn ảnh hưởng trực tiếp tới nghiệp vụ dashboard (chi tiết & các điểm khác ở Data Gap).

- **Revenue = Units × Unit Price (gross)** — chưa xác nhận Gross/Net & các khoản điều chỉnh (DG-01).
- **Tiền tệ là USD** (DG-02).
- **So sánh Actual–Plan ở mức Category-Segment theo tháng**, cho kỳ hai bên cùng tồn tại (DG-03).
- **Mỗi dòng `Sales` = một đơn vị bán** — ảnh hưởng cách đếm Units/Active Customers (DG-06).
- **Lịch dùng năm dương lịch** — ảnh hưởng KPI thời gian/YoY (DG-09).

## 6. Dashboard Scope

### In Scope
- Theo dõi **Revenue, Units Sold** và xu hướng theo thời gian.
- **Mức đạt kế hoạch**: Actual vs Budget, Actual vs Forecast (trong kỳ dữ liệu cho phép).
- Phân tích đóng góp theo **Category / Segment**.
- Phân tích theo **Region** (và cấp địa lý sẵn có).
- Hiệu quả **Campaign / Traffic Channel / Device**.
- Lợi nhuận gộp & biên lợi nhuận (nếu Revenue được xác nhận).

### Out of Scope
- Chi tiết từng giao dịch / báo cáo tác nghiệp.
- Phân tích đối thủ / thị phần (dữ liệu chỉ gồm sản phẩm VanArsdel).
- Tồn kho, chuỗi cung ứng, nhân sự, báo cáo tài chính kế toán.
- Dự báo bằng AI/ML.
- Phân tích cấp khách hàng cá nhân (PII) — ngoài phạm vi dashboard điều hành.

---
*Đây là scope của dashboard, không phải scope dự án. BRD chỉ nên được chốt sau khi các Data Gap **High (DG-01 → DG-05)** được stakeholder xác nhận; trước đó, các KPI tiền tệ và so-kế-hoạch giữ trạng thái dựa trên Assumption.*
