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
| 1 | [docs/01-data-investigation.md](docs/01-data-investigation.md) | Cấu trúc, quan hệ, profiling từng bảng, quan sát nghiệp vụ, KPI khả thi, Assumptions, câu hỏi cho stakeholder | ✅ Hoàn thành (Bước 1) |
| 2 | [docs/02-data-quality-report.md](docs/02-data-quality-report.md) | Danh sách vấn đề chất lượng dữ liệu, phân loại mức độ, nguyên nhân & khuyến nghị | ✅ Hoàn thành (Bước 1) |
| 3 | [docs/03-business-glossary.md](docs/03-business-glossary.md) | Từ điển nghiệp vụ VanArsdel cho Ban lãnh đạo (Concept Map + định nghĩa khái niệm) | ✅ Hoàn thành (Bước 2) |
| 4 | [docs/04-data-dictionary.md](docs/04-data-dictionary.md) | Mô tả 8 bảng: purpose, grain, PK/FK, quan hệ, từng cột, business rules, assumptions, data gap | ✅ Hoàn thành (Bước 3) |
| 5 | [docs/05-data-gap.md](docs/05-data-gap.md) | Tổng hợp nội dung cần stakeholder xác nhận (G-1…G-11), phân mức ưu tiên | ✅ Hoàn thành |
| 6 | [docs/06-business-requirements-document.md](docs/06-business-requirements-document.md) | BRD: mục đích, stakeholders & quyết định, business questions, KPI nghiệp vụ, assumptions, scope | ✅ Hoàn thành (Bước 4) |
| — | Report Spec + Mockup | Đặc tả để handoff cho DA | ⏳ Bước sau |

## Tóm tắt nhanh dữ liệu (từ Bước 1)
- 2 workbook: **Actuals** (6 sheet, fact `Sales` ~675K dòng, 2015→30/6/2020) và **Budget & Forecast** (wide, 2020–2021).
- Fact: `Sales` (thực tế), `Budget & Forecast` (kế hoạch). Dimension: Product, Customer, Geo, Campaign, Date.
- **Cảnh báo sớm cần làm rõ:** Sales không có cột doanh thu (`Units` luôn = 1); lệch khung thời gian giữa Sales / Date dim / Budget; chi tiết tại 2 tài liệu trên.

---
*Bước hiện tại: Bước 1 — Data Investigation đã xong, đang chờ review trước khi sang bước tiếp theo.*
