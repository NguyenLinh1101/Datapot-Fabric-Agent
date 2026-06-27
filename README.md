# VanArsdel Executive Dashboard — Business Analyst Project

Dự án **Business Analyst** cho khách hàng giả định **VanArsdel** (công ty bán lẻ tại thị trường Mỹ), trong khuôn khổ bài tuyển dụng của **Datapot Analytics**. Sản phẩm cuối là một **bộ tài liệu nghiệp vụ + dataset sẵn sàng phân tích + Power BI Executive Dashboard**, được xây dựng theo đúng quy trình Business Analysis từ dữ liệu thô đến đặc tả dashboard.

---

## 1. Project Overview & Business Problem

VanArsdel cung cấp dữ liệu bán hàng và ngân sách nhưng **chưa biết rõ cần dashboard gì**:

> *"Chúng tôi muốn một dashboard điều hành để theo dõi tình hình kinh doanh, nhưng chưa biết rõ cần gì. Đây là toàn bộ dữ liệu chúng tôi có. Bạn giúp chúng tôi làm rõ yêu cầu và mô tả lại để đội kỹ thuật build."*

**Vấn đề:** Ban lãnh đạo không có công cụ thống nhất để theo dõi sức khỏe kinh doanh; dữ liệu nằm rải rác ở dạng thô, khó so sánh thực tế với kế hoạch, và yêu cầu báo cáo chưa được định hình. Đây là khoảng trống giữa **dữ liệu thô** và **đặc tả rõ ràng** để đội kỹ thuật xây dựng — phần việc của Business Analyst.

---

## 2. Project Objectives

- Làm rõ và **chuẩn hóa ngôn ngữ nghiệp vụ** của VanArsdel.
- **Khám phá & đánh giá chất lượng** dữ liệu; xác định dữ liệu hỗ trợ được gì và chưa hỗ trợ được gì.
- Xác định **KPI** và các **câu hỏi nghiệp vụ** dashboard phải trả lời.
- Bàn giao **bộ tài liệu đầy đủ** để Data Analyst build dashboard mà gần như không cần hỏi lại.
- (Nice-to-have) **Xây Power BI Dashboard** để kiểm chứng tính khả thi của đặc tả.

---

## 3. End-to-End BA Workflow

```
Project Analysis (Discovery)
        ↓
Data Analysis (Investigation + Quality)
        ↓
Business Glossary
        ↓
Data Dictionary
        ↓
Data Gap
        ↓
Business Requirements Document (BRD)
        ↓
Report Specification
        ↓
Dashboard Wireframe
        ↓
Business-ready Dataset
        ↓
Power BI Executive Dashboard (PBIP)
```

Mỗi bước là **đầu vào** cho bước sau: không chuẩn hóa thuật ngữ khi chưa hiểu dữ liệu; không đặc tả KPI khi chưa biết khoảng trống dữ liệu; không vẽ wireframe khi chưa có KPI & câu hỏi nghiệp vụ.

---

## 4. Repository Structure

```
.
├── Project-Analysis.md          # Discovery: bối cảnh, mục tiêu, stakeholder, giả định
├── README.md
├── docs/
│   ├── part-01/                 # Hiểu & dịch dữ liệu
│   │   ├── 01-data-analysis.md
│   │   ├── 02-business-glossary.md
│   │   ├── 03-data-dictionary.md
│   │   └── 04-data-gap.md
│   └── part-02/                 # Đặc tả để handoff
│       ├── 05-business-requirements-document.md
│       ├── 06-report-spec.md
│       └── 07-dashboard-wireframe.md
├── dataset/
│   ├── DATASET-CONTRACT.md       # Hợp đồng dữ liệu cho Semantic Model
│   ├── raw/                      # Dữ liệu gốc (.xlsx) — KHÔNG commit (.gitignore)
│   ├── clean-tech/               # Sau Technical Cleaning (CSV)
│   └── business-ready/           # Dataset cuối cho Power BI (CSV)
└── pbip/                         # Power BI Project
    ├── VanArsdelExecutive.pbip
    ├── VanArsdelExecutive.SemanticModel/   # TMDL (model + measures)
    ├── VanArsdelExecutive.Report/          # PBIR (3 pages + theme)
    └── README.md                            # Hướng dẫn mở PBIP
```

---

## 5. Dataset Overview

Ba lớp dữ liệu (giữ nguyên bản gốc, chuẩn hóa tăng dần):

| Lớp | Mô tả |
|---|---|
| `raw/` | 2 workbook gốc: `VanArsdel_Actuals.xlsx`, `VanArsdel_Budget_Forecast.xlsx` + file Power Query M. *(Không commit theo `.gitignore`.)* |
| `clean-tech/` | **technical cleaning**: trim, sửa typo, ép kiểu, bỏ BOM, unpivot Budget/Forecast, chỉ xử lý vấn đề về tech, không xử lý business. |
| `business-ready/` | Dataset cuối cho Semantic Model (clean-tech sau khi đã được bổ sung các giả định, thực hiện sau khi hoàn thành tài liệu số 07): ISO date, thêm `YearQuarter`, `MonthNo`/`MonthID`, tách `City`. |

**Bảng trong `business-ready/` (số dòng dữ liệu):**

| Bảng | Vai trò | Dòng |
|---|---|--:|
| `Sales` | Fact (thực tế) | 675,368 |
| `Budget_Forecast` | Fact (kế hoạch, đã unpivot) | 324 |
| `Customer` | Dimension | 282,597 |
| `Geo` | Dimension | 39,948 |
| `Product` | Dimension | 212 |
| `Date` | Dimension | 2,192 |
| `Campaign` | Dimension | 22 |

Chi tiết grain, khóa, quan hệ, datatype, business rules → xem [`dataset/DATASET-CONTRACT.md`](dataset/DATASET-CONTRACT.md).

---

## 6. Documentation Overview

| # | Tài liệu | Nội dung |
|---|---|---|
| 00 | [Project-Analysis.md](Project-Analysis.md) | Discovery: phân tích đề bài, bối cảnh, mục tiêu engagement, stakeholder, ≥12 Business Assumptions (kèm căn cứ), workflow. |
| 01 | [01-data-analysis.md](docs/part-01/01-data-analysis.md) | Data Investigation + Data Quality: cấu trúc, quan hệ, profiling 7 bảng, 18 vấn đề chất lượng, technical cleaning đã thực hiện. |
| 02 | [02-business-glossary.md](docs/part-01/02-business-glossary.md) | Từ điển nghiệp vụ VanArsdel (Concept Map + định nghĩa khái niệm) cho Ban lãnh đạo. |
| 03 | [03-data-dictionary.md](docs/part-01/03-data-dictionary.md) | Mô tả 7 bảng: Business Purpose, Grain, PK/FK, từng cột (Source/Type/Hidden/Description), Relationships. |
| 04 | [04-data-gap.md](docs/part-01/04-data-gap.md) | Các điểm chặn việc build cần stakeholder xác nhận (DG-01…DG-10), phân mức ưu tiên. |
| 05 | [05-business-requirements-document.md](docs/part-02/05-business-requirements-document.md) | BRD: người dùng & quyết định, business questions, KPI + định nghĩa công thức nghiệp vụ, assumptions, scope. |
| 06 | [06-report-spec.md](docs/part-02/06-report-spec.md) | Report Specification: KPI spec, grain & dimensions, từng page/visual (metric·dimension·purpose), filters, design notes. |
| 07 | [07-dashboard-wireframe.md](docs/part-02/07-dashboard-wireframe.md) | Wireframe ASCII + visual spec có truy vết (BQ→KPI→Visual→Decision) + Coverage Check. |

---

## 7. Dashboard Overview

**Power BI Project** (`pbip/VanArsdelExecutive.pbip`): Semantic Model (TMDL) + Report (PBIR), 3 trang, nối tới `dataset/business-ready/`.

**Semantic Model:** 7 bảng (star schema quanh `Sales`; `Budget_Forecast` nối `Date` qua `BudgetDate`), **14 measure**: Units Sold, Revenue, Average Selling Price, Active Customers, COGS, Gross Profit, Gross Margin %, Budget, Forecast, Budget Attainment %, Forecast Variance, Forecast Variance %, Revenue PY, YoY Growth %.

| Page | Mục đích | Stakeholder chính |
|---|---|---|
| **Executive Overview** | Tổng quan sức khỏe kinh doanh: KPI cards · Revenue trend · Actual vs Budget · Actual vs Forecast · Revenue by Category/Region | CEO, Finance Director |
| **Product & Sales Performance** | Hiệu quả sản phẩm: Revenue by Category/Segment · Units Sold · Gross Margin % · Top Products · YoY Growth | Sales Director, CEO |
| **Marketing & Customer** | Hiệu quả marketing & khách hàng: Revenue by Campaign/Traffic Channel/Device · Active Customers (trend & theo Region) | Marketing Director |

---

## 8. Business Assumptions & Data Gaps (tóm tắt)

**Giả định đang dùng (cần stakeholder xác nhận — không phải sự thật):**
- `Revenue = Units × Unit Price` (gross); đơn vị tiền tệ giả định **USD**.
- So sánh Actual–Plan ở mức **Category-Segment theo tháng**; mỗi dòng `Sales` = một đơn vị bán; dùng **năm dương lịch**.

**Data Gaps ưu tiên (chặn build nếu chưa rõ):**
- **DG-01/02** — định nghĩa Revenue (gross/net, thuế/chiết khấu/trả hàng) & tiền tệ.
- **DG-03** — kỳ/cấp so sánh Actual vs Budget/Forecast (Actual 2015–6/2020, Budget 2020–2021, Forecast chỉ 2021 → overlap rất hẹp).
- **DG-06/08** — `Sales` không có khóa giao dịch (có dòng trùng); ngữ nghĩa Campaign–Sales (attribution?).

Chi tiết: [`docs/part-01/04-data-gap.md`](docs/part-01/04-data-gap.md).

---

## 9. Tech Stack

- **Power BI Desktop** — PBIP (Semantic Model dạng **TMDL** + Report dạng **PBIR**).
- **Power Query (M)** — nạp dữ liệu CSV từ `dataset/business-ready/` qua tham số `DataFolder`.
- **CSV** — định dạng dataset business-ready.
- **Markdown** — toàn bộ tài liệu BA.
- **Microsoft Skills for Fabric** — dùng làm *hướng dẫn thực thi* khi dựng PBIP.
- Nguồn gốc: **Excel** (`.xlsx`).

---

## 10. How to Reproduce

1. **Clone** repository. *(Thư mục `dataset/raw/` không được commit; dataset làm việc nằm ở `dataset/business-ready/`.)*
2. Đọc tài liệu theo thứ tự: `Project-Analysis.md` → `docs/part-01/01…04` → `docs/part-02/05…07`.
3. Mở **Power BI Desktop** và bật Preview features:
   - *Power BI Project (.pbip) save option*
   - *Store reports using enhanced metadata format (PBIR)*
4. Mở `pbip/VanArsdelExecutive.pbip`.
5. Kiểm tra tham số **`DataFolder`** trỏ tới thư mục `dataset/business-ready/` (mặc định: `...\dataset\business-ready`); chỉnh nếu di chuyển project.
6. **Refresh** để nạp dữ liệu. Xem thêm [`pbip/README.md`](pbip/README.md).

---

## 11. Project Outcomes

- Bộ tài liệu BA **đầy đủ, truy vết được** từ Discovery đến Wireframe.
- Dataset **business-ready** sạch, nhất quán, sẵn sàng dựng Semantic Model.
- Power BI Dashboard **3 trang** trả lời **8/8 Business Questions** trong BRD và hỗ trợ quyết định của 4 stakeholder.
- Mọi giả định & khoảng trống dữ liệu được **ghi nhận minh bạch**, không suy diễn.

---

## 12. Limitations

- **Data Gaps chưa được stakeholder chốt** (DG-01/02/03…): các KPI tiền tệ & so-kế-hoạch dựa trên giả định.
- **Lệch khung thời gian:** dữ liệu bán 2015 nằm ngoài bảng `Date` (2016–2021) → bị loại khỏi visual theo thời gian; Actual vs Forecast hầu như không có kỳ trùng → cụm Plan-vs-Actual chỉ có ý nghĩa khi lọc về **2020**.
- **Campaign không có tên mô tả** (chỉ `CampaignID` 1–22).
- **PBIP chưa được validate bằng Power BI Desktop** trong môi trường tạo file — cần mở & refresh để xác minh.
- So sánh Budget theo Category/Segment chưa được dựng (để mở theo DG-03).

---

## 13. License & Notes

Dự án mang tính **case study đào tạo** (Datapot Analytics). Dữ liệu VanArsdel là **bộ dữ liệu mẫu** dùng cho mục đích học tập/đánh giá năng lực, không phải dữ liệu kinh doanh thật.
