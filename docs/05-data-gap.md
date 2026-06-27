# Data Gap & Questions for Stakeholder — VanArsdel

> Tổng hợp các nội dung **cần stakeholder (Business Sponsor — Director of Sales & Marketing) xác nhận** trước khi cố định ngữ nghĩa nghiệp vụ và xây dựng Executive Dashboard.
> Tách từ [03-business-glossary.md](03-business-glossary.md). Vấn đề kỹ thuật/chất lượng dữ liệu chi tiết nằm ở [02-data-quality-report.md](02-data-quality-report.md); bối cảnh & câu hỏi nghiệp vụ ở [01-data-investigation.md](01-data-investigation.md).

## Mức ưu tiên
🔴 **Chặn build** — phải làm rõ trước khi dựng dashboard · 🟡 **Quan trọng** — ảnh hưởng cách thiết kế · 🟢 **Nên rõ** — cải thiện độ chính xác diễn giải.

| # | Khái niệm | Nội dung cần xác nhận | Ưu tiên |
|---|---|---|---|
| G-1 | **Revenue** | Doanh thu chưa có sẵn; cần **định nghĩa chính thức** (gross/net; có trừ thuế, chiết khấu, trả hàng không) trước khi mọi báo cáo tiền tệ được xây. | 🔴 |
| G-2 | **Currency** | Đơn vị tiền tệ chưa được nêu rõ — xác nhận là USD hay khác. | 🔴 |
| G-3 | **Sales / Unit** | Một lần bán có thể gồm nhiều đơn vị không; "giao dịch/đơn hàng" được định nghĩa thế nào (hiện mỗi lần bán = 1 đơn vị). | 🟡 |
| G-4 | **Budget / Forecast** | Phân biệt nghiệp vụ Budget vs Forecast; kỳ/phạm vi so sánh với Actual (hai bên không trùng kỳ đầy đủ); cách xử lý nhóm sản phẩm có doanh số nhưng không có dòng kế hoạch. | 🔴 |
| G-5 | **Category / Segment** | Định nghĩa kinh doanh của từng Category/Segment; quan hệ phân cấp có cố định không. | 🟡 |
| G-6 | **Geography / Store** | Có thực thể **cửa hàng/chi nhánh** không, hay địa lý chỉ theo khách hàng; mô hình bán hàng (online/đại lý/cửa hàng). | 🟡 |
| G-7 | **District** | District là địa giới hành chính, vùng bán hàng, hay đơn vị tổ chức nội bộ. | 🟢 |
| G-8 | **Campaign** | Quan hệ Campaign–Sales là *attribution* (doanh số đến từ chiến dịch) hay chỉ là chiến dịch đang chạy lúc bán. | 🟡 |
| G-9 | **Manufacturer** | Có ý định đưa sản phẩm đối thủ vào để phân tích thị phần không (hiện chỉ có VanArsdel). | 🟢 |
| G-10 | **Date / Calendar** | Dùng năm dương lịch hay năm tài chính; định nghĩa "Quý". | 🟢 |
| G-11 | **Customer** | Ràng buộc bảo mật thông tin cá nhân (PII) khi hiển thị khách hàng trên dashboard. | 🟢 |

## Ưu tiên cao nhất cần chốt trước
1. **G-1 Revenue** + **G-2 Currency** — nền tảng mọi số liệu tiền tệ.
2. **G-4 Budget/Forecast vs Actual** — quyết định phạm vi thời gian & cách đo mức đạt mục tiêu của dashboard.
3. **G-3 Sales grain** — ảnh hưởng cách đếm số lượng/giao dịch.
