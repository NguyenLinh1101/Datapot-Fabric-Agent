# Business Glossary — VanArsdel

> **Bước 02 — Discovery.** Mục tiêu: chuẩn hóa ngôn ngữ nghiệp vụ để mọi stakeholder và Data Analyst hiểu cùng một khái niệm, trước khi xây Data Dictionary và Business Requirements.
>
> Phạm vi: chỉ định nghĩa khái niệm nghiệp vụ có **đủ căn cứ** từ dataset và bối cảnh. Không định nghĩa KPI/công thức (để ở BRD), không mô tả kỹ thuật cột (để ở Data Dictionary). Thuật ngữ chưa đủ căn cứ chỉ được **ghi nhận để xác nhận** (tổng hợp tại Data Gap).

## Bối cảnh ngắn
VanArsdel là công ty bán lẻ tại thị trường Mỹ, bán các sản phẩm do chính mình sản xuất, tới khách hàng trên nhiều vùng, thông qua các chiến dịch marketing. Glossary này phản ánh cách hiểu nghiệp vụ dựa trên dữ liệu bán hàng và ngân sách hiện có.

## Concept Map (toàn cảnh)
```
   VanArsdel (bán lẻ, sản phẩm của chính mình)
        │ bán hàng
   ┌────┴───────────────┬────────────────────┐
 SẢN PHẨM           KÊNH TIẾP CẬN          KHÁCH HÀNG
 Category            Campaign               Customer
   └ Segment           ├ Traffic Channel      └ Geography
       └ Product       └ Device                  Region → State → District → ZIP
        │
        ▼
   BÁN HÀNG (Sales / Actual)  →  Units Sold · Revenue
        │ đo theo Thời gian (Năm → Quý → Tháng → Ngày)
        ▼
   ACTUAL  ⇄  BUDGET  ⇄  FORECAST
```

---

## Sản phẩm

### Product
- **Business Definition:** Một mặt hàng cụ thể mà VanArsdel bán ra — đơn vị nhỏ nhất của "thứ được bán".
- **Business Context:** Là đối tượng trung tâm khi phân tích mặt hàng nào bán chạy, sinh lời; mọi giao dịch bán đều gắn với một sản phẩm.
- **Related Data:** `Product`; tham chiếu trong `Sales`.
- **Assumption / Note:** Sản phẩm được định danh bằng mã sản phẩm; tên sản phẩm có thể trùng giữa các mã — cách nhận diện trong báo cáo cần xác nhận.

### Category
- **Business Definition:** Cách phân loại sản phẩm ở **cấp cao nhất**, gom các dòng sản phẩm lớn lại với nhau.
- **Business Context:** Cấp tổng hợp để Ban lãnh đạo nhìn danh mục sản phẩm tổng thể.
- **Related Data:** `Product` (thuộc tính Category); là một chiều của `Budget`/`Forecast`.
- **Assumption / Note:** Ý nghĩa kinh doanh của từng Category cần stakeholder xác nhận.

### Segment
- **Business Definition:** Cách phân loại sản phẩm ở **cấp trung gian**, chi tiết hơn Category.
- **Business Context:** Mức chi tiết để phân tích hiệu quả từng dòng sản phẩm; cũng là mức mà kế hoạch được lập.
- **Related Data:** `Product` (thuộc tính Segment); là một chiều của `Budget`/`Forecast`.
- **Assumption / Note:** Quan hệ phân cấp **Category → Segment → Product** được giả định cố định (mỗi Segment thuộc một Category) — cần xác nhận.

> **Category vs Segment:** Category là nhóm lớn (cấp cha), Segment là phân khúc chi tiết hơn nằm trong Category. Một Category gồm nhiều Segment; một Segment gồm nhiều Product. Kế hoạch ngân sách được lập ở mức tổ hợp **Category–Segment**.

### Manufacturer
- **Business Definition:** Đơn vị sản xuất ra sản phẩm.
- **Business Context:** Dùng để phân biệt sản phẩm "của mình" với sản phẩm "đối thủ".
- **Related Data:** `Product` (thuộc tính Manufacturer).
- **Assumption / Note:** Trong dữ liệu, mọi sản phẩm đều do **VanArsdel** sản xuất → dữ liệu hiện **chỉ phản ánh hàng của chính VanArsdel, không có sản phẩm đối thủ**. Việc có đưa sản phẩm đối thủ vào phân tích thị phần hay không cần xác nhận.

> **Manufacturer vs Product:** Manufacturer là *ai sản xuất* (hiện chỉ VanArsdel); Product là *mặt hàng cụ thể*. Vì chỉ có một nhà sản xuất, phân tích hiện tại là về kết quả bán hàng của chính VanArsdel.

---

## Khách hàng & Địa lý

### Customer
- **Business Definition:** Cá nhân mua hàng của VanArsdel.
- **Business Context:** Đối tượng tạo ra doanh số; cơ sở phân tích độ rộng tập khách và hành vi mua theo địa lý.
- **Related Data:** `Customer`; tham chiếu trong `Sales`.
- **Assumption / Note:** Cần xác nhận thông tin khách hàng có ràng buộc bảo mật (PII) khi hiển thị trên dashboard không.

### Geography
- **Business Definition:** Vị trí địa lý gắn với khách hàng, theo phân cấp **Region → State (Bang) → District → City → ZIP**, toàn bộ tại Mỹ.
- **Business Context:** Cho phép phân tích doanh số theo địa bàn (vùng/bang/khu vực).
- **Related Data:** `Geo`; liên kết với `Customer` qua mã ZIP.
- **Assumption / Note:** Địa lý gắn theo **vị trí khách hàng** (không có thực thể cửa hàng/chi nhánh). Mô hình bán hàng (online/đại lý/cửa hàng) cần xác nhận.

### Region
- **Business Definition:** Cấp địa lý cao nhất, gom nhiều bang thành các vùng lớn theo miền (Đông/Trung/Tây).
- **Business Context:** Mức tổng hợp để lãnh đạo nhìn doanh số theo miền.
- **Related Data:** `Geo` (thuộc tính Region).
- **Assumption / Note:** —

### District
- **Business Definition:** Một khu vực địa bàn được đánh số — cách VanArsdel chia địa bàn để quản lý.
- **Business Context:** Có thể là đơn vị quản lý kinh doanh theo khu vực.
- **Related Data:** `Geo` (thuộc tính District).
- **Assumption / Note:** Chưa rõ District là địa giới hành chính, vùng bán hàng (sales territory), hay đơn vị tổ chức nội bộ — cần xác nhận.

---

## Marketing

### Campaign
- **Business Definition:** Một hoạt động/chiến dịch marketing mà VanArsdel triển khai để thúc đẩy bán hàng.
- **Business Context:** Gắn doanh số với nỗ lực marketing; trả lời "doanh số đến từ chiến dịch nào".
- **Related Data:** `Campaign`; tham chiếu trong `Sales`.
- **Assumption / Note:** Mỗi chiến dịch được mô tả bằng một **Traffic Channel** và một **Device**. Quan hệ Campaign–Sales là *attribution* (doanh số đến từ chiến dịch) hay chỉ là chiến dịch đang chạy lúc bán — cần xác nhận.

### Traffic Channel
- **Business Definition:** Kênh mà khách tiếp cận VanArsdel — gồm cả kênh **số** (tìm kiếm, email, quảng cáo hiển thị…) và kênh **ngoại tuyến** (thư tín).
- **Business Context:** Đánh giá hiệu quả từng kênh tiếp cận trong việc tạo doanh số.
- **Related Data:** `Campaign` (thuộc tính TrafficChannel).
- **Assumption / Note:** Danh mục kênh chuẩn (tên gọi & cách gom nhóm) cần stakeholder xác nhận để báo cáo nhất quán.

### Device
- **Business Definition:** Thiết bị hoặc hình thức khách dùng khi tương tác với chiến dịch (máy tính, điện thoại, máy tính bảng, hoặc hình thức ngoại tuyến).
- **Business Context:** Hiểu khách tương tác qua nền tảng nào; hỗ trợ quyết định đầu tư trải nghiệm theo thiết bị.
- **Related Data:** `Campaign` (thuộc tính Device).
- **Assumption / Note:** Cách phân loại online/offline và danh mục thiết bị chuẩn cần xác nhận.

> **Campaign ánh xạ marketing như thế nào:** Campaign là *hoạt động marketing*; mỗi Campaign cho biết nó chạy qua **Traffic Channel** nào (kênh tiếp cận) và trên **Device** nào (nền tảng). Đây là ba cấp mô tả nỗ lực marketing gắn với doanh số.

---

## Bán hàng & Kết quả

### Sales
- **Business Definition:** Sự kiện bán một sản phẩm cho một khách hàng — hoạt động cốt lõi tạo doanh thu.
- **Business Context:** Là kết quả thực tế (Actual) của hoạt động kinh doanh, đặt cạnh kế hoạch để đánh giá.
- **Related Data:** `Sales` (kết nối Product, Customer, Campaign, Date).
- **Assumption / Note:** Mỗi lần bán hiện ứng với một đơn vị sản phẩm; việc một lần bán có thể nhiều đơn vị hay không cần xác nhận.

### Units Sold
- **Business Definition:** Số đơn vị sản phẩm bán ra — thước đo *số lượng* (bán bao nhiêu cái), khác với thước đo *giá trị* (doanh thu).
- **Business Context:** Phản ánh sản lượng tiêu thụ.
- **Related Data:** `Sales`.
- **Assumption / Note:** —

### Revenue
- **Business Definition:** Doanh thu — tổng giá trị tiền VanArsdel thu được từ bán hàng. Là khái niệm tài chính trung tâm mà dashboard điều hành hướng tới.
- **Business Context:** Thước đo kết quả kinh doanh quan trọng nhất; cơ sở để so với Budget và Forecast.
- **Related Data:** Hình thành từ `Sales` kết hợp giá bán trong `Product`.
- **Assumption / Note:** **Doanh thu không tồn tại sẵn như một giá trị trong dữ liệu** — cần stakeholder chốt **định nghĩa doanh thu** (gross hay net; có trừ thuế/chiết khấu/trả hàng không). Công thức tính sẽ được đặc tả ở BRD, không nêu tại đây.

### Currency
- **Business Definition:** Đơn vị tiền tệ của mọi giá trị tài chính (giá, doanh thu, ngân sách).
- **Business Context:** Đảm bảo mọi con số tiền được hiểu và hiển thị nhất quán.
- **Related Data:** Áp dụng cho giá trị tiền trong `Product` (giá), `Budget`/`Forecast` (kế hoạch).
- **Assumption / Note:** Dữ liệu không ghi rõ tiền tệ; nhiều khả năng là **USD** (thị trường Mỹ) nhưng **chưa được xác nhận**.

---

## Thực tế & Kế hoạch

### Actual
- **Business Definition:** Kết quả kinh doanh **đã thực sự xảy ra** — doanh số bán hàng thực tế.
- **Business Context:** Là "sự thật" để đo lường hiệu quả; mọi đánh giá mức đạt mục tiêu đều lấy Actual đặt cạnh kế hoạch.
- **Related Data:** `Sales`.
- **Assumption / Note:** Kỳ thời gian của Actual và của kế hoạch không trùng khớp đầy đủ — cần xác nhận kỳ so sánh.

### Budget
- **Business Definition:** Ngân sách / mục tiêu kế hoạch doanh thu VanArsdel đặt ra, theo từng tháng và từng nhóm sản phẩm (Category–Segment).
- **Business Context:** "Thước đo kỳ vọng" để đối chiếu kết quả thực tế; nền tảng đánh giá mức đạt mục tiêu.
- **Related Data:** `Budget_Forecast` (các dòng `Type = Budget`).
- **Assumption / Note:** Budget hiện chỉ có cho một số năm gần đây → phạm vi so sánh với thực tế bị giới hạn; cần xác nhận kỳ so sánh.

### Forecast
- **Business Definition:** Dự báo doanh thu — mức VanArsdel kỳ vọng đạt được, theo tháng và nhóm sản phẩm, có thể cập nhật trong năm.
- **Business Context:** Phản ánh kỳ vọng cập nhật, khác với mục tiêu cố định là Budget.
- **Related Data:** `Budget_Forecast` (các dòng `Type = Forecast`).
- **Assumption / Note:** Cần phân biệt rõ nghiệp vụ giữa Budget (mục tiêu cố định) và Forecast (dự báo cập nhật).

---

## Thời gian

### Date / Time
- **Business Definition:** Trục thời gian của hoạt động kinh doanh, theo phân cấp Năm → Quý → Tháng → Ngày.
- **Business Context:** Cho phép theo dõi xu hướng và so sánh theo kỳ; là trục cốt lõi của mọi dashboard điều hành.
- **Related Data:** `Date`; liên kết với `Sales` (thời điểm bán) và `Budget`/`Forecast` (kỳ kế hoạch theo tháng).
- **Assumption / Note:** Lịch dùng năm dương lịch hay năm tài chính, và cách hiểu "Quý", cần xác nhận.

---

> Các điểm **Assumption / Note** ở trên là những nội dung cần stakeholder xác nhận; chúng sẽ được tổng hợp tại **Data Gap** (bước 04). Tài liệu này không định nghĩa KPI, công thức hay cấu trúc kỹ thuật của cột.
