# Business Glossary — VanArsdel

> Vai trò: **Senior Business Analyst** · Ngày: 2026-06-27
> Mục đích: giải thích các **khái niệm nghiệp vụ** của VanArsdel bằng ngôn ngữ mà business stakeholder hiểu được — không mô tả cột dữ liệu, không phải Data Dictionary, không định nghĩa KPI.
> Nguyên tắc **Evidence-first**: ý nghĩa được suy ra từ dữ liệu thực tế và tài liệu Bước 1. Khái niệm chưa đủ dữ liệu để kết luận được ghi ở mục **Data Gap** (cuối tài liệu), không suy diễn.

## Cách đọc mỗi mục
Mỗi khái niệm gồm: **Ý nghĩa nghiệp vụ** · **Vai trò trong kinh doanh** · **Quan hệ với khái niệm khác** · **Điểm chưa rõ (hỏi stakeholder)**.

## Danh mục khái niệm
**Doanh nghiệp & sản phẩm:** [VanArsdel](#vanarsdel) · [Product](#product) · [Category](#category) · [Segment](#segment) · [Manufacturer](#manufacturer) · [Unit Price](#unit-price) · [Unit Cost](#unit-cost)
**Khách hàng & địa lý:** [Customer](#customer) · [Geography](#geography) · [Region](#region) · [District](#district)
**Marketing:** [Campaign](#campaign) · [Traffic Channel](#traffic-channel) · [Device](#device)
**Bán hàng:** [Sales](#sales) · [Unit / Units Sold](#unit--units-sold) · [Revenue](#revenue) · [Currency](#currency)
**Thực tế & Kế hoạch:** [Actual](#actual) · [Budget](#budget) · [Forecast](#forecast)
**Thời gian:** [Date / Calendar](#date--calendar)

---

## Doanh nghiệp & sản phẩm

### VanArsdel
- **Ý nghĩa nghiệp vụ:** Doanh nghiệp bán lẻ — chủ thể của toàn bộ dữ liệu. Bán các sản phẩm **do chính mình sản xuất** tại thị trường Mỹ.
- **Vai trò trong kinh doanh:** Là "chúng tôi" trong mọi phân tích; mọi doanh số, kế hoạch, chiến dịch đều thuộc về VanArsdel.
- **Quan hệ:** Là [Manufacturer](#manufacturer) duy nhất xuất hiện trong dữ liệu; sở hữu mọi [Product](#product), [Campaign](#campaign), [Budget](#budget)/[Forecast](#forecast).
- **Điểm chưa rõ:** Không có. (Toàn bộ dữ liệu xoay quanh một doanh nghiệp.)

### Product
- **Ý nghĩa nghiệp vụ:** Một mặt hàng cụ thể mà VanArsdel bán ra. Đây là đơn vị nhỏ nhất của "thứ được bán".
- **Vai trò trong kinh doanh:** Đối tượng trung tâm của doanh số — mọi giao dịch bán đều gắn với một sản phẩm; là cơ sở để phân tích mặt hàng nào bán chạy, sinh lời.
- **Quan hệ:** Mỗi sản phẩm thuộc một [Segment](#segment) và một [Category](#category); được sản xuất bởi [Manufacturer](#manufacturer) (VanArsdel); có [Unit Price](#unit-price) và [Unit Cost](#unit-cost); xuất hiện trong các giao dịch [Sales](#sales).
- **Điểm chưa rõ:** Nhiều sản phẩm khác nhau có thể trùng **tên** — cần xác nhận tên sản phẩm có ý nghĩa nhận diện riêng với business hay chỉ là nhãn kỹ thuật.

### Category
- **Ý nghĩa nghiệp vụ:** Cách phân loại sản phẩm ở **cấp cao nhất** theo nhóm lớn (ví dụ các nhóm như Urban, Rural, Youth, Mix, Accessory).
- **Vai trò trong kinh doanh:** Giúp lãnh đạo nhìn bức tranh tổng thể theo dòng sản phẩm lớn; là cấp tổng hợp tự nhiên khi báo cáo cho ban điều hành.
- **Quan hệ:** Là cấp **cha** của [Segment](#segment); một Category gồm nhiều Segment, mỗi Segment gồm nhiều [Product](#product). [Budget](#budget)/[Forecast](#forecast) được lập ở mức tổ hợp **Category–Segment**.
- **Điểm chưa rõ:** Cần stakeholder xác nhận **định nghĩa kinh doanh** của từng Category (ví dụ "Urban", "Rural", "Youth" phản ánh phân khúc thị trường, kiểu sản phẩm, hay nhóm khách hàng mục tiêu?).

### Segment
- **Ý nghĩa nghiệp vụ:** Phân loại sản phẩm ở **cấp trung gian**, chi tiết hơn Category (ví dụ Moderation, Convenience, Regular, Extreme, Select, Productivity, All Season…).
- **Vai trò trong kinh doanh:** Mức chi tiết để phân tích hiệu quả từng dòng sản phẩm; cũng là mức mà kế hoạch ([Budget](#budget)) được lập.
- **Quan hệ:** Nằm **giữa** [Category](#category) và [Product](#product) trong phân cấp sản phẩm. Tổ hợp **Category–Segment** là đơn vị để so sánh thực tế với kế hoạch.
- **Điểm chưa rõ:** Quan hệ Category–Segment cần xác nhận là **phân cấp cố định** (mỗi Segment chỉ thuộc một Category) hay linh hoạt; và ý nghĩa kinh doanh của từng Segment. → xem **Data Gap**.

### Manufacturer
- **Ý nghĩa nghiệp vụ:** Đơn vị sản xuất ra sản phẩm. Trong dữ liệu hiện tại **chỉ có một nhà sản xuất: VanArsdel**.
- **Vai trò trong kinh doanh:** Phân biệt sản phẩm "của mình" với sản phẩm "đối thủ". Vì chỉ có VanArsdel, **dữ liệu hiện tại chỉ phản ánh doanh số sản phẩm của chính VanArsdel**, không có sản phẩm cạnh tranh.
- **Quan hệ:** Gắn với mọi [Product](#product); đồng nhất với [VanArsdel](#vanarsdel).
- **Điểm chưa rõ:** Cần xác nhận liệu sau này có ý định đưa **sản phẩm đối thủ** vào để phân tích thị phần hay không (hiện tại không thể).

### Unit Price
- **Ý nghĩa nghiệp vụ:** Giá bán của **một đơn vị** sản phẩm — số tiền khách trả cho mỗi đơn vị.
- **Vai trò trong kinh doanh:** Cơ sở để xác định doanh thu; phản ánh định giá của từng sản phẩm.
- **Quan hệ:** Gắn với từng [Product](#product); là thành phần hình thành [Revenue](#revenue); đặt cạnh [Unit Cost](#unit-cost) để thể hiện phần chênh lệch giữa giá bán và giá vốn của sản phẩm.
- **Điểm chưa rõ:** Cần xác nhận đây là giá **niêm yết** hay giá **thực thu** (đã trừ khuyến mãi/chiết khấu?); và đơn vị [Currency](#currency).

### Unit Cost
- **Ý nghĩa nghiệp vụ:** Giá vốn của **một đơn vị** sản phẩm — chi phí VanArsdel bỏ ra cho mỗi đơn vị.
- **Vai trò trong kinh doanh:** Cơ sở để đánh giá lợi nhuận/biên lợi nhuận theo sản phẩm.
- **Quan hệ:** Thuộc tính của [Product](#product); so với [Unit Price](#unit-price) cho ra biên lợi nhuận đơn vị.
- **Điểm chưa rõ:** Cần xác nhận phạm vi chi phí (chỉ giá vốn hàng hóa hay gồm cả chi phí khác); đơn vị [Currency](#currency).

---

## Khách hàng & địa lý

### Customer
- **Ý nghĩa nghiệp vụ:** Cá nhân mua hàng của VanArsdel. Tập khách hàng rất lớn (hàng trăm nghìn).
- **Vai trò trong kinh doanh:** Đối tượng tạo ra doanh số; cơ sở để phân tích độ rộng tập khách, hành vi mua theo địa lý.
- **Quan hệ:** Gắn với [Geography](#geography) qua vị trí (mã ZIP) của khách; xuất hiện trong [Sales](#sales).
- **Điểm chưa rõ:** Cần xác nhận thông tin khách hàng là **dữ liệu thật hay dữ liệu mẫu/ẩn danh**, và có ràng buộc bảo mật thông tin cá nhân (PII) nào khi đưa lên dashboard điều hành không.

### Geography
- **Ý nghĩa nghiệp vụ:** Vị trí địa lý gắn với khách hàng, theo phân cấp **ZIP → Thành phố → District → Bang → Vùng (Region)**, toàn bộ tại Mỹ.
- **Vai trò trong kinh doanh:** Cho phép phân tích doanh số **theo địa bàn**; trả lời "vùng/bang nào bán tốt".
- **Quan hệ:** Đính kèm [Customer](#customer) (địa lý theo **vị trí khách hàng**, không phải cửa hàng); chứa các cấp [Region](#region) và [District](#district).
- **Điểm chưa rõ:** **Không có thực thể cửa hàng/chi nhánh** trong dữ liệu — cần xác nhận VanArsdel bán online, qua đại lý, hay có cửa hàng vật lý mà dữ liệu chưa cung cấp. → xem **Data Gap**.

### Region
- **Ý nghĩa nghiệp vụ:** Cấp địa lý **cao nhất**, gom nhiều bang thành các vùng lớn (East / Central / West).
- **Vai trò trong kinh doanh:** Mức tổng hợp để lãnh đạo nhìn doanh số theo miền; thường là cấp đầu tiên khi "khoan sâu" địa lý.
- **Quan hệ:** Cấp cha trong [Geography](#geography), trên Bang và [District](#district).
- **Điểm chưa rõ:** Không có điểm cần làm rõ ở cấp khái niệm — cách chia East/Central/West đã rõ ràng.

### District
- **Ý nghĩa nghiệp vụ:** Một đơn vị địa lý/khu vực được đánh số (District #01…#39). Có vẻ là cách VanArsdel **chia địa bàn** để quản lý.
- **Vai trò trong kinh doanh:** Có thể là đơn vị quản lý kinh doanh theo khu vực; mức trung gian giữa Bang/Vùng.
- **Quan hệ:** Một cấp trong [Geography](#geography).
- **Điểm chưa rõ:** Chưa rõ District là **địa giới hành chính**, **vùng bán hàng (sales territory)**, hay đơn vị tổ chức nội bộ; quan hệ chính xác giữa District với Bang/Region cần xác nhận. → xem **Data Gap**.

---

## Marketing

### Campaign
- **Ý nghĩa nghiệp vụ:** Một hoạt động/chiến dịch marketing mà VanArsdel triển khai để thúc đẩy bán hàng.
- **Vai trò trong kinh doanh:** Gắn doanh số với nỗ lực marketing; trả lời "doanh số đến từ chiến dịch nào".
- **Quan hệ:** Mỗi giao dịch [Sales](#sales) gắn với một Campaign; mỗi Campaign mô tả bằng [Traffic Channel](#traffic-channel) và [Device](#device).
- **Điểm chưa rõ:** Cần xác nhận ngữ nghĩa quan hệ: gắn campaign vào một giao dịch nghĩa là giao dịch đó **đến từ** chiến dịch (attribution), hay chỉ là chiến dịch **đang chạy** tại thời điểm bán? → xem **Data Gap**.

### Traffic Channel
- **Ý nghĩa nghiệp vụ:** **Kênh** mà khách tiếp cận/đến với VanArsdel (ví dụ Organic Search, SEO, SEM, Email, Banner, Affiliate, SMO, Mail).
- **Vai trò trong kinh doanh:** Đánh giá hiệu quả từng kênh tiếp cận trong việc tạo ra doanh số.
- **Quan hệ:** Thuộc tính mô tả [Campaign](#campaign). Gồm cả kênh **số** (search, email, banner…) lẫn kênh **ngoại tuyến** (Mail).
- **Điểm chưa rõ:** Cần stakeholder xác nhận **danh mục kênh chuẩn** của VanArsdel — tên gọi chính thức và cách gom nhóm các kênh — để báo cáo nhất quán.

### Device
- **Ý nghĩa nghiệp vụ:** **Thiết bị** mà khách dùng khi tương tác với chiến dịch (Desktop, Mobile, Tablet) hoặc hình thức tiếp cận ngoại tuyến (Paper).
- **Vai trò trong kinh doanh:** Hiểu khách tương tác qua nền tảng nào; hỗ trợ quyết định đầu tư trải nghiệm theo thiết bị.
- **Quan hệ:** Thuộc tính mô tả [Campaign](#campaign), đi cùng [Traffic Channel](#traffic-channel).
- **Điểm chưa rõ:** "Paper" là thiết bị hay là hình thức (gắn với kênh Mail)? Cần xác nhận **danh mục thiết bị chuẩn** và cách phân loại online/offline.

---

## Bán hàng

### Sales
- **Ý nghĩa nghiệp vụ:** Sự kiện **bán một sản phẩm cho một khách hàng** — hoạt động cốt lõi tạo doanh thu. Mỗi bản ghi bán phản ánh một lần bán đơn vị sản phẩm.
- **Vai trò trong kinh doanh:** Là "kết quả thực tế" (Actual) của hoạt động kinh doanh, được đặt cạnh kế hoạch để đánh giá.
- **Quan hệ:** Kết nối [Product](#product), [Customer](#customer), [Campaign](#campaign) và [Date](#date--calendar); là nguồn để hình thành [Revenue](#revenue) và [Units Sold](#unit--units-sold); đối chiếu với [Budget](#budget)/[Forecast](#forecast).
- **Điểm chưa rõ:** Mỗi bản ghi bán hiện luôn ứng với **một đơn vị**; cần xác nhận một lần bán có thể gồm **nhiều đơn vị** hay không, và "giao dịch/đơn hàng" được hiểu thế nào. → xem **Data Gap**.

### Unit / Units Sold
- **Ý nghĩa nghiệp vụ:** **Đơn vị sản phẩm** bán ra — thước đo *số lượng* (bán được bao nhiêu cái), khác với thước đo *giá trị* (doanh thu).
- **Vai trò trong kinh doanh:** Phản ánh sản lượng tiêu thụ; nền tảng để tính doanh thu khi nhân với giá.
- **Quan hệ:** Phát sinh từ [Sales](#sales); kết hợp [Unit Price](#unit-price) để ra [Revenue](#revenue).
- **Điểm chưa rõ:** Vì mỗi bản ghi luôn là 1 đơn vị, cần xác nhận liệu "số lượng" có bao giờ lớn hơn 1 trên một lần bán không. → xem **Data Gap**.

### Revenue
- **Ý nghĩa nghiệp vụ:** **Doanh thu** — tổng giá trị tiền VanArsdel thu được từ bán hàng. Đây là khái niệm tài chính trung tâm mà dashboard điều hành nhắm tới.
- **Vai trò trong kinh doanh:** Thước đo kết quả kinh doanh quan trọng nhất; cơ sở để so với [Budget](#budget)/[Forecast](#forecast).
- **Quan hệ:** Hình thành từ hoạt động [Sales](#sales) (số lượng bán × giá bán); được lập kế hoạch qua [Budget](#budget) và [Forecast](#forecast); thể hiện bằng [Currency](#currency).
- **Điểm chưa rõ:** **Doanh thu không tồn tại sẵn như một giá trị trong dữ liệu** — nó là khái niệm phải được *xác định lại* từ hoạt động bán. Cần stakeholder chốt **định nghĩa doanh thu** (gross hay net, có trừ thuế/chiết khấu/trả hàng không). → xem **Data Gap** (đây là điểm quan trọng nhất).

### Currency
- **Ý nghĩa nghiệp vụ:** **Đơn vị tiền tệ** của mọi giá trị tài chính (giá, doanh thu, ngân sách).
- **Vai trò trong kinh doanh:** Đảm bảo mọi con số tiền được hiểu và hiển thị nhất quán.
- **Quan hệ:** Áp dụng cho [Unit Price](#unit-price), [Unit Cost](#unit-cost), [Revenue](#revenue), [Budget](#budget), [Forecast](#forecast).
- **Điểm chưa rõ:** Dữ liệu **không ghi rõ tiền tệ**; thị trường là Mỹ nên nhiều khả năng là USD nhưng **chưa được xác nhận**. → xem **Data Gap**.

---

## Thực tế & Kế hoạch

### Actual
- **Ý nghĩa nghiệp vụ:** Kết quả kinh doanh **đã thực sự xảy ra** — doanh số bán hàng thực tế của VanArsdel, đối lập với con số kế hoạch hay dự báo. (Trong dữ liệu, đây chính là phần "Actuals".)
- **Vai trò trong kinh doanh:** Là "sự thật" để đo lường hiệu quả; mọi đánh giá mức độ đạt mục tiêu đều lấy Actual đặt cạnh kế hoạch.
- **Quan hệ:** Hình thành từ hoạt động [Sales](#sales) và [Revenue](#revenue); được đối chiếu với [Budget](#budget) và [Forecast](#forecast).
- **Điểm chưa rõ:** Kỳ thời gian của Actual và của kế hoạch **không trùng khớp đầy đủ**, nên cần xác nhận kỳ so sánh chính thức trước khi đánh giá đạt mục tiêu. → xem **Data Gap (G-4)**.

### Budget
- **Ý nghĩa nghiệp vụ:** **Ngân sách / mục tiêu kế hoạch** doanh thu mà VanArsdel đặt ra, theo từng tháng và từng nhóm sản phẩm (Category–Segment).
- **Vai trò trong kinh doanh:** Là "thước đo kỳ vọng" để đối chiếu kết quả thực tế; nền tảng cho việc đánh giá mức độ đạt mục tiêu.
- **Quan hệ:** Đặt cạnh kết quả [Actual](#actual) ([Sales](#sales)/[Revenue](#revenue)) ở mức [Category](#category)–[Segment](#segment) theo tháng; cùng họ với [Forecast](#forecast).
- **Điểm chưa rõ:** Budget chỉ tồn tại cho **một số năm** (không phủ toàn bộ kỳ có doanh số thực tế), nên việc so sánh Actual–Budget bị giới hạn về thời gian. Cần xác nhận kỳ và phạm vi so sánh. → xem **Data Gap**.

### Forecast
- **Ý nghĩa nghiệp vụ:** **Dự báo** doanh thu — ước tính VanArsdel kỳ vọng đạt được, theo tháng và nhóm sản phẩm, có thể cập nhật trong năm.
- **Vai trò trong kinh doanh:** Phản ánh kỳ vọng cập nhật (khác với mục tiêu cố định là Budget); hỗ trợ điều chỉnh kế hoạch.
- **Quan hệ:** Cùng cấu trúc và đặt cạnh [Budget](#budget); đối chiếu với kết quả [Actual](#actual) ([Sales](#sales)/[Revenue](#revenue)).
- **Điểm chưa rõ:** Cần phân biệt rõ ý nghĩa nghiệp vụ **Budget vs Forecast** (mục tiêu cứng vs dự báo mềm?) và quy trình lập từng loại; ngoài ra Forecast chỉ có cho kỳ chưa có doanh số thực tế nên khó kiểm chứng độ chính xác. → xem **Data Gap**.

---

## Thời gian

### Date / Calendar
- **Ý nghĩa nghiệp vụ:** **Trục thời gian** của hoạt động kinh doanh, theo phân cấp Năm → Quý → Tháng → Ngày.
- **Vai trò trong kinh doanh:** Cho phép theo dõi xu hướng, so sánh kỳ (tháng này vs trước, năm nay vs năm trước); là trục cốt lõi của mọi dashboard điều hành.
- **Quan hệ:** Gắn với [Sales](#sales) (thời điểm bán) và [Budget](#budget)/[Forecast](#forecast) (kỳ kế hoạch theo tháng).
- **Điểm chưa rõ:** Lịch kinh doanh dùng **năm dương lịch** hay **năm tài chính** (fiscal year)? Khái niệm "Quý" hiểu theo lịch nào? Ngoài ra phạm vi thời gian của thực tế và của kế hoạch **không trùng nhau** (chi tiết ở data-investigation/data-quality). → xem **Data Gap**.

---

## Data Gap — khái niệm chưa đủ dữ liệu/làm rõ để kết luận

> Các điểm dưới đây **chưa kết luận**; cần stakeholder làm rõ trước khi cố định ngữ nghĩa nghiệp vụ. (Vấn đề kỹ thuật/chất lượng dữ liệu nằm ở [data-quality-report.md](data-quality-report.md); câu hỏi tổng hợp ở [data-investigation.md](data-investigation.md) mục 6.)

| # | Khái niệm | Điều chưa rõ cần stakeholder làm rõ |
|---|---|---|
| G-1 | **Revenue** | Doanh thu không có sẵn; cần định nghĩa chính thức (gross/net; có trừ thuế, chiết khấu, trả hàng không) trước khi mọi báo cáo tiền tệ được xây. |
| G-2 | **Currency** | Tiền tệ không được ghi trong dữ liệu — xác nhận là USD hay khác. |
| G-3 | **Sales / Unit** | Một lần bán có thể gồm nhiều đơn vị không; "giao dịch/đơn hàng" được định nghĩa thế nào (hiện mỗi bản ghi = 1 đơn vị). |
| G-4 | **Budget / Forecast** | Phân biệt nghiệp vụ Budget vs Forecast; phạm vi/kỳ so sánh với Actual (hai bên không trùng kỳ đầy đủ); cách xử lý nhóm sản phẩm có doanh số nhưng không có dòng kế hoạch. |
| G-5 | **Category / Segment** | Định nghĩa kinh doanh của từng Category/Segment; quan hệ phân cấp có cố định không. |
| G-6 | **Geography / Store** | Có thực thể **cửa hàng/chi nhánh** không, hay địa lý chỉ theo khách hàng; mô hình bán hàng (online/đại lý/cửa hàng). |
| G-7 | **District** | District là địa giới hành chính, vùng bán hàng, hay đơn vị tổ chức nội bộ. |
| G-8 | **Campaign** | Quan hệ Campaign–Sales là *attribution* (doanh số đến từ chiến dịch) hay chỉ là chiến dịch đang chạy lúc bán. |
| G-9 | **Manufacturer** | Có ý định đưa sản phẩm đối thủ vào để phân tích thị phần không (hiện chỉ có VanArsdel). |
| G-10 | **Date / Calendar** | Dùng năm dương lịch hay năm tài chính; định nghĩa Quý. |
| G-11 | **Customer** | Dữ liệu khách là mẫu/ẩn danh; ràng buộc PII nếu có. |

---
*Tài liệu này chỉ định nghĩa khái niệm nghiệp vụ. Không mô tả field, không Data Dictionary, không KPI, không BRD, không Report Spec.*

---

# Review Summary

*Đợt review của Senior Business Analyst, 2026-06-27 — chỉnh sửa trực tiếp trên file, không viết lại từ đầu.*

## Những gì đã chỉnh
- **Bỏ ngôn ngữ kỹ thuật/DA còn sót** (yêu cầu 1 & 2):
  - *Traffic Channel*: bỏ ghi chú "biến thể không nhất quán (khoảng trắng/chính tả)" — đó là góc nhìn chất lượng dữ liệu; chuyển thành nhu cầu nghiệp vụ "xác nhận danh mục kênh chuẩn".
  - *Unit Price*: bỏ phát biểu mang tính profiling ("cao hơn Unit Cost ở mọi sản phẩm (biên dương)") → diễn đạt theo nghiệp vụ là phần chênh lệch giá bán so với giá vốn.
  - *Customer*: bỏ chi tiết kỹ thuật email `@xyza.com` → giữ câu hỏi nghiệp vụ về dữ liệu thật/ẩn danh & PII.
  - *Region*: bỏ cụm "theo dữ liệu" trong phần Điểm chưa rõ.
- **Bổ sung khái niệm còn thiếu** (yêu cầu 7): thêm **Actual** — đối trọng nghiệp vụ của Budget/Forecast (bộ dữ liệu vốn gọi là "Actuals" nhưng chưa được đặt tên khái niệm). Đổi nhóm "Kế hoạch" thành "Thực tế & Kế hoạch".
- **Đảm bảo quan hệ nhất quán** (yêu cầu 4): nối Budget và Forecast về khái niệm Actual; bộ ba Actual–Budget–Forecast giờ tham chiếu chéo thống nhất.
- **Rà Business Role** (yêu cầu 3): xác nhận mỗi khái niệm đều nêu được "vì sao quan trọng với kinh doanh" — không phải chỉnh thêm.
- **Rà concept thừa** (yêu cầu 6): cân nhắc bỏ *VanArsdel* và *Manufacturer* (chỉ một giá trị). **Quyết định giữ**: *Manufacturer* trả lời câu hỏi nghiệp vụ "của mình hay đối thủ"; *VanArsdel* đặt khung "chúng tôi" cho toàn glossary. Không khái niệm nào bị loại.
- Không thêm KPI, Data Dictionary, hay Report Spec (yêu cầu 8–10).

## Những gì vẫn còn là giả định (chưa kết luận)
- **Revenue = giá trị bán hàng** nhưng *cách xác định chưa chốt* — vẫn để mở ở mục Revenue, không gán công thức.
- **Currency là USD** — suy đoán từ thị trường Mỹ, **chưa xác nhận**.
- **Geography phản ánh vị trí khách hàng** (không phải cửa hàng) — đúng theo dữ liệu hiện có, nhưng cần xác nhận mô hình bán hàng.
- **Mỗi lần bán = 1 đơn vị** — đúng với dữ liệu hiện tại, chưa rõ có ngoại lệ.
> Các giả định này được giữ nguyên dạng "cần xác nhận", không nâng thành sự thật trong nội dung khái niệm.

## Những điểm cần stakeholder xác nhận
Tổng hợp tại mục **Data Gap (G-1 → G-11)** ở trên. Ưu tiên cao nhất cho bước tiếp theo (Data Dictionary):
1. **G-1 Revenue** — định nghĩa doanh thu chính thức (gross/net, thuế/chiết khấu/trả hàng).
2. **G-2 Currency** — đơn vị tiền tệ.
3. **G-4 Actual vs Budget/Forecast** — kỳ so sánh và cách xử lý nhóm sản phẩm thiếu kế hoạch.
4. **G-5 Category/Segment** — định nghĩa kinh doanh & tính cố định của phân cấp.
5. **G-6/G-7 Geography/District/Store** — có cửa hàng không; District là loại đơn vị gì.
