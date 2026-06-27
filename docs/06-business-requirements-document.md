# Business Requirements Document (BRD) — VanArsdel

> Tài liệu yêu cầu nghiệp vụ cho **Executive Dashboard** của VanArsdel. Đối tượng đọc: CEO, Sales Director, Marketing Director, Finance Director.
> Thuật ngữ nghiệp vụ dùng trong tài liệu này được định nghĩa tại [03-business-glossary.md](03-business-glossary.md). Các điểm cần làm rõ tổng hợp tại [05-data-gap.md](05-data-gap.md).

---

## 1. Mục đích

VanArsdel cần một **Executive Dashboard** để Ban lãnh đạo theo dõi sức khỏe kinh doanh trên một màn hình duy nhất, thay vì tổng hợp thủ công từ nhiều nguồn.

Dashboard giúp Ban lãnh đạo theo dõi:
- **Kết quả bán hàng** (doanh thu, sản lượng) và **xu hướng theo thời gian**.
- **Mức độ đạt kế hoạch** — thực tế so với ngân sách (Budget) và dự báo (Forecast).
- **Đóng góp theo danh mục sản phẩm** (Category, Segment) — đâu là động lực tăng trưởng, đâu là điểm yếu.
- **Hiệu quả theo địa bàn** (Region, District) — vùng nào tăng trưởng tốt.
- **Hiệu quả marketing** — chiến dịch và kênh nào mang lại doanh thu.

Mục đích là **hỗ trợ ra quyết định điều hành**, không phải báo cáo tác nghiệp chi tiết.

---

## 2. Stakeholders & Decision Making

| Stakeholder | Dùng dashboard để | Quyết định kinh doanh cần đưa ra |
|---|---|---|
| **CEO** | Nhìn bức tranh tổng thể: doanh thu toàn công ty, mức đạt mục tiêu, xu hướng tăng trưởng | Định hướng chiến lược chung; ưu tiên nguồn lực giữa các dòng sản phẩm/vùng; đánh giá công ty có đi đúng kế hoạch không |
| **Sales Director** | Theo dõi doanh thu theo Region/District, theo Category/Segment, so với Budget | Phân bổ mục tiêu & nguồn lực bán hàng theo vùng; can thiệp vùng/nhóm đang hụt kế hoạch; đẩy mạnh nhóm tăng trưởng tốt |
| **Marketing Director** | Đánh giá hiệu quả Campaign, Traffic Channel, Device trong việc tạo doanh thu | Phân bổ ngân sách marketing vào kênh/chiến dịch hiệu quả; dừng/điều chỉnh chiến dịch kém hiệu quả |
| **Finance Director** | So sánh Actual với Budget và Forecast; theo dõi mức đạt kế hoạch theo tháng/nhóm | Điều chỉnh dự báo; kiểm soát mức đạt ngân sách; cảnh báo lệch kế hoạch sớm |

> Tài liệu chỉ mô tả nhu cầu ở góc nhìn nghiệp vụ; không quy định cách trình bày dashboard (thuộc bước Report Specification sau).

---

## 3. Business Questions

Executive Dashboard phải trả lời được các câu hỏi nghiệp vụ sau:

**Kết quả & xu hướng**
1. Doanh thu hiện tại đang tăng hay giảm so với kỳ trước?
2. Xu hướng doanh thu theo thời gian (tháng/quý/năm) ra sao?
3. Đã bán được bao nhiêu sản lượng (số đơn vị)?

**Kế hoạch**
4. Doanh thu thực tế có đạt Budget không, và đạt bao nhiêu phần trăm?
5. Thực tế đang cao hơn hay thấp hơn Forecast?
6. Nhóm sản phẩm hay vùng nào đang lệch kế hoạch nhiều nhất?

**Sản phẩm**
7. Category nào đóng góp doanh thu lớn nhất?
8. Segment nào đang hoạt động kém?

**Địa lý**
9. Region nào tăng trưởng tốt nhất / kém nhất?
10. District nào cần chú ý?

**Marketing**
11. Campaign nào hiệu quả nhất về doanh thu?
12. Traffic Channel nào mang lại nhiều doanh thu nhất?
13. Thiết bị (Device) nào gắn với doanh số tốt hơn?

Mỗi câu hỏi phục vụ một quyết định cụ thể của Ban lãnh đạo nêu ở Mục 2.

---

## 4. Business KPIs

> Công thức dưới đây viết ở **mức nghiệp vụ**, không phải DAX/SQL. Nhiều công thức phụ thuộc các điểm chưa được xác nhận (xem ghi chú và Mục 5).

### Revenue (Doanh thu)
- **Business Meaning:** Tổng giá trị tiền VanArsdel thu được từ bán hàng.
- **Business Formula:** Units Sold × Unit Price — **Business assumption, cần stakeholder xác nhận** (dữ liệu bán hàng không có sẵn cột doanh thu).
- **Data Source:** Sales + Product.
- **Business Notes:** Cần xác nhận Revenue là **Gross hay Net** (có trừ thuế/chiết khấu/trả hàng không) và đơn vị tiền tệ. Đây là KPI nền tảng — chốt định nghĩa trước khi build.

### Units Sold (Sản lượng bán)
- **Business Meaning:** Số đơn vị sản phẩm đã bán ra.
- **Business Formula:** Tổng số đơn vị bán ra trong kỳ.
- **Data Source:** Sales.
- **Business Notes:** Hiện mỗi lần bán ghi nhận 1 đơn vị — **cần xác nhận** một lần bán có thể nhiều hơn 1 đơn vị không.

### Budget (Ngân sách kế hoạch)
- **Business Meaning:** Mục tiêu doanh thu VanArsdel đặt ra theo tháng và theo nhóm sản phẩm.
- **Business Formula:** Giá trị kế hoạch có sẵn theo Category–Segment và theo tháng (không phải số tính ra).
- **Data Source:** Budget.
- **Business Notes:** Hiện chỉ có Budget cho một số năm gần đây — phạm vi so sánh với thực tế bị giới hạn (xem Mục 7).

### Forecast (Dự báo)
- **Business Meaning:** Mức doanh thu VanArsdel kỳ vọng đạt được, theo tháng và nhóm sản phẩm.
- **Business Formula:** Giá trị dự báo có sẵn theo Category–Segment và theo tháng.
- **Data Source:** Forecast.
- **Business Notes:** Cần phân biệt rõ Budget (mục tiêu cố định) và Forecast (kỳ vọng cập nhật).

### Budget Attainment % (Mức đạt ngân sách)
- **Business Meaning:** Thực tế đạt bao nhiêu phần trăm so với ngân sách kế hoạch.
- **Business Formula:** Actual Revenue ÷ Budget.
- **Data Source:** Sales + Product (Actual) và Budget.
- **Business Notes:** Chỉ tính được cho kỳ mà **Actual và Budget cùng tồn tại** — **cần stakeholder xác nhận** kỳ so sánh (xem Mục 7).

### Forecast Variance (Chênh lệch so dự báo)
- **Business Meaning:** Mức thực tế lệch so với dự báo (cao hơn hay thấp hơn).
- **Business Formula:** Actual Revenue − Forecast (và/hoặc tỷ lệ % so với Forecast).
- **Data Source:** Sales + Product (Actual) và Forecast.
- **Business Notes:** Chỉ có ý nghĩa khi kỳ Forecast có Actual tương ứng — hiện kỳ Forecast và kỳ Actual có thể không trùng (xem Mục 7).

### Actual vs Budget (Thực tế so kế hoạch)
- **Business Meaning:** So sánh doanh thu thực tế với ngân sách để thấy đang vượt hay hụt mục tiêu.
- **Business Formula:** Actual Revenue so với Budget (chênh lệch tuyệt đối và %).
- **Data Source:** Sales + Product (Actual) và Budget.
- **Business Notes:** Phụ thuộc định nghĩa Revenue và kỳ so sánh được xác nhận.

### Actual vs Forecast (Thực tế so dự báo)
- **Business Meaning:** So sánh doanh thu thực tế với dự báo để đánh giá độ chính xác kỳ vọng.
- **Business Formula:** Actual Revenue so với Forecast (chênh lệch tuyệt đối và %).
- **Data Source:** Sales + Product (Actual) và Forecast.
- **Business Notes:** Phụ thuộc việc có Actual cho kỳ được dự báo (xem Mục 7).

> Các KPI hiệu quả marketing/sản phẩm/địa lý (doanh thu theo Campaign, Channel, Device, Category, Segment, Region…) đều dựa trên **Revenue** ở trên, cắt theo chiều tương ứng — nên cũng phụ thuộc định nghĩa Revenue được chốt.

---

## 5. Business Assumptions

> Đây là các giả định **đang được sử dụng tạm thời** để tài liệu có thể tiến triển — **chưa phải sự thật**, cần stakeholder xác nhận.

1. **Revenue** được giả định tính từ **Units Sold × Unit Price** (doanh thu không có sẵn trong dữ liệu bán hàng).
2. **Currency** được giả định là **USD** (do thị trường là Mỹ).
3. **Geography** gắn với **Customer** (vị trí khách hàng), không có thực thể cửa hàng.
4. **Budget/Forecast** được lập ở mức **Category–Segment theo tháng**.
5. Mỗi **lần bán = một đơn vị** sản phẩm.
6. Giá trị **Budget/Forecast** là **doanh thu** kế hoạch/dự báo, cùng đơn vị với Revenue thực tế.

---

## 6. Scope

### In Scope
- Executive Dashboard cho Ban lãnh đạo.
- Phân tích doanh thu (Revenue) và sản lượng (Units Sold).
- Hiệu quả sản phẩm (Category, Segment).
- Hiệu quả marketing (Campaign, Traffic Channel, Device).
- Phân tích địa lý (Region, District).
- So sánh Actual vs Budget.
- So sánh Actual vs Forecast.
- Xu hướng theo thời gian.

### Out of Scope
- Báo cáo chi tiết từng giao dịch (transaction detail).
- Báo cáo tác nghiệp (operational reporting).
- Tồn kho (inventory) và chuỗi cung ứng (supply chain).
- Nhân sự (HR).
- Báo cáo tài chính kế toán (financial statements).
- Dự báo bằng AI / Machine Learning.
- Phân tích sản phẩm đối thủ / thị phần (dữ liệu chỉ gồm sản phẩm VanArsdel).

---

## 7. Open Business Questions

> Các điểm **ảnh hưởng trực tiếp** tới việc xây dựng Executive Dashboard, cần stakeholder xác nhận trước. Chi tiết & danh sách đầy đủ tại [05-data-gap.md](05-data-gap.md).

1. **Định nghĩa Revenue** — Gross hay Net; có trừ thuế/chiết khấu/trả hàng không? *(Chặn build — nền tảng mọi KPI tiền tệ.)*
2. **Currency** — đơn vị tiền tệ chính thức (USD?).
3. **Kỳ so sánh Actual vs Budget/Forecast** — thực tế và kế hoạch hiện **không trùng kỳ đầy đủ**; cần chốt phạm vi thời gian dashboard và cách xử lý nhóm sản phẩm có doanh số nhưng không có kế hoạch.
4. **Định nghĩa giao dịch & sản lượng** — một lần bán có thể gồm nhiều đơn vị không.
5. **Ý nghĩa Category/Segment và District** — để diễn giải đúng khi cắt theo sản phẩm và địa bàn.
6. **Ngữ nghĩa Campaign** — doanh số *đến từ* chiến dịch (attribution) hay chỉ là chiến dịch đang chạy lúc bán.

---
*Tài liệu chỉ tập trung yêu cầu nghiệp vụ. Không mô tả cấu trúc bảng/field, không Data Dictionary, không DAX/SQL, không thiết kế dashboard.*
