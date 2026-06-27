# Data Gap & Questions for Stakeholder — VanArsdel

## 1. Purpose

Data Gap ghi nhận những điểm **chưa thể kết luận chỉ từ dataset và các tài liệu đã xây dựng**, nhằm phân biệt rõ giữa **điều Business Analyst có thể suy luận hợp lý** và **điều cần được stakeholder xác nhận**.

Tài liệu này **không nhằm liệt kê mọi điểm bất thường của dữ liệu**, cũng không phải tài liệu làm sạch dữ liệu. Những vấn đề đã được xử lý ở bước Technical Data Cleaning hoặc có thể kết luận hợp lý từ dataset sẽ không được đưa vào Data Gap.

Chỉ những nội dung có khả năng ảnh hưởng đến **định nghĩa KPI, cách diễn giải nghiệp vụ hoặc thiết kế dashboard** mới được ghi nhận để xác nhận trước khi hoàn thiện Business Requirements Document (BRD) và Report Specification.

## 2. Data Gap Summary

| ID | Area | Observation | Business Impact | Question for Stakeholder | Priority |
|---|---|---|---|---|---|
| DG-01 | Revenue / Metric Definition | `Sales` không có cột doanh thu; `Units` luôn = 1 | Không thể định nghĩa KPI doanh thu — nền tảng của toàn bộ dashboard | "Revenue" tính thế nào (Units × Unit Price)? Là **Gross hay Net**? Có gồm **VAT** / trừ **chiết khấu** / loại **đơn trả hàng**? | High |
| DG-02 | Currency | Dữ liệu không nêu đơn vị tiền tệ | Mọi KPI tiền tệ hiển thị sai đơn vị | Đơn vị tiền tệ là **USD** hay khác? | High |
| DG-03 | Time / Budget / Forecast | Actual: 2015→30/6/2020; Budget: 2020–2021; Forecast: chỉ 2021 | Không xác định được kỳ và cấp so sánh Actual–Plan → không viết được KPI mức đạt kế hoạch | So sánh Actual vs Budget/Forecast cho **kỳ nào**, ở **cấp nào** (Category-Segment theo tháng)? Xử lý 2015 và nửa cuối 2020 ra sao? | High |
| DG-04 | Reporting | Đề bài "chưa biết rõ cần gì"; người dùng & mục tiêu hiện là giả định | Không xác định được scope & KPI chính của dashboard | Dashboard phục vụ **ai** (CEO/Sales/Marketing/Finance Director)? **Mục tiêu** chính và **KPI nào là KPI chính**? | High |
| DG-05 | Metric Definition | Chưa có target/benchmark cho KPI | Không thể trình bày "đạt/không đạt" trong Report Specification | Mỗi KPI có **target/benchmark** không, lấy từ đâu (vd Budget làm mục tiêu)? | High |
| DG-06 | Sales / Business Rules | Không có khóa giao dịch; tồn tại dòng trùng hoàn toàn | Đếm trùng sản lượng/doanh thu → KPI sai | Mỗi dòng Sales = một giao dịch một đơn vị? Dòng trùng là **giao dịch hợp lệ hay lỗi**? | Medium |
| DG-07 | Budget / Forecast | Chưa rõ khác biệt nghiệp vụ giữa Budget và Forecast | Hiểu sai bản chất hai chỉ tiêu → so sánh sai | Budget và Forecast **khác nhau nghiệp vụ thế nào** (mục tiêu cứng vs dự báo)? Forecast cập nhật bao lâu/lần? | Medium |
| DG-08 | Campaign / Business Rules | Quan hệ Campaign–Sales chưa rõ là attribution | Diễn giải sai "doanh số đến từ chiến dịch" → KPI marketing sai | Campaign được **mapping với Sales theo business rule nào** (attribution hay chỉ chiến dịch đang chạy)? | Medium |
| DG-09 | Time | `Quarter` không kèm năm; chưa rõ Calendar/Fiscal | KPI thời gian (YoY, YTD…) có thể sai kỳ | Dùng **Calendar hay Fiscal Year**? "Quý" theo lịch nào? | Medium |
| DG-10 | Budget / Business Rules | `Product` có tổ hợp Rural-Productivity nhưng Budget/Forecast không có | KPI mức đạt kế hoạch của nhóm này phản ánh sai | Nhóm có doanh số nhưng **không có kế hoạch** xử lý thế nào khi tính Budget Attainment? | Low |

## 3. Open Questions by Category

**Business Metrics & Definition**
- "Revenue" tính thế nào; Gross hay Net; gồm VAT/chiết khấu/trả hàng? (DG-01)
- Đơn vị tiền tệ? (DG-02)
- KPI có target/benchmark không? (DG-05)

**Sales Process / Business Rules**
- Dòng Sales trùng là giao dịch hợp lệ hay lỗi; mỗi dòng = một đơn vị? (DG-06)
- Campaign mapping với Sales theo rule nào? (DG-08)
- Nhóm sản phẩm thiếu kế hoạch xử lý ra sao? (DG-10)

**Budget & Forecast**
- Kỳ và cấp so sánh Actual vs Budget/Forecast? (DG-03)
- Budget và Forecast khác nhau nghiệp vụ thế nào? (DG-07)

**Reporting**
- Dashboard phục vụ ai, mục tiêu và KPI chính là gì? (DG-04)
- Dùng Calendar hay Fiscal Year? (DG-09)

## 4. Assumptions Waiting for Validation

> Giả định đang dùng tạm để dự án tiến triển — **chưa khẳng định đúng/sai**, cần stakeholder xác nhận.

| Assumption | Liên quan |
|---|---|
| Revenue = Units Sold × Unit Price (gross) | DG-01 |
| Đơn vị tiền tệ là USD | DG-02 |
| So sánh Actual–Plan ở mức Category-Segment theo tháng | DG-03 |
| Giá trị `Budget_Forecast` là doanh thu kế hoạch/dự báo, cùng đơn vị với Revenue | DG-01, DG-03 |
| Mỗi dòng `Sales` = một đơn vị bán | DG-06 |
| Dashboard phục vụ Ban lãnh đạo (CEO & các Director) | DG-04 |
| Lịch dùng năm dương lịch | DG-09 |

---
*BRD và Report Specification chỉ nên chốt sau khi các gap **High** (DG-01 → DG-05) được stakeholder trả lời.*
