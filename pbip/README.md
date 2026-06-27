# VanArsdel Executive — Power BI Project (PBIP)

> **Bước 08 (Nice-to-have).** Kiểm chứng rằng bộ đặc tả (BRD + Report Spec + Wireframe) build được trên thực tế. Sinh theo hướng dẫn **Microsoft Skills for Fabric** (PBIP = TMDL Semantic Model + PBIR Report). Nguồn dữ liệu: `dataset/business-ready/`.

## Cách mở
1. Mở `VanArsdelExecutive.pbip` bằng **Power BI Desktop** (bật *Preview features → Power BI Project (.pbip)* và *Store reports using enhanced metadata / PBIR*).
2. Dữ liệu nối qua tham số **`DataFolder`** đang trỏ:
   `d:\Personal_Project\Datapot-Fabric-Agent\dataset\business-ready`
   Nếu di chuyển project, sửa `DataFolder` trong `...SemanticModel/definition/expressions.tmdl` (hoặc Transform data → Parameters) rồi **Refresh**.

> ⚠️ File được sinh ở dạng text (TMDL/PBIR) và **chưa được mở/validate bằng Power BI Desktop** trong môi trường tạo. DA cần mở + refresh để xác minh; có thể cần tinh chỉnh nhỏ.

## Semantic Model
- **Fact:** `Sales` (transaction grain), `Budget_Forecast` (Type×Year×Month×Category×Segment).
- **Dimension:** `Date`, `Product`, `Customer`, `Geo`, `Campaign`.
- **Quan hệ:** Sales→Product/Customer/Campaign/Date; Customer→Geo (ZIP); Budget_Forecast→Date qua cột tính `BudgetDate` (ngày 1 mỗi tháng).
- **Measures (`Key Measures`)** = đúng KPI trong BRD: Units Sold, Revenue, Average Selling Price, Active Customers, Gross Profit, Gross Margin %, Budget, Forecast, Budget Attainment %, Forecast Variance, Forecast Variance %, YoY Growth %.

## Report (3 trang — theo Report Spec & Wireframe)
1. **Executive Overview** — KPI cards · Revenue trend · Actual vs Budget · Actual vs Forecast · Revenue by Category · Revenue by Region.
2. **Product & Sales Performance** — Revenue by Category/Segment · Units Sold · Gross Margin % · Top Products · YoY Growth.
3. **Marketing & Customer** — Revenue by Campaign/Traffic Channel/Region · Active Customers trend · by State.

## Lưu ý nghiệp vụ (gắn Data Gap — chưa giải quyết)
- **Revenue = Units × Unit Price** là measure theo **Assumption DG-01**; tiền tệ chưa chốt (**DG-02**) → format dùng `#,##0` (không gắn ký hiệu tiền tệ).
- **DG-03:** `Sales` có dữ liệu 2015 nằm ngoài `Date` (2016–2021) → các giao dịch 2015 rơi vào hàng *blank* của Date khi cắt theo thời gian; Budget 2020–2021, Forecast chỉ 2021 → Budget Attainment/Forecast chỉ có ý nghĩa trong kỳ trùng. Đây là thực tế dữ liệu, **không** tự sửa.
- So sánh Budget/Forecast theo **Category/Segment** chưa dựng (cần dimension dùng chung) — để mở theo DG-03.

## Đồng bộ tài liệu
Model & report bám: `docs/part-02/05-business-requirements-document.md`, `06-report-spec.md`, `07-dashboard-wireframe.md`; dữ liệu theo `dataset/business-ready/DATASET-CONTRACT.md`.
