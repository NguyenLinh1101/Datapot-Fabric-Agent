# Project Analysis & BA Approach — VanArsdel

> Tài liệu **đầu tiên** của dự án, thuộc giai đoạn **Discovery**. Mục đích: phân tích đề bài, xác định bối cảnh, stakeholder, mục tiêu và các giả định hợp lý **trước khi** Business Analyst bắt đầu bất kỳ hoạt động phân tích dữ liệu nào.
>
> Mọi giả định ở đây là **giả định**, kèm căn cứ và bước xác nhận — không phải sự thật đã được khách hàng chốt.

---

## I. Tổng quan dự án

### 1. Bối cảnh

Đề bài:

> *"Chúng tôi muốn một dashboard điều hành để theo dõi tình hình kinh doanh, nhưng chưa biết rõ cần gì. Đây là toàn bộ dữ liệu chúng tôi có. Bạn giúp chúng tôi làm rõ yêu cầu và mô tả lại để đội kỹ thuật build."*

**VanArsdel đang gặp vấn đề gì.** Phân tích câu nói trên cho thấy ba điểm cốt lõi:
- *"muốn một dashboard điều hành để theo dõi tình hình kinh doanh"* → có **nhu cầu theo dõi**, nhưng đang thiếu công cụ tổng hợp.
- *"nhưng chưa biết rõ cần gì"* → **yêu cầu chưa được định hình**. Khách hàng biết mục tiêu (theo dõi kinh doanh) nhưng chưa biết cần đo gì, xem gì.
- *"Đây là toàn bộ dữ liệu chúng tôi có… giúp làm rõ yêu cầu và mô tả lại để đội kỹ thuật build"* → khách hàng có **dữ liệu nhưng chưa có cầu nối** giữa dữ liệu và đội kỹ thuật.

**Vì sao họ tìm đến Business Analyst.** Vấn đề của VanArsdel không phải vấn đề kỹ thuật ("không build được dashboard") mà là vấn đề **làm rõ và đặc tả yêu cầu** ("chưa biết cần gì"). Tóm lại, VanArsdel đang ở tình trạng có dữ liệu nhưng thiếu cầu nối giữa dữ liệu thô và insight kinh doanh — đây chính là khoảng trống mà Business Analyst cần lấp đầy.

**Đây là giai đoạn nào của dự án BI.** Vì yêu cầu chưa được định hình và dữ liệu chưa được khám phá, dự án đang ở **giai đoạn Discovery** — giai đoạn sớm nhất, trước cả Data Investigation. Tài liệu này là sản phẩm mở màn của Discovery.

> Đề bài không nêu ngành con, quy mô, vùng thị trường hay hệ thống hiện có của VanArsdel, nên tài liệu không kết luận các điểm này.

### 2. Mục tiêu của dự án (engagement)

Đây là mục tiêu của **toàn bộ hợp tác giữa VanArsdel và Business Analyst**, không phải mục tiêu của dashboard.

| Mục tiêu | Vì sao cần |
|---|---|
| **Làm rõ yêu cầu kinh doanh** | Khách nói "chưa biết rõ cần gì" → việc đầu tiên BA phải làm là biến nhu cầu mơ hồ thành yêu cầu cụ thể, nếu không mọi bước sau đều thiếu nền. |
| **Chuẩn hóa ngôn ngữ nghiệp vụ** | Nhiều phòng ban (bán hàng, marketing, tài chính) có thể hiểu khác nhau về cùng một thuật ngữ; cần một cách hiểu thống nhất trước khi đo lường. |
| **Khám phá dữ liệu** | Dữ liệu là nguồn thông tin duy nhất khách cung cấp; phải hiểu dữ liệu nói được gì và **không** nói được gì trước khi hứa hẹn KPI. |
| **Chuyển nhu cầu thành tài liệu để Data Analyst build dashboard** | Đề bài nêu rõ "mô tả lại để đội kỹ thuật build" → đầu ra của BA là bộ đặc tả, không phải dashboard. |

### 3. Vai trò của Business Analyst trong case study

Trong phạm vi case study này, Business Analyst **không** chịu trách nhiệm chính cho việc phát triển dashboard. Trọng tâm là **tạo bộ tài liệu đặc tả đủ rõ** để Data Analyst triển khai dashboard mà không phải làm rõ lại nhiều yêu cầu.

Việc tự xây dashboard Power BI (PBIP/TMDL/PBIR) là **phần mở rộng (Nice to Have)** nhằm **kiểm chứng tính khả thi** của tài liệu — chứng minh đặc tả "build được" trên thực tế — chứ **không** phải deliverable bắt buộc.

### 4. Deliverables & thứ tự

Các tài liệu sẽ tạo, sắp theo đúng trình tự logic của Business Analysis:

| # | Tài liệu | Vì sao đặt ở vị trí này |
|---|---|---|
| 00 | Project Analysis & BA Approach | Định khung trước khi làm bất cứ gì. |
| 01 | Data Investigation | Hiểu dữ liệu trước khi diễn giải nghiệp vụ. |
| 02 | Data Quality Assessment | Biết dữ liệu đáng tin tới đâu trước khi xây khái niệm trên nó. |
| 03 | Business Glossary | Chuẩn hóa thuật ngữ — nền cho mọi mô tả sau. |
| 04 | Data Dictionary | Gắn thuật ngữ nghiệp vụ với dữ liệu thực tế để handoff. |
| 05 | Data Gap | Tách bạch điều đã biết và điều cần khách xác nhận. |
| 06 | Business Requirements Document | Tổng hợp nhu cầu, KPI, scope ở góc nhìn nghiệp vụ. |
| 07 | Report Specification | Đặc tả nội dung dashboard để DA build. |
| 08 | Dashboard Wireframe | Phác layout, hoàn thiện đặc tả trực quan. |

**Vì sao thứ tự này hợp lý:** mỗi tài liệu là **đầu vào** cho tài liệu kế tiếp. Không thể chuẩn hóa thuật ngữ (03) khi chưa hiểu dữ liệu (01); không thể đặc tả KPI (06) khi chưa biết khoảng trống dữ liệu (05); không thể vẽ wireframe (08) khi chưa có KPI và câu hỏi nghiệp vụ (06–07). Đi sai thứ tự sẽ phải làm lại.

### 5. Phạm vi dự án

**In Scope** 
- **Phân tích & đặc tả yêu cầu cho Executive Dashboard** 
- **Khám phá & đánh giá chất lượng dữ liệu bán hàng và ngân sách** 
- **Chuẩn hóa ngôn ngữ nghiệp vụ** — điều kiện để các bên hiểu thống nhất.
- **Bộ tài liệu handoff cho Data Analyst** — đầu ra cuối cùng của engagement.

**Out of Scope** 
- **Xây dựng dashboard Power BI như deliverable bắt buộc** — đề bài định vị đây là Nice-to-have.
- **Báo cáo tác nghiệp / chi tiết giao dịch** — Executive Dashboard phục vụ điều hành, không phải vận hành.
- **Các lĩnh vực ngoài dữ liệu cung cấp** (tồn kho, chuỗi cung ứng, nhân sự, kế toán tài chính) — không có dữ liệu, và nằm ngoài "tình hình kinh doanh" theo nghĩa bán hàng/ngân sách.
- **Dự báo bằng AI/ML** 

---

## II. Business Assumptions

> Mỗi giả định gồm: **Cơ sở** · **Tác động tới dự án (kể cả nếu giả định sai)** · **Cần xác nhận ở bước nào**. Đây là giả định, không phải sự thật.

### BA-01 — Dashboard phục vụ Ban lãnh đạo
- **Business Assumption:** Dashboard được giả định phục vụ Ban lãnh đạo (CEO và các Director).
- **Cơ sở:** Đề bài dùng cụm "dashboard điều hành" (Executive Dashboard). Trong thực tế BI, Executive Dashboard phục vụ nhóm ra quyết định chiến lược. Đề không nêu người dùng cụ thể nên BA dùng giả định này làm nền.
- **Tác động:** Định hình mức độ tổng hợp, ngôn ngữ và KPI của toàn bộ tài liệu. Nếu sai (ví dụ thực ra phục vụ quản lý cấp trung), độ chi tiết và KPI sẽ phải điều chỉnh lại đáng kể.
- **Cần xác nhận:** Discovery — phỏng vấn stakeholder.

### BA-02 — Dashboard phục vụ quyết định chiến lược/điều hành, không phải tác nghiệp
- **Business Assumption:** Dashboard tập trung mức tổng hợp để ra quyết định điều hành, không phục vụ thao tác hằng ngày.
- **Cơ sở:** Hệ quả của "dashboard điều hành"; Executive Dashboard theo thông lệ là tổng hợp, không chi tiết từng giao dịch.
- **Tác động:** Quyết định cái gì In/Out of Scope (ví dụ loại transaction detail). Nếu sai, phạm vi sẽ phải mở rộng sang báo cáo tác nghiệp.
- **Cần xác nhận:** Discovery; chốt lại ở BRD.

### BA-03 — Khách hàng chưa có yêu cầu chi tiết
- **Business Assumption:** VanArsdel chưa xác định rõ KPI, câu hỏi nghiệp vụ hay layout mong muốn.
- **Cơ sở:** Câu "chưa biết rõ cần gì" trong đề bài.
- **Tác động:** BA phải **chủ động đề xuất** yêu cầu thay vì chỉ ghi nhận. Mọi đề xuất sẽ là giả định cần khách phê duyệt. Nếu thực ra khách đã có yêu cầu ẩn, cần thu thập sớm để tránh làm lệch hướng.
- **Cần xác nhận:** Discovery.

### BA-04 — Dataset là nguồn thông tin duy nhất ban đầu
- **Business Assumption:** Toàn bộ hiểu biết ban đầu về nghiệp vụ phải rút ra từ dữ liệu được cung cấp.
- **Cơ sở:** Đề bài: "Đây là toàn bộ dữ liệu chúng tôi có."
- **Tác động:** Việc khám phá nghiệp vụ mang tính **data-driven**; những gì dữ liệu không thể hiện sẽ không được khẳng định. Nếu sau này có thêm nguồn (CRM, tài chính…), một số kết luận có thể thay đổi.
- **Cần xác nhận:** Discovery (hỏi còn nguồn dữ liệu nào khác không).

### BA-05 — Dataset chưa phải sự thật tuyệt đối
- **Business Assumption:** Dữ liệu có thể chứa lỗi, thiếu, hoặc không nhất quán.
- **Cơ sở:** Thông lệ BI; chưa có cam kết nào về chất lượng dữ liệu từ khách.
- **Tác động:** Bắt buộc có bước Data Quality Assessment (02) trước khi xây khái niệm/KPI trên dữ liệu. Nếu bỏ qua, KPI có thể sai mà không ai biết.
- **Cần xác nhận:** Bước 02 (Data Quality), và xác nhận lại với khách các bất thường.

### BA-06 — Business Analyst phải chủ động khám phá nghiệp vụ từ dữ liệu
- **Business Assumption:** Vì yêu cầu mơ hồ, BA phải suy ra nghiệp vụ từ cấu trúc dữ liệu rồi đề xuất ngược lại cho khách.
- **Cơ sở:** Kết hợp BA-03 và BA-04.
- **Tác động:** Xác định phương pháp làm việc của cả dự án (khám phá trước, xác nhận sau). Nếu khách kỳ vọng BA chỉ ghi chép yêu cầu có sẵn, cách tiếp cận sẽ phải đổi.
- **Cần xác nhận:** Discovery.

### BA-07 — Điều chưa đủ bằng chứng sẽ đưa vào Data Gap, không suy diễn
- **Business Assumption:** Mọi điểm chưa rõ được ghi nhận như câu hỏi cho stakeholder thay vì tự kết luận.
- **Cơ sở:** Nguyên tắc nghề nghiệp BA và yêu cầu "làm rõ" của đề bài.
- **Tác động:** Giữ tài liệu trung thực và có thể truy vết; tránh đưa giả định thành sự thật. Nếu suy diễn bừa, DA có thể build sai theo niềm tin sai.
- **Cần xác nhận:** Xuyên suốt; tổng hợp tại Data Gap (05).

### BA-08 — Phạm vi nghiệp vụ là Bán hàng & Ngân sách
- **Business Assumption:** Dashboard xoay quanh kết quả bán hàng và đối chiếu kế hoạch (ngân sách), không mở sang lĩnh vực khác.
- **Cơ sở:** Đề bài: khách cung cấp "bộ dữ liệu bán hàng & ngân sách".
- **Tác động:** Khoanh vùng KPI và chiều phân tích. Nếu khách thực ra muốn cả lĩnh vực ngoài (ví dụ dịch vụ khách hàng), phải mở rộng dữ liệu và phạm vi.
- **Cần xác nhận:** Discovery; chốt ở BRD scope.

### BA-09 — Người tiếp nhận tài liệu là Data Analyst
- **Business Assumption:** Bộ tài liệu được viết để một Data Analyst dùng làm đầu vào build dashboard.
- **Cơ sở:** Đề bài: "mô tả lại để đội kỹ thuật build" và tiêu chí "DA không phải hỏi lại nhiều".
- **Tác động:** Quyết định độ chi tiết và tính tự-đủ của tài liệu (đặc biệt Report Spec). Nếu người nhận là vai trò khác, mức đặc tả sẽ khác.
- **Cần xác nhận:** Đã ngầm định trong đề; rà lại khi handoff.

### BA-10 — Trọng tâm là tài liệu; build dashboard là Nice-to-have
- **Business Assumption:** Thành công của dự án được đo bằng chất lượng tài liệu, không bằng việc có dashboard hoàn chỉnh.
- **Cơ sở:** Đề bài tách rõ phần "tạo tài liệu" (chính) và "build Power BI" (Nice to have).
- **Tác động:** Ưu tiên nguồn lực cho đặc tả. Nếu khách thực sự kỳ vọng dashboard chạy được ngay, cần thương lượng lại phạm vi.
- **Cần xác nhận:** Discovery / thống nhất phạm vi với PM.

### BA-11 — VanArsdel là doanh nghiệp bán lẻ
- **Business Assumption:** Khung phân tích áp dụng đặc thù bán lẻ (doanh thu, sản phẩm, khách hàng, vùng, kênh).
- **Cơ sở:** Đề bài nêu trực tiếp "một công ty bán lẻ".
- **Tác động:** Định hướng các chiều phân tích đề xuất. Nếu mô hình kinh doanh khác với bán lẻ thuần (ví dụ bán buôn/đại lý), một số chiều có thể không phù hợp.
- **Cần xác nhận:** Discovery.

### BA-12 — Dashboard sẽ được hiện thực bằng Power BI
- **Business Assumption:** Công cụ xây dashboard là Power BI.
- **Cơ sở:** Phần Nice-to-have của đề bài nhắc tới "report PBIP (TMDL + PBIR)" — định dạng Power BI.
- **Tác động:** Ảnh hưởng cách viết Report Spec (thuật ngữ trang/visual phù hợp Power BI). Nếu công cụ khác, đặc tả trình bày cần điều chỉnh, dù phần nghiệp vụ vẫn dùng lại được.
- **Cần xác nhận:** Discovery / xác nhận với đội kỹ thuật.

---

## III. Stakeholder Analysis

> Danh sách dưới đây được BA **suy ra từ cụm "dashboard điều hành" + bối cảnh bán lẻ với dữ liệu bán hàng & ngân sách**, và là **giả định cần xác nhận ở Discovery** (gắn với BA-01).

### CEO
- **Cơ sở:** Executive Dashboard phục vụ cấp ra quyết định cao nhất.
- **Vai trò:** Điều hành chung toàn doanh nghiệp.
- **Mối quan tâm:** Bức tranh tổng thể — doanh thu toàn công ty, mức đạt mục tiêu, xu hướng tăng trưởng.
- **Quyết định dùng dashboard:** Định hướng chiến lược, ưu tiên nguồn lực giữa dòng sản phẩm/vùng.

### Sales Director
- **Cơ sở:** Dữ liệu cốt lõi là bán hàng → lãnh đạo bán hàng là người dùng tự nhiên.
- **Vai trò:** Chịu trách nhiệm kết quả bán hàng theo vùng và nhóm sản phẩm.
- **Mối quan tâm:** Doanh thu theo Region/sản phẩm so với kế hoạch; vùng/nhóm nào hụt mục tiêu.
- **Quyết định dùng dashboard:** Phân bổ mục tiêu & nguồn lực bán hàng, can thiệp vùng yếu.

### Marketing Director
- **Cơ sở:** Dữ liệu có yếu tố chiến dịch/kênh → hiệu quả marketing là một góc của "tình hình kinh doanh".
- **Vai trò:** Chịu trách nhiệm hiệu quả marketing.
- **Mối quan tâm:** Chiến dịch/kênh nào tạo ra doanh thu.
- **Quyết định dùng dashboard:** Phân bổ ngân sách marketing, điều chỉnh/dừng chiến dịch kém.

### Finance Director
- **Cơ sở:** Đề bài có dữ liệu **ngân sách** → đối chiếu thực tế với kế hoạch là mối quan tâm tài chính.
- **Vai trò:** Kiểm soát kế hoạch tài chính và mức đạt ngân sách.
- **Mối quan tâm:** Thực tế so với Budget/Forecast theo kỳ.
- **Quyết định dùng dashboard:** Điều chỉnh dự báo, cảnh báo lệch kế hoạch sớm.

### Business Analyst *(vai trò dự án)*
- **Cơ sở:** Chính là vai trò người thực hiện engagement.
- **Vai trò:** Làm rõ nghiệp vụ, chuẩn hóa thuật ngữ, đặc tả yêu cầu.
- **Mối quan tâm:** Tài liệu đúng nhu cầu và đủ rõ để handoff.
- **Quyết định:** Xác định KPI, câu hỏi nghiệp vụ, scope đề xuất cho stakeholder phê duyệt.

### Data Analyst *(vai trò dự án)*
- **Cơ sở:** Đề bài: "đội kỹ thuật build".
- **Vai trò:** Hiện thực đặc tả thành dashboard (Power BI).
- **Mối quan tâm:** Tài liệu đủ rõ để build mà không phải hỏi lại nhiều.
- **Quyết định:** Cách dựng mô hình dữ liệu và trực quan dựa trên đặc tả của BA.

---

## IV. Business Problem Statement (hiểu biết ban đầu)

> **Đây là cách hiểu tạm thời** của Business Analyst trước Discovery, **chưa phải sự thật đã xác nhận**.

VanArsdel hiện chưa có một công cụ thống nhất để Ban lãnh đạo theo dõi tình hình kinh doanh. Thông tin nằm ở dạng dữ liệu thô (bán hàng & ngân sách), khó tổng hợp và khó đối chiếu thực tế với kế hoạch. Quan trọng hơn, **bản thân yêu cầu báo cáo chưa được định hình** — khách biết muốn "theo dõi kinh doanh" nhưng chưa biết cần đo gì.

Vì vậy vấn đề trọng tâm không thuần kỹ thuật, mà là **khoảng trống giữa nhu cầu mơ hồ và một đặc tả rõ ràng** để đội kỹ thuật xây dựng được. Cách hiểu này sẽ được kiểm chứng và tinh chỉnh qua Discovery và các bước phân tích tiếp theo.

---

## V. Success Criteria

Dự án thành công khi:

| Tiêu chí | Vì sao là thước đo đúng |
|---|---|
| **Yêu cầu nghiệp vụ rõ ràng** | Đề bài bắt đầu từ "chưa biết rõ cần gì" → làm rõ được chính là giá trị cốt lõi BA tạo ra. |
| **Data Analyst không phải hỏi lại BA nhiều** | Đây là tiêu chí ngầm nêu thẳng trong đề bài; đo trực tiếp chất lượng handoff. |
| **Dashboard (khi build) phản ánh đúng nhu cầu kinh doanh** | Tài liệu đúng nhưng lệch nhu cầu thực thì vô nghĩa; KPI và câu hỏi phải khớp quyết định của stakeholder. |
| **Giả định & khoảng trống được ghi nhận minh bạch** | Một bộ tài liệu trung thực (phân biệt rõ điều đã biết vs cần xác nhận) đáng tin hơn một bộ tài liệu "trông hoàn chỉnh" nhưng suy diễn. |

---

## VI. Kế hoạch thực hiện (Business Analysis Workflow)

```
Project Analysis & BA Approach
        ↓
Data Investigation
        ↓
Data Quality Assessment
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
Wireframe / Mockup
        ↓
Power BI Dashboard (Optional)
```

**Vì sao phải theo trình tự này — nguyên nhân trước–sau:**

1. **Project Analysis** đứng đầu vì phải định khung (mục tiêu, scope, giả định) trước khi tiêu tốn công sức; tránh phân tích lan man sai hướng.
2. **Data Investigation** trước mọi diễn giải nghiệp vụ, vì dữ liệu là nguồn duy nhất (BA-04) — không hiểu dữ liệu thì không thể nói nó đo được gì.
3. **Data Quality Assessment** ngay sau đó, vì cần biết dữ liệu đáng tin tới đâu (BA-05) trước khi xây khái niệm/KPI trên nó.
4. **Business Glossary** trước Data Dictionary, vì phải thống nhất *ý nghĩa nghiệp vụ* trước khi gắn nó vào *cấu trúc dữ liệu*.
5. **Data Dictionary** nối thuật ngữ nghiệp vụ với dữ liệu thực — là cầu nối kỹ thuật, chỉ làm được sau khi đã có glossary và hiểu dữ liệu.
6. **Data Gap** tách bạch điều đã biết và điều cần khách xác nhận; phải có trước BRD để BRD không xây trên giả định chưa kiểm.
7. **BRD** tổng hợp nhu cầu, KPI, scope — chỉ vững khi đã qua các bước hiểu dữ liệu, chuẩn hóa thuật ngữ và xác định khoảng trống.
8. **Report Specification** cụ thể hóa BRD thành nội dung dashboard; phải có KPI và câu hỏi nghiệp vụ (từ BRD) làm đầu vào.
9. **Wireframe** hoàn thiện đặc tả trực quan, đặt sau Report Spec vì layout phải phục vụ các KPI/câu hỏi đã chốt.
10. **Power BI Dashboard** đặt cuối và **tùy chọn**, chỉ để kiểm chứng tính khả thi của đặc tả (BA-10).

Mỗi bước là đầu vào của bước sau; đảo thứ tự sẽ buộc phải quay lại làm lại — đó là lý do trình tự này được giữ chặt.

---
*Tài liệu Discovery. Không phân tích dataset, không định nghĩa KPI, không thiết kế dashboard. Mọi giả định cần được stakeholder xác nhận ở các bước đã nêu.*
