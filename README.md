# VanArsdel — Bộ tài liệu Business Analyst

Dự án Business Analyst cho khách hàng giả định **VanArsdel** (bán lẻ). Khách cung cấp dữ liệu bán hàng & ngân sách nhưng chưa rõ cần dashboard gì.

**Mục tiêu:** KHÔNG build Power BI ngay, mà tạo bộ tài liệu phân tích & đặc tả để một **Data Analyst** có thể nhận và build dashboard mà không phải hỏi lại nhiều.

## Nguyên tắc xuyên suốt
- **Handoff-first** — viết để DA build được ngay.
- **Evidence-first** — mọi kết luận phải truy xuất được từ dữ liệu; điều chưa chắc → đưa vào *Assumptions* hoặc *Questions for Stakeholder*, không suy diễn.
- **Góc nhìn Business Analyst** — nghiệp vụ trước, hình thức trình bày sau.

## Cấu trúc thư mục
```
dataset/raw/        # Dữ liệu gốc (không chỉnh sửa)
docs/               # Tài liệu phân tích & đặc tả
README.md           # File này
```

## Thứ tự đọc tài liệu

| # | Tài liệu | Nội dung | Trạng thái |
|---|---|---|---|
| 1 | [docs/data-investigation.md](docs/data-investigation.md) | Cấu trúc, quan hệ, profiling từng bảng, quan sát nghiệp vụ, KPI khả thi, Assumptions, câu hỏi cho stakeholder | ✅ Hoàn thành (Bước 1) |
| 2 | [docs/data-quality-report.md](docs/data-quality-report.md) | Danh sách vấn đề chất lượng dữ liệu, phân loại mức độ, nguyên nhân & khuyến nghị | ✅ Hoàn thành (Bước 1) |
| — | Business Glossary | Định nghĩa khái niệm nghiệp vụ VanArsdel | ⏳ Bước sau |
| — | Data Dictionary | Mô tả bảng/cột/quan hệ theo template | ⏳ Bước sau |
| — | BRD + Report Spec + Mockup | Đặc tả để handoff cho DA | ⏳ Bước sau |

## Tóm tắt nhanh dữ liệu (từ Bước 1)
- 2 workbook: **Actuals** (6 sheet, fact `Sales` ~675K dòng, 2015→30/6/2020) và **Budget & Forecast** (wide, 2020–2021).
- Fact: `Sales` (thực tế), `Budget & Forecast` (kế hoạch). Dimension: Product, Customer, Geo, Campaign, Date.
- **Cảnh báo sớm cần làm rõ:** Sales không có cột doanh thu (`Units` luôn = 1); lệch khung thời gian giữa Sales / Date dim / Budget; chi tiết tại 2 tài liệu trên.

---
*Bước hiện tại: Bước 1 — Data Investigation đã xong, đang chờ review trước khi sang bước tiếp theo.*
