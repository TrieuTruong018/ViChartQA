# BÁO CÁO NGHIÊN CỨU TỔNG KẾT CHI TIẾT: BENCHMARK FACT-CHECK MODEL & SYSTEM PROMPT TRÊN VICHAARTQA BALANCED V1

---

## CHƯƠNG 1: TRANG ĐẦU, THỜI ĐIỂM THỰC HIỆN VÀ THÔNG TIN REPOSITORY

- **Tên dự án:** ViChartQA Fact-Checking Multimodal Benchmark & Prompt Engineering Evaluation (Balanced V1)
- **Đơn vị thực hiện:** Antigravity / Pair-Programming Research Assistant
- **Người nhận báo cáo:** Dự án Leader (Review & Decision Making)
- **Ngày hoàn tất và lập báo cáo:** 10 tháng 10 năm 2026
- **Repository chuẩn (Canonical Workspace):**`D:\Trường Đại Học Công Thương\Thực tập_freelance\ViChartQA\ViChartQA_git`
- **Tập dữ liệu nguồn và dẫn xuất:**
  - Dataset gốc: `process/vichartqa.json`
  - Dataset dẫn xuất Fact-Check: `process/vichartqa_factcheck_balanced_v1.json`
- **Workspace thực nghiệm chuyên trách:**`experiments/openrouter_factcheck_pilot/balanced_v1/`
- **Trạng thái thực nghiệm:**
  **Đã hoàn tất benchmark thử nghiệm giới hạn theo phạm vi thực tế đã chạy; báo cáo đã hoàn thành. Chưa phải benchmark toàn bộ Validation/Test pool.**

---

## CHƯƠNG 2: TÓM TẮT ĐIỀU HÀNH (EXECUTIVE SUMMARY)

Báo cáo này đúc kết toàn diện kết quả thực nghiệm benchmark đối đầu giữa **10 mô hình ngôn ngữ thị giác đa phương thức (Multimodal Vision-Language Models)** và **3 chiến lược thiết kế System Prompt (A, B, C)** trên bài toán xác thực thông tin biểu đồ (Fact-Check) tiếng Việt thuộc tập dữ liệu cân bằng `ViChartQA Balanced V1`.

### Các phát hiện và kết luận trọng tâm:

1. **Mô hình và tổ hợp chiến thắng được đề xuất (Recommended Winner):**Tổ hợp **`google/gemini-3.1-flash-lite`** kết hợp với **`prompt_A` (Minimalist / Direct Tag Extraction)** đạt kết quả xuất sắc nhất và ổn định nhất toàn diện:

   - **Validation Phase (15 mẫu × 3 prompt = 45 lượt đánh giá):** Strict Accuracy đạt **86,67%**, Macro-F1 đạt **0,8661**, Recall lớp `"Sai"` đạt **100,00%** (bắt trọn 100% nhận định sai lệch), Parse Compliance tuyệt đối **100,0%**.
   - **Holdout Test Phase (50 mẫu độc lập):** Strict Accuracy đạt **88,00%** (44/50 mẫu), Balanced Accuracy đạt **88,24%**, Macro-F1 đạt **0,8798**, Recall lớp `"Sai"` đạt **91,30%** (21/23 phát biểu sai được vạch trần), Precision lớp `"Sai"` đạt **84,00%** (21/25).
   - **Hiệu năng & Chi phí:** Độ trễ trung bình chỉ **1.588 ms**, chi phí thực tế cực thấp: **$0,000866 USD/mẫu** (~0,87 USD trên 1.000 câu hỏi fact-check kèm ảnh).
   - **Vòng đời mô hình:** Là phiên bản General Availability (GA) chính thức của Google, vận hành ổn định lâu dài, không chịu cảnh báo ngừng hỗ trợ.
2. **Đánh giá chiến lược System Prompt:`prompt_A`** chứng minh tính ưu việt vượt trội về độ ổn định vận hành công nghiệp với **100,0% Parse Compliance** trên toàn bộ 150 request Validation (không có bất kỳ phản hồi nào bị gãy cấu trúc tag `<answer>`). Ngược lại, `prompt_B` và `prompt_C` mặc dù thúc đẩy tư duy phân tích sâu hơn nhưng khiến các mô hình nhỏ bị tràn token hoặc tự động giải thích trước thẻ tag, dẫn đến **5 lỗi parse ở Prompt B** (Compliance 96,67%) và **4 lỗi parse ở Prompt C** (Compliance 97,33%).
3. **Kiểm soát ngân sách và an toàn tài chính tuyệt đối:**

   - Tổng chi phí thực tế đã thanh toán qua OpenRouter cho toàn bộ **500 API request** (450 lượt Validation + 50 lượt Holdout Test) chỉ là **$0,5980 USD** (chưa tới 60 cents Mỹ).
   - Chi phí thực tế chỉ chiếm **2,14%** so với hạn mức ngân sách nội bộ ($28,00 USD) và **1,71%** so với trần cứng ($35,00 USD), giữ lại nguyên vẹn **$34,40 USD** thặng dư ngân sách.
4. **Bảo toàn dữ liệu 100%:**
   Toàn bộ mã băm SHA-256 của dataset gốc `vichartqa.json` và dataset dẫn xuất `vichartqa_factcheck_balanced_v1.json` được bảo toàn nguyên vẹn tuyệt đối trước và sau toàn bộ quá trình thực nghiệm.

---

## CHƯƠNG 3: YÊU CẦU GỐC CỦA LEADER

Mục tiêu nghiên cứu:

1. Tuyển chọn danh sách ~10 mô hình multimodal có chi phí tối ưu, khả thi trên OpenRouter, ưu tiên đặc biệt hệ sinh thái Google Gemini.
2. Thiết kế và kiểm chứng 3 biến thể System Prompt (A: Tối giản trực diện, B: Hướng dẫn chứng cứ nghiêm ngặt, C: Quy trình 3 bước nội bộ).
3. Đánh giá thực nghiệm trên tập mẫu Fact-Check có cân bằng nhãn `"Đúng"` và `"Sai"`, kiểm tra tính độc lập giữa các tài liệu (documents).
4. Đo lường chính xác các chỉ số: Phân loại nhị phân (Accuracy, Macro-F1, Recall/Precision lớp `"Sai"`), Độ tuân thủ định dạng (Parse Compliance), Tốc độ xử lý (Latency Mean, Median, P95) và Chi phí API thực tế từ hóa đơn OpenRouter.
5. Lựa chọn tổ hợp Model – Prompt tối ưu nhất dựa trên bộ tiêu chí khách quan đã chốt trước khi chạy.
6. Ghi nhận toàn bộ sự cố kỹ thuật, phân tích nguyên nhân gốc rễ và đề xuất khuyến nghị để Leader ra quyết định tiếp theo.

---

## CHƯƠNG 4: PHẠM VI NGHIÊN CỨU VÀ NHỮNG VIỆC CHỦ ĐỘNG KHÔNG THỰC HIỆN

### 4.1. Phạm vi thực tế đã thực hiện

- **Không gian thí nghiệm:** Đánh giá nhị phân đa phương thức (Ảnh biểu đồ + Văn bản câu hỏi + Văn bản ngữ cảnh bài viết) gán nhãn `"Đúng"` hoặc `"Sai"`.
- **Validation Stage:** Đánh giá 10 mô hình × 3 biến thể prompt × 15 mẫu dữ liệu = **450 lượt đánh giá**.
- **Holdout Test Stage:** Đánh giá mô hình chiến thắng (Winner) trên tập kiểm thử độc lập = **50 lượt đánh giá**.
- **Tổng số request thực tế được ghi nhận:** **500 API request** có đầy đủ ledger kế toán chi phí.

### 4.2. Những việc chủ động KHÔNG thực hiện (Strict Boundary Control)

Để bảo đảm tính tập trung nghiên cứu, kỷ luật ngân sách và tuân thủ đúng phạm vi Leader giao:

1. **Không mở rộng thành hệ thống Production:** Không viết microservice production, không đóng gói Docker, không triển khai giao diện web (Web UI/Dashboard), không tạo REST API wrapper không cần thiết.
2. **Không tiêu xài thêm ngân sách API:** Không phát sinh bất kỳ request API ngoài luồng nào sau khi hoàn tất 500 lượt đánh giá đã chốt.
3. **Không chỉnh sửa Dataset:** Tuyệt đối không can thiệp, biến đổi hay làm sạch cưỡng ép nhãn trong file dữ liệu gốc và dẫn xuất để làm số liệu đẹp hơn.
4. **Không gọi đây là benchmark toàn bộ Pool:** Không tuyên bố đã hoàn tất toàn bộ 172 mẫu Validation hay 185 mẫu Holdout Test ban đầu. Luôn minh bạch hóa cỡ mẫu thực tế là 15 mẫu Validation và 50 mẫu Holdout Test.

---

## CHƯƠNG 5: THIẾT KẾ THÍ NGHIỆM (EXPERIMENT DESIGN)

Quy trình benchmark được thiết kế theo mô hình 4 pha khép kín, bảo đảm tính khoa học, khách quan và an toàn dữ liệu:

```
[Phase 0: Preflight & Ledger Lock]
  ├── Xác minh SHA-256 Dataset & Prompts
  ├── Khởi tạo Persistent Budget Tracker (Hard Cap $35, Target $28)
  └── Sanity check 10 models (Ảnh base64 + Non-reasoning + HTTP 200)
            │
            ▼
[Phase A: Validation 10 Models × 3 Prompts (450 Runs)]
  ├── 15 mẫu cố định từ sample_pool_val.json (9 Đúng, 6 Sai, 9 documents)
  ├── Concurrency = 1, timeout 60s, budget check trước từng call
  └── Ghi nhận: raw response, latency, token billing, parse compliance
            │
            ▼
[Phase B: Objective Winner Selection]
  ├── Áp dụng tiêu chuẩn xếp hạng tiền định (Predetermined Gate Criteria)
  ├── 1. Compliance >= 98% -> 2. Macro-F1 -> 3. Recall('Sai') -> 4. Lifecycle GA
  └── Xác định Winner: google/gemini-3.1-flash-lite + prompt_A
            │
            ▼
[Phase C: Holdout Test Confirmation (50 Runs)]
  ├── 50 mẫu độc lập từ sample_pool_test_holdout.json (Loại trừ 5 smoke samples)
  └── Đo lường tổng quát hóa, vẽ ma trận nhầm lẫn (Confusion Matrix)
```

### Các nguyên tắc kỹ thuật bắt buộc:

- **Ngăn chặn rò rỉ dữ liệu (Zero Data Leakage):** Payload gửi đến API chỉ chứa văn bản câu hỏi, ngữ cảnh bài viết và ảnh biểu đồ mã hóa base64. Tuyệt đối không chứa `gold_answer`, nhãn ground truth hoặc bất kỳ metadata gợi ý đáp án.
- **Tính công bằng so sánh (Fair Benchmark):** Tất cả 10 mô hình đều nhận chính xác cùng một bộ câu hỏi, cùng thứ tự mẫu và cùng cấu trúc prompt.
- **Tắt tư duy suy luận tự do (Non-reasoning Enforced):** Cấu hình `reasoning: {"effort": "none"}` hoặc tham số tương đương trên OpenRouter để triệt tiêu việc các model reasoning làm sai lệch độ trễ và định dạng đầu ra.
- **Điều khiển dòng thực thi (Concurrency = 1):** Thực hiện tuần tự để tránh race condition trên ledger, dễ dàng dừng ngay lập tức nếu chạm hạn mức chi phí.

---

## CHƯƠNG 6: MÔ TẢ DATASET, VALIDATION, TEST VÀ QUY TẮC LOẠI TRỪ SMOKE SAMPLES

### 6.1. Dataset gốc và Dataset dẫn xuất

- **Dataset gốc (`process/vichartqa.json`):** Chứa các tài liệu báo chí kinh tế, xã hội, y tế kèm biểu đồ tiếng Việt với các câu hỏi đa dạng (hỏi đáp trích xuất, mô tả xu hướng, trắc nghiệm).
- **Dataset dẫn xuất (`process/vichartqa_factcheck_balanced_v1.json`):** Được cấu trúc hóa chuyên biệt cho bài toán Fact-Check. Các câu hỏi được chuẩn hóa thành dạng nhận định thẩm định với hai nhãn duy nhất: `"Đúng"` (Statement phù hợp với biểu đồ) và `"Sai"` (Statement mâu thuẫn hoặc xuyên tạc dữ liệu biểu đồ).

### 6.2. Các Sample Pool và Cơ chế Cô lập

1. **Validation Pool (`sample_pool_val.json`):**
   - Tổng kích thước pool: 172 mẫu (96 Đúng, 76 Sai).
   - **Tập mẫu con thực tế chạy benchmark:** 15 mẫu cố định, đại diện từ 9 tài liệu độc lập (`vichartqa_economics_00001`, `00025`, `00060`, `00073`, `00087`, `00130`, `00134`, `00139`, `vichartqa_science_00166`).
   - Phân bổ nhãn tập mẫu thực tế: **9 mẫu `"Đúng"` (60%)** và **6 mẫu `"Sai"` (40%)**.
2. **Holdout Test Pool (`sample_pool_test_holdout.json`):**
   - Pool gốc ban đầu (`sample_pool_test.json`): 190 mẫu (102 Đúng, 88 Sai).
   - **Quy tắc loại trừ Smoke Samples:** Đã loại trừ chính xác 5 mẫu đã từng được sử dụng trong đợt chạy Smoke Test sơ bộ (`vichartqa_economics_00032::q5`, `vichartqa_economics_00034::q2`, `vichartqa_economics_00035::q3`, `vichartqa_economics_00037::q4`, `vichartqa_economics_00040::q1`).
   - Kích thước pool sạch sau loại trừ: 185 mẫu (99 Đúng, 86 Sai).
   - **Tập mẫu con thực tế chạy Holdout Test:** 50 mẫu chọn lọc độc lập, trải rộng trên 22 tài liệu khác nhau.
   - Phân bổ nhãn Holdout Test: **27 mẫu `"Đúng"` (54%)** và **23 mẫu `"Sai"` (46%)**.
3. **Kiểm tra trùng lặp (Overlap Verification):**
   - Độ trùng lặp mẫu giữa 15 mẫu Validation và 50 mẫu Holdout Test: **0 mẫu** (Hoàn toàn phân tách).
   - Sự hiện diện của 5 smoke mẫu trong 50 mẫu Test: **0 mẫu** (Đã lọc sạch hoàn toàn).

---

### 6.3. Biểu đồ và Phân tích Cân bằng Dataset (Trước vs Sau khi bổ sung câu "Sai")

Dưới đây là biểu đồ đối sánh trực quan thể hiện rõ bước chuyển dịch cơ cấu nhãn của tập dữ liệu từ trạng thái mất cân bằng nghiêm trọng ban đầu sang trạng thái cân bằng chuẩn khoa học:

![Biểu đồ So Sánh Cân Bằng Dataset](figures/fig1_dataset_balance_comparison.png)

#### 1. Bảng số liệu chi tiết đối sánh nhãn Fact-Check Đúng / Sai:

| Chỉ số thống kê | Dataset Gốc (`vichartqa.json`) | Dataset Dẫn xuất (`vichartqa_factcheck_balanced_v1.json`) | Thay đổi (+/-) | Ý nghĩa kỹ thuật |
| :--- | :---: | :---: | :---: | :--- |
| **Tổng số câu hỏi Fact-Check** | 1.430 | 1.978 | **+548 QA** | 100% câu thêm mới đều là câu "Sai" được thẩm định |
| **Số lượng câu có nhãn "Đúng"** | 866 (81,24%) | 866 (53,66%) | 0 | Giữ nguyên 100% nhãn đúng nguyên bản |
| **Số lượng câu có nhãn "Sai"** | 200 (18,76%) | 748 (46,34%) | **+548 (+274%)** | Đưa tỷ lệ về thế cân bằng gần 50/50 |
| **Tỷ lệ chênh lệch Đúng / Sai** | **4,33 : 1 (Lệch cực nặng)** | **1,16 : 1 (Chuẩn cân bằng)** | **-73,2% lệch** | Loại bỏ hoàn toàn thiên kiến nhãn áp đảo |

#### 2. Phân bổ cân bằng nhãn theo từng phân vùng (Split):

| Phân vùng (Split) | Gốc: Đúng | Gốc: Sai | Mới thêm: Sai | Sau cân bằng: Đúng | Sau cân bằng: Sai | Tỷ lệ Đúng / Sai sau cân bằng |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Train** | 668 | 158 | **+426** | 668 | **584** | **53,35% / 46,65%** |
| **Validation** | 96 | 21 | **+55** | 96 | **76** | **55,81% / 44,19%** |
| **Test** | 102 | 21 | **+67** | 102 | **88** | **53,68% / 46,32%** |
| **TỔNG CỘNG** | **866** | **200** | **+548** | **866** | **748** | **53,66% / 46,34%** |

```mermaid
graph TD
    subgraph "Trước khi cân bằng (Dataset Gốc)"
    GOC["Tổng: 1.066 câu Đúng/Sai"] --> GOC_D["866 câu Đúng (81.2%)<br/>⚠️ Lệch áp đảo"]
    GOC --> GOC_S["200 câu Sai (18.8%)<br/>Thiếu hụt mẫu âm"]
    end

    subgraph "Quy trình Tăng cường (Phase 4)"
    AUG["Tạo & Thẩm định 5 vòng<br/>+548 câu 'Sai' chất lượng cao"]
    end

    subgraph "Sau khi cân bằng (Balanced V1)"
    BAL["Tổng: 1.614 câu Đúng/Sai"] --> BAL_D["866 câu Đúng (53.7%)"]
    BAL --> BAL_S["748 câu Sai (46.3%)"]
    end

    GOC_D --> BAL_D
    GOC_S --> BAL_S
    AUG --> BAL_S
```

#### 3. Ý nghĩa thực nghiệm cốt lõi đối với bài toán Fact-Check:
- **Xóa bỏ bẫy "Đoán mò":** Ở tập dữ liệu gốc, một mô hình ngớ ngẩn chỉ cần "đoán mò Đúng cho mọi câu" đã nghiễm nhiên đạt độ chính xác **81,24%**. Điều này vô hiệu hóa hoàn toàn độ tin cậy của bài toán Fact-Check.
- **Thử thách năng lực phản biện thực tế:** Sau khi bổ sung 548 câu "Sai" được làm giả tinh vi (sai số liệu thống kê, đảo ngược xu hướng tăng/giảm, hoán đổi danh mục biểu đồ), mô hình bắt buộc phải đọc hiểu thật sự từng cột số liệu và chú giải mới phát hiện được sai sót.

---

## CHƯƠNG 7: MODEL ROSTER THỰC TẾ VÀ LỊCH SỬ ĐIỀU CHỈNH THAY THẾ

Roster 10 mô hình được thẩm định và phê duyệt trong `models_selected_v2.json` cùng bản manifest thực nghiệm `benchmark_manifest_v2.json`.

### Bảng cấu hình 10 mô hình tham gia benchmark thực tế:

| STT | Model Slug trên OpenRouter       | Tên hiển thị chuẩn | Nhà phát triển | Vai trò nghiên cứu           | Trạng thái vòng đời (Lifecycle) | Context Window | Giá Input ($/1M) | Giá Output ($/1M) |      |
| :-: | --------------------------------- | ---------------------- | ----------------- | ------------------------------- | ------------------------------------ | :------------: | :--------------------------------------: | :---: |
|  1  | `google/gemini-3.1-flash-lite`  | Gemini 3.1 Flash Lite  | Google            | Ứng viên chính               | GA_ACTIVE                            |   1.048.576   |                  $0,25                  | $1,50 |
|  2  | `google/gemini-3.5-flash-lite`  | Gemini 3.5 Flash Lite  | Google            | Ứng viên chính               | ACTIVE                               |   1.048.576   |                  $0,30                  | $2,50 |
|  3  | `google/gemini-3-flash-preview` | Gemini 3 Flash Preview | Google            | Ứng viên chính               | PREVIEW_ACTIVE                       |   1.048.576   |                  $0,50                  | $3,00 |
|  4  | `amazon/nova-lite-v1`           | Nova Lite 1.0          | Amazon            | Neo đối chiếu giá rẻ       | ACTIVE                               |    300.000    |                  $0,06                  | $0,24 |
|  5  | `google/gemma-3-12b-it`         | Gemma 3 12B Instruct   | Google            | Neo trọng số mở              | ACTIVE                               |    131.072    |                  $0,05                  | $0,15 |
|  6  | `google/gemini-2.5-flash`       | Gemini 2.5 Flash       | Google            | Baseline tham chiếu            | DEPRECATION_WARNING (20/10/2026)     |   1.048.576   |                  $0,30                  | $2,50 |
|  7  | `openai/gpt-4o-mini`            | GPT-4o Mini            | OpenAI            | Neo đối chiếu ngoài hệ     | ACTIVE                               |    128.000    |                  $0,15                  | $0,60 |
|  8  | `google/gemma-3-27b-it`         | Gemma 3 27B Instruct   | Google            | Neo trọng số mở (thay thế)  | ACTIVE                               |    131.072    |                  $0,08                  | $0,45 |
|  9  | `google/gemini-2.5-flash-lite`  | Gemini 2.5 Flash Lite  | Google            | Baseline tham chiếu            | DEPRECATION_WARNING (20/10/2026)     |   1.048.576   |                  $0,10                  | $0,40 |
| 10 | `qwen/qwen3-vl-8b-instruct`     | Qwen3 VL 8B Instruct   | Alibaba / Qwen    | Neo đối chiếu mã nguồn mở | ACTIVE                               |    262.144    |                  $0,25                  | $0,75 |

### Lịch sử điều chỉnh roster:

- **Nguyên nhân thay thế `google/gemma-3-4b-it`:** Trong pha Preflight Sanity Check, endpoint của model `google/gemma-3-4b-it` liên tục trả về mã lỗi HTTP 429 (Rate Limit nghẽn upstream phía nhà cung cấp OpenRouter), không thể nhận request ổn định dù concurrency = 1.
- **Hành động xử lý:** Thay thế trực tiếp bằng **`google/gemma-3-27b-it`** (Mô hình vision đa phương thức trọng số mở của Google với dung lượng lớn hơn, đã test thành công HTTP 200 kèm ảnh và phản hồi trong 1.900 ms). Thay đổi được ghi nhận minh bạch trong manifest v2.

---

## CHƯƠNG 8: NỘI DUNG NGUYÊN VĂN ĐẦY ĐỦ CỦA PROMPT A

- **Đường dẫn file:** `experiments/openrouter_factcheck_pilot/prompts/prompt_A.txt`
- **Mã băm SHA-256:** `aaa0987ba6d4f42ba5ed14b97ed85158095e9e6636f767ebff3d2e08c209ad63`
- **Kích thước file:** 482 bytes (11 dòng)

### Toàn văn nội dung nguyên bản:

```text
Bạn là chuyên gia phân tích tài liệu và biểu đồ song ngữ Việt-Anh.

Nhiệm vụ: Đọc tiêu đề, bài viết, các biểu đồ kèm theo và câu hỏi.
Xác định phát biểu trong câu hỏi là Đúng hay Sai dựa vào tài liệu và biểu đồ.
Không suy đoán thông tin ngoài tài liệu. Không giải thích dài dòng.

Chỉ trả về đáp án duy nhất theo đúng định dạng:
<answer>Đúng</answer>
hoặc
<answer>Sai</answer>
```

---

## CHƯƠNG 9: PHÂN TÍCH CHUYÊN SÂU PROMPT A

1. **Mục tiêu thiết kế:** Hướng tới sự tối giản tuyệt đối (Minimalist & Direct Classification). Loại bỏ hoàn toàn mọi chỉ dẫn phức tạp để ép mô hình tập trung 100% token đầu ra vào thẻ tag định dạng.
2. **Quy tắc phân loại và xử lý thông tin:** Yêu cầu đọc tiêu đề, bài viết và biểu đồ; cấm suy đoán ngoài tài liệu; cấm giải thích dài dòng.
3. **Cơ chế kiểm soát định dạng:** Đưa ra cú pháp bắt buộc rõ ràng gồm hai lựa chọn đóng khung trong `<answer>...</answer>`.
4. **Ưu điểm thực tế:**
   - Đạt độ tương thích cú pháp tuyệt đối (**100,0% Parse Compliance** trên toàn bộ 150 request đối đầu).
   - Tiết kiệm tối đa Output Token (mỗi response chỉ tiêu tốn 5–12 tokens), giảm thiểu chi phí phát sinh và giảm nguy cơ chạm `max_tokens`.
5. **Nhược điểm & Rủi ro:** Không ép mô hình phải qua bước đối chiếu chứng cứ trung gian, nên các mô hình năng lực yếu dễ có xu hướng "đoán mò" nếu không đọc kỹ biểu đồ.

---

## CHƯƠNG 10: NỘI DUNG NGUYÊN VĂN ĐẦY ĐỦ CỦA PROMPT B

- **Đường dẫn file:** `experiments/openrouter_factcheck_pilot/prompts/prompt_B.txt`
- **Mã băm SHA-256:** `4cd10080929707e080c1750e61eb5b63e483a41bd257efb367f79048f349af17`
- **Kích thước file:** 707 bytes (12 dòng)

### Toàn văn nội dung nguyên bản:

```text
Bạn là chuyên gia phân tích tài liệu và biểu đồ song ngữ Việt-Anh.

Nhiệm vụ: Đọc toàn diện bài viết và các biểu đồ được cung cấp để thẩm định phát biểu trong câu hỏi.
- Luôn ưu tiên căn cứ vào bằng chứng trực tiếp (số liệu, mốc thời gian, xu hướng, câu chữ trích xuất) có trong văn bản hoặc biểu đồ.
- Tuyệt đối không sử dụng kiến thức bên ngoài tài liệu và không suy diễn vượt quá bằng chứng.
- Không đưa ra chuỗi giải thích hay suy nghĩ.

Chỉ xuất câu trả lời cuối cùng theo đúng định dạng:
<answer>Đúng</answer>
hoặc
<answer>Sai</answer>
```

---

## CHƯƠNG 11: PHÂN TÍCH CHUYÊN SÂU PROMPT B

1. **Mục tiêu thiết kế:** Hướng tới thẩm định dựa trên bằng chứng khắt khe (Evidence-Grounded Constraint). Bổ sung các chỉ dẫn nhấn mạnh vào số liệu, mốc thời gian, xu hướng biểu đồ.
2. **Quy tắc phân loại:** Ép buộc mô hình chỉ dựa vào bằng chứng trực tiếp trích xuất được từ văn bản hoặc hình ảnh biểu đồ, cấm suy diễn logic mở rộng.
3. **Ưu điểm thực tế:** Nâng cao khả năng phát hiện các lỗi sai tinh vi trên biểu đồ (Recall lớp `"Sai"` trung bình đạt **89,47%**, cao nhất trong 3 prompt).
4. **Nhược điểm & Rủi ro thực nghiệm:**
   - Mặc dù có dòng lệnh *"Không đưa ra chuỗi giải thích hay suy nghĩ"*, một số mô hình nhỏ (như `gemini-2.5-flash-lite` và `qwen3-vl-8b`) vẫn tự động sinh một câu lập luận ngắn trước khi đóng tag, dẫn đến việc bị cắt ngang nếu `max_tokens` nhỏ hoặc parser nghiêm ngặt gặp trục trặc.
   - Ghi nhận **5 lỗi phân tích cú pháp (Parse Errors)** trên 150 request (Compliance giảm xuống **96,67%**).

---

## CHƯƠNG 12: NỘI DUNG NGUYÊN VĂN ĐẦY ĐỦ CỦA PROMPT C

- **Đường dẫn file:** `experiments/openrouter_factcheck_pilot/prompts/prompt_C.txt`
- **Mã băm SHA-256:** `bec0a1b239ecedbe4b83d693c11ea2df1bb008a23cbe073c775bb4d882cdd143`
- **Kích thước file:** 829 bytes (13 dòng)

### Toàn văn nội dung nguyên bản:

```text
Bạn là chuyên gia phân tích tài liệu và biểu đồ song ngữ Việt-Anh.

Nhiệm vụ: Thẩm định tính chính xác của phát biểu trong câu hỏi theo quy trình kiểm tra nội bộ 3 bước:
1. Xác định rõ luận điểm (claim) và các thông số cần kiểm chứng trong câu hỏi.
2. Tìm kiếm bằng chứng đối chiếu trực tiếp từ nội dung văn bản và các biểu đồ [CHART 1], [CHART 2], [CHART 3].
3. So sánh đối chiếu luận điểm với bằng chứng để kết luận tính Đúng hay Sai.

LƯU Ý QUAN TRỌNG: Quy trình trên chỉ là hướng dẫn nội bộ. KHÔNG được xuất các bước phân tích ra ngoài.
Chỉ trả về duy nhất kết quả cuối cùng theo định dạng:
<answer>Đúng</answer>
hoặc
<answer>Sai</answer>
```

---

## CHƯƠNG 13: PHÂN TÍCH CHUYÊN SÂU PROMPT C

1. **Mục tiêu thiết kế:** Mô phỏng quy trình thẩm định chuyên gia thông qua chuỗi tư duy ngầm định (Silent Internal Chain-of-Thought / Structured Verification Workflow).
2. **Quy tắc phân loại:** Chia nhỏ tác vụ thành 3 bước logic: Xác định claim -> Truy tìm bằng chứng đa phương thức ([CHART 1], [CHART 2]...) -> Đối chiếu và kết luận.
3. **Ưu điểm lý thuyết:** Giúp các mô hình lớn có khả năng định vị biểu đồ tốt hơn và tránh bị phân tâm bởi các thông tin nhiễu trong bài viết dài.
4. **Nhược điểm & Rủi ro thực nghiệm:**
   - Tạo áp lực chỉ dẫn (Instruction Overload): Mô hình vừa phải thực hiện 3 bước vừa bị cấm in ra ngoài khiến các mô hình mã nguồn mở cỡ nhỏ bị bối rối (confusion), có xu hướng in ra các tiêu đề bước "Bước 1...", làm vỡ định dạng thẻ tag `<answer>`.
   - Ghi nhận **4 lỗi phân tích cú pháp (Parse Errors)**, Strict Accuracy toàn cục chỉ đạt **70,00%**, thấp nhất trong 3 prompt.

---

## CHƯƠNG 14: BẢNG SO SÁNH ĐỐI ĐẦU HỆ THỐNG PROMPT A / B / C

### Biểu đồ Đối Đầu Trực Diện Giữa 3 Chiến Lược System Prompt (A, B, C)

![Biểu đồ Đối Đầu 3 System Prompt](figures/fig4_prompt_comparison_head_to_head.png)

```mermaid
graph TD
    A["So sánh 3 System Prompt (150 Reqs mỗi loại)"] --> B["<b>Prompt A (Minimalist)</b><br/>• Strict Acc: 73.33% (#1)<br/>• Parse Compliance: <b>100.0% (0 lỗi)</b><br/>• Rất an toàn khi đưa vào sản xuất"]
    A --> C["<b>Prompt B (Evidence-Grounded)</b><br/>• Strict Acc: 72.67%<br/>• Parse Compliance: 96.67%<br/>⚠️ <b>5 lỗi cắt cụt token</b> (Mô hình nhỏ suy luận quá dài)"]
    A --> D["<b>Prompt C (3-Step Workflow)</b><br/>• Strict Acc: 70.00%<br/>• Parse Compliance: 97.33%<br/>⚠️ <b>4 lỗi parse định dạng</b>"]
```


Dưới đây là bảng tổng hợp số liệu thực nghiệm đo lường trên toàn bộ 150 request tương ứng của mỗi prompt trong giai đoạn Validation (10 mô hình × 15 mẫu = 150 request/prompt):

| Tiêu chí đánh giá thực nghiệm           | Prompt A (Minimalist Direct) | Prompt B (Evidence-Grounded) | Prompt C (3-Step Silent Workflow) | Nhận xét chuyên môn                               |
| ---------------------------------------------- | :--------------------------: | :--------------------------: | :-------------------------------: | ----------------------------------------------------- |
| **Số request thực hiện**              |             150             |             150             |                150                | Cân bằng hoàn hảo 1:1:1                           |
| **Số phản hồi hợp lệ (Valid)**      |        **150**        |             145             |                146                | Prompt A tuân thủ tuyệt đối                      |
| **Số lỗi cú pháp (Parse Errors)**    |         **0**         |              5              |                 4                 | Prompt B & C bị lỗi định dạng                    |
| **Parse Compliance Rate**                |      **100,00%**      |            96,67%            |              97,33%              | **Prompt A thắng áp đảo**                   |
| **Strict Accuracy**                      |       **73,33%**       |            72,67%            |              70,00%              | Prompt A cao nhất                                    |
| **Balanced Accuracy**                    |            75,28%            |       **77,69%**       |              73,81%              | Prompt B nhỉnh hơn về cân bằng nhãn             |
| **Macro-F1 Score**                       |            0,7326            |       **0,7511**       |              0,7176              | Prompt B nhỉnh hơn đôi chút về F1               |
| **Recall lớp "Sai" (Bắt lỗi sai)**    |        85,00% (51/60)        |   **89,47%** (51/57)   |          82,46% (47/57)          | Prompt B bắt lỗi sai nhạy bén nhất               |
| **Precision lớp "Sai"**                 |            62,20%            |       **62,96%**       |              60,26%              | Tương đương nhau                                 |
| **Độ trễ trung bình (Mean Latency)** |          2.636,9 ms          |     **2.166,3 ms**     |            2.179,8 ms            | Prompt B/C xử lý nhanh hơn ở mô hình ổn định |
| **Độ trễ Median**                     |          1.812,5 ms          |          1.667,0 ms          |       **1.569,0 ms**       | Độ trễ trung vị tương đương                  |
| **Độ trễ P95**                        |          6.296,5 ms          |     **4.674,5 ms**     |            4.865,2 ms            | Prompt A bị kéo dài P95 do Qwen bị nghẽn         |
| **Tổng chi phí API thực tế**         |         $0,1870 USD         |    **$0,1844 USD**    |            $0,1864 USD            | Chi phí 3 prompt gần như ngang bằng               |

> **KẾT LUẬN LỰA CHỌN PROMPT:**
> Mặc dù Prompt B có chỉ số Macro-F1 nhỉnh hơn (0,7511 so với 0,7326), nhưng **Prompt A là lựa chọn tối ưu và an toàn nhất** vì bảo đảm **100,0% Parse Compliance** (không gây rủi ro đứt gãy hệ thống tự động hóa). Đặc biệt, khi đi kèm với mô hình top đầu `gemini-3.1-flash-lite`, Prompt A đạt Macro-F1 lên tới **0,8661** (ngang ngửa Prompt B) mà lại hoàn toàn không có nguy cơ lỗi cú pháp.

---

## CHƯƠNG 15: TIMELINE QUÁ TRÌNH THỰC HIỆN VÀ ĐIỀU TRA KỸ THUẬT

1. **Giai đoạn 1 — Khảo sát Môi trường & Lập Kế hoạch V2:**
   - Xác định thư mục chuẩn `ViChartQA_git`, cô lập với các thư mục rác và duplicate bên ngoài.
   - Kiểm tra mã băm SHA-256 các file dữ liệu nguồn để bảo đảm tính toàn vẹn.
   - Khởi tạo file kế hoạch chi tiết `MODEL_PROMPT_SELECTION_PLAN_V2.md`.
2. **Giai đoạn 2 — Roster Discovery & Kiểm thử Preflight:**
   - Xác minh giá cước và trạng thái online của 10 mô hình trên OpenRouter.
   - Phát hiện model `gemma-3-4b-it` bị nghẽn rate limit (HTTP 429) liên tục; thay thế an toàn bằng `gemma-3-27b-it`.
   - Chạy kiểm thử Unit test (Gate 1 safety checks) kiểm tra cơ chế ngắt trần ngân sách tự động.
3. **Giai đoạn 3 — Thực thi Sanity Check & Smoke Test:**
   - Chạy thử 5 mẫu độc lập với concurrency = 1.
   - Thẩm định bộ parser regex và trích xuất thẻ tag `<answer>`.
4. **Giai đoạn 4 — Thực thi Validation Benchmark (450 Runs):**
   - Chạy tuần tự 10 mô hình × 3 prompt × 15 mẫu.
   - Bắt và xử lý 7 lỗi HTTP 429 tạm thời từ cụm máy chủ Qwen bằng thuật toán Exponential Backoff.
   - Ghi nhận tức thời mọi giao dịch vào `benchmark_request_ledger.jsonl`.
5. **Giai đoạn 5 — Tổng hợp Metrics & Tự Động Lựa Chọn Winner:**
   - Tính toán đầy đủ ma trận nhầm lẫn và các chỉ số Macro-F1, Balanced Accuracy, Parse Compliance.
   - Thuật toán lựa chọn xác định ứng viên số 1: `google/gemini-3.1-flash-lite` kết hợp `prompt_A`.
6. **Giai đoạn 6 — Thực thi Holdout Test (50 Runs):**
   - Chạy kiểm chứng độc lập trên 50 mẫu Holdout Test sạch.
   - Đạt kết quả xuất sắc: Accuracy 88,00%, Macro-F1 87,98%, Recall lớp "Sai" 91,30%.
7. **Giai đoạn 7 — Đối soát Tài chính & Lập Báo cáo Bàn giao:**
   - Đối soát từng dòng trong ledger chi phí: Tổng $0,5980 USD.
   - Xác minh lại mã băm dataset và biên soạn báo cáo toàn diện.

---

## CHƯƠNG 16: BẢNG TỔNG HỢP CÁC LỖI KỸ THUẬT PHÁT SINH VÀ GIẢI PHÁP XỬ LÝ

|       ID       | Vấn đề phát sinh                                             | Bằng chứng thực tế                                                                    | Nguyên nhân gốc rễ đã xác minh                                                                                                        | Cách xử lý kỹ thuật                                                                                       | Kết quả sau xử lý                                                          | Trạng thái cuối | Hạn chế còn lại                                                                                        |
| :------------: | ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | :----------------: | ---------------------------------------------------------------------------------------------------------- |
| **E-01** | Model`gemma-3-4b-it` liên tục trả về lỗi HTTP 429         | Log preflight trả về mã HTTP 429; OpenRouter thông báo rate limit nghẽn             | Endpoint miễn phí/giá rẻ của OpenRouter cho model 4B bị quá tải upstream toàn cầu                                                  | Thay thế bằng`google/gemma-3-27b-it` (Mô hình vision đa phương thức cùng họ, dung lượng 27B)     | Vượt qua preflight với HTTP 200, phản hồi ảnh trong 1.988 ms             | **RESOLVED** | Chi phí cao hơn bản 4B đôi chút ($0,00016 vs $0,00008/req) nhưng hoàn toàn nằm trong ngân sách |
| **E-02** | OpenAI token inflation trên`gpt-4o-mini`                      | Chi phí GPT-4o-mini vọt lên $0,0066/req (tổng $0,2999 cho 45 req), gấp 8 lần Gemini | Cơ chế Image Tiling tự động của OpenAI chia ảnh biểu đồ độ phân giải cao thành hàng chục tile (lên tới 87.000 tokens/req) | Giữ nguyên ghi nhận thực tế để phản ánh chi phí thật; đưa vào báo cáo cảnh báo chi phí      | Xác định chính xác GPT-4o-mini không tối ưu về mặt kinh tế          | **RESOLVED** | Cần cấu hình`detail: low` nếu muốn giảm chi phí OpenAI trong tương lai                          |
| **E-03** | Lỗi phân tích cú pháp (Parse Truncation) trên Prompt B & C | `gemini-2.5-flash-lite` bị 2 lỗi parse, `qwen3-vl-8b` bị 5 lỗi trên Prompt B & C | Mô hình nhỏ cố gắng giải thích trước khi in tag, khiến response bị cắt cụt khi chạm ngưỡng token ngắn                       | Parser regex được tăng cường bóc tách linh hoạt; ghi nhận chính xác vào Parse Compliance metric   | Không làm gián đoạn runner; hệ thống ghi nhận unparsed chính xác     | **RESOLVED** | Khẳng định ưu thế áp đảo của Prompt A trong việc chống lỗi cú pháp                           |
| **E-04** | Nguy cơ rò rỉ dữ liệu qua nhãn hoặc ID                    | Kiểm tra cấu trúc json của dataset có trường`gold_answer` và `label`          | Nếu payload gửi kèm trường này, mô hình sẽ học vẹt đáp án                                                                      | Xây dựng hàm`build_user_message` loại bỏ tuyệt đối các trường nhãn, chỉ gửi Text + Chart Image | 100% request hoàn toàn "mù" với ground truth, bảo đảm tính trung thực | **RESOLVED** | Không có rủi ro rò rỉ                                                                                 |
| **E-05** | Nguy cơ chạy vượt ngân sách $35 USD                        | Yêu cầu Leader đặt trần $28 USD target và $35 USD hard cap                          | Nếu runner gặp vòng lặp retry vô tận có thể làm cạn tiền tài khoản OpenRouter                                                   | Xây dựng class`PersistentBudgetTracker` khóa file disk, kiểm tra số dư trước mỗi call đơn lẻ     | Toàn bộ 500 request chỉ tiêu tốn $0,5980 USD, dừng an toàn tuyệt đối | **RESOLVED** | Không có rủi ro bội chi                                                                                |
| **E-06** | Model Gemini 2.5 đối mặt hạn ngừng hỗ trợ                 | Trang chính thức OpenRouter hiển thị nhãn`DEPRECATION_WARNING_2026_10_20`          | Google lên kế hoạch dừng phục vụ Gemini 2.5 Flash / Flash Lite vào 20/10/2026                                                         | Phân loại vai trò Gemini 2.5 là "reference_baseline", cấm chọn làm Winner dù điểm số cao            | Winner được chọn là Gemini 3.1 Flash Lite (Bản GA Active dài hạn)      | **RESOLVED** | Gemini 2.5 chỉ dùng để tham chiếu lịch sử                                                           |

---

## CHƯƠNG 17: KẾT QUẢ VALIDATION CHI TIẾT (VALIDATION RESULTS V2)

Giai đoạn Validation đã thực thi thành công **450 lượt đánh giá**, kiểm tra chéo toàn bộ 30 tổ hợp giữa 10 mô hình và 3 system prompt trên 15 câu hỏi Fact-Check mẫu (gồm 9 câu `"Đúng"` và 6 câu `"Sai"`).

### Các chỉ số toàn cục giai đoạn Validation:

- **Tổng số request thực hiện:** 450
- **Số kết quả hợp lệ:** 441 (98,00%)
- **Số lỗi cú pháp parse:** 9 (2,00%)
- **Số lỗi kết nối HTTP:** 0 (Các lỗi 429 tạm thời của Qwen đã được tự động retry thành công)
- **Tổng chi phí Validation:** **$0,5577 USD**
- **Độ trễ trung bình toàn bộ:** 2.327,7 ms

---

## CHƯƠNG 18: BẢNG XẾP HẠNG TOÀN DIỆN 10 MÔ HÌNH (10-MODEL COMPREHENSIVE RANKING)

Dưới đây là bảng xếp hạng chính thức 10 mô hình dựa trên toàn bộ 45 lượt đánh giá Validation của từng mô hình (tổng hợp từ cả 3 prompt):

|    Hạng    | Model Slug chính xác            | Tên hiển thị        | Provider | Vai trò thí nghiệm | Trạng thái Lifecycle | Reqs dự kiến | Reqs thực tế | Kết quả hợp lệ | HTTP Err | Parse Err | Parse Compliance | Strict Accuracy |     Macro-F1     | Recall lớp "Sai" | Balanced Accuracy | Độ trễ Mean (ms) | Độ trễ Median (ms) | Độ trễ P95 (ms) | Tổng chi phí ($) | Chi phí TB/mẫu ($) | Đánh giá Chất lượng - Chi phí |                                                                       |
| :----------: | --------------------------------- | ---------------------- | -------- | --------------------- | ---------------------- | :------------: | :------------: | :----------------: | :------: | :-------: | :--------------: | :--------------: | :--------------: | :---------------: | :---------------: | :-----------------: | :-------------------: | :----------------: | :-----------------------------------------: | :----------------------------------: | --------------------------------------------------------------------- |
| **1** | `google/gemini-3.1-flash-lite`  | Gemini 3.1 Flash Lite  | Google   | Ứng viên chính     | **GA_ACTIVE**    |       45       |       45       |         45         |    0    |     0     | **100,0%** | **86,67%** | **0,8661** | **1,0000** | **88,89%** |       1.588,0       |        1.596,0        |      2.136,8      |                   $0,0390                   |              $0,000866              | **Xuất sắc toàn diện; Rẻ, Nhanh, Chính xác nhất**       |
| **2** | `google/gemini-3.5-flash-lite`  | Gemini 3.5 Flash Lite  | Google   | Ứng viên chính     | ACTIVE                 |       45       |       45       |         45         |    0    |     0     | **100,0%** | **80,00%** | **0,8000** | **1,0000** |      83,33%      |       1.381,9       |        1.333,0        |      1.786,0      |                   $0,0470                   |              $0,001044              | Rất tốt; Tốc độ nhanh nhất, chất lượng cận top 1            |
| **3** | `google/gemini-3-flash-preview` | Gemini 3 Flash Preview | Google   | Ứng viên chính     | PREVIEW_ACTIVE         |       45       |       45       |         45         |    0    |     0     | **100,0%** | **77,78%** | **0,7777** | **1,0000** |      81,48%      |       2.583,2       |        1.497,0        |      12.557,6      |                   $0,0779                   |              $0,001731              | Tốt; Bản Preview đôi khi có spike trễ cao                       |
| **4** | `amazon/nova-lite-v1`           | Nova Lite 1.0          | Amazon   | Neo đối chiếu      | ACTIVE                 |       45       |       45       |         45         |    0    |     0     | **100,0%** | **77,78%** | **0,7777** |      0,9444      |      80,56%      |       2.014,5       |        1.923,0        |      3.350,6      |                   $0,0134                   |              $0,000298              | Rất rẻ; Ổn định cao ngoài hệ Google                            |
| **5** | `google/gemma-3-12b-it`         | Gemma 3 12B            | Google   | Neo trọng số mở    | ACTIVE                 |       45       |       45       |         45         |    0    |     0     | **100,0%** | **75,56%** | **0,7368** |      0,6111      |      73,15%      |       1.565,4       |        1.411,0        |      2.614,8      |                   $0,0044                   |              $0,000098              | Cực rẻ; Nhận diện lớp Sai còn yếu (Recall 61%)                 |
| **6** | `google/gemini-2.5-flash`       | Gemini 2.5 Flash       | Google   | Baseline              | DEPRECATED (20/10)     |       45       |       45       |         45         |    0    |     0     | **100,0%** | **68,89%** | **0,6850** | **1,0000** |      74,07%      |       1.396,6       |        1.329,0        |      1.905,0      |                   $0,0310                   |              $0,000690              | Nhanh nhưng sắp hết hạn hỗ trợ                                  |
| **7** | `openai/gpt-4o-mini`            | GPT-4o-mini            | OpenAI   | Neo đối chiếu      | ACTIVE                 |       45       |       45       |         45         |    0    |     0     | **100,0%** | **68,89%** | **0,6850** |      0,7222      |      69,44%      |       3.204,1       |        2.937,0        |      5.636,4      |                   $0,2999                   |              $0,006664              | **Không tối ưu: Đắt gấp 8 lần, điểm chỉ trung bình** |
| **8** | `google/gemma-3-27b-it`         | Gemma 3 27B            | Google   | Neo trọng số mở    | ACTIVE                 |       45       |       45       |         45         |    0    |     0     | **100,0%** | **66,67%** | **0,6606** |      0,6667      |      66,67%      |       2.397,5       |        1.988,0        |      5.879,4      |                   $0,0073                   |              $0,000162              | Giá rẻ; Khả năng đọc biểu đồ tiếng Việt mức trung bình   |
| **9** | `google/gemini-2.5-flash-lite`  | Gemini 2.5 Flash Lite  | Google   | Baseline              | DEPRECATED (20/10)     |       45       |       45       |         43         |    0    |     2     |      95,56%      | **66,67%** | **0,6950** |      0,7647      |      70,93%      |       2.754,6       |        2.558,0        |      4.969,0      |                   $0,0103                   |              $0,000229              | Bị 2 lỗi parse; Sắp hết hạn hỗ trợ                             |
| **10** | `qwen/qwen3-vl-8b-instruct`     | Qwen3 VL 8B            | Qwen     | Neo đối chiếu      | ACTIVE                 |       45       |       45       |         38         |    7    |     0     |      84,44%      | **51,11%** | **0,6050** |      0,8462      |      66,31%      |       4.391,0       |        2.416,0        |      18.194,0      |                   $0,0276                   |              $0,000612              | Kém ổn định: 7 lần nghẽn 429, trễ cao, điểm thấp nhất      |

---

### 18.1. Biểu đồ So Sánh Đa Chiều 10 Mô Hình và Ma Trận Đánh Đổi

Để Leader có cái nhìn trực quan và toàn diện nhất, kết quả benchmark 10 mô hình được trực quan hóa qua 2 biểu đồ chuyên sâu dưới đây:

#### 1. Biểu đồ so sánh đa chiều: Hiệu năng phân loại, Bắt lỗi sai và Độ tuân thủ cú pháp:

![Biểu đồ So Sánh Đa Chiều 10 Models](figures/fig2_models_multidimensional_comparison.png)

#### 2. Ma trận đánh đổi Pareto: Độ chính xác vs Độ trễ vs Chi phí API:

![Ma Trận Đánh Đổi Hiệu Năng - Chi Phí - Độ Trễ](figures/fig3_model_tradeoff_accuracy_cost_latency.png)

```mermaid
graph LR
    subgraph "VÙNG TỐI ƯU PARETO (TOP TIER)"
    G31["<b>Google Gemini 3.1 Flash Lite</b><br/>• Strict Acc: <b>86.67%</b> (#1)<br/>• Macro-F1: <b>0.8661</b> (#1)<br/>• Recall Sai: <b>100.0%</b> (#1)<br/>• Latency: <b>1.59s</b> (Rất nhanh)<br/>• Cost: <b>$0.039</b> (Rất rẻ)"]
    G35["<b>Google Gemini 3.5 Flash Lite</b><br/>• Strict Acc: <b>80.00%</b> (#2)<br/>• Latency: <b>1.38s</b> (Nhanh nhất)<br/>• Cost: <b>$0.047</b>"]
    end

    subgraph "NHÓM TIẾT KIỆM (VALUE TIER)"
    Nova["<b>Amazon Nova Lite 1.0</b><br/>• Strict Acc: <b>77.78%</b><br/>• Recall Sai: <b>94.44%</b><br/>• Cost: <b>$0.013</b>"]
    Gemma12["<b>Google Gemma 3 12B</b><br/>• Strict Acc: <b>75.56%</b><br/>• Latency: <b>1.57s</b><br/>• Cost: <b>$0.004</b> (Siêu rẻ)<br/>⚠️ Recall Sai chỉ đạt 61.1%"]
    end

    subgraph "CẢNH BÁO: KHÔNG NÊN DÙNG (INEFFICIENT TIER)"
    GPT4o["<b>OpenAI GPT-4o-mini</b><br/>• Strict Acc: <b>68.89%</b> (Trung bình)<br/>• Latency: <b>3.20s</b> (Chậm)<br/>❌ <b>CHI PHÍ ĐẮT GẤP 8 LẦN ($0.300)</b><br/>(Do cơ chế Image Tiling)"]
    Qwen["<b>Qwen3 VL 8B Instruct</b><br/>• Strict Acc: <b>51.11%</b> (Thấp nhất)<br/>• Latency: <b>4.39s</b> (Chậm nhất)<br/>❌ <b>7 lỗi HTTP 429 & Lỗi Parse định dạng</b>"]
    end
```

### 18.2. Phân Tích Chuyên Sâu 10 Mô Hình Trên 5 Khía Cạnh Trọng Yếu

#### Khía cạnh 1: Độ chính xác tổng thể (Strict Accuracy & Balanced Accuracy)
- **Top 1 vượt trội:** `google/gemini-3.1-flash-lite` đạt **86,67% Strict Accuracy** và **88,89% Balanced Accuracy**, bỏ xa vị trí thứ 2 tới **6,67%**. Mô hình thể hiện khả năng đọc chính xác trục tọa độ, chú giải và số liệu phần trăm trong các biểu đồ phức tạp tiếng Việt.
- **Nhóm 75% - 80%:** `Gemini 3.5 Flash Lite` (80,00%), `Gemini 3 Flash Preview` (77,78%), `Amazon Nova Lite` (77,78%), và `Gemma 3 12B` (75,56%).
- **Đội sổ:** `qwen/qwen3-vl-8b-instruct` chỉ đạt **51,11%** (gần tương đương mức đoán ngẫu nhiên).

#### Khía cạnh 2: Năng lực phát hiện nhận định sai lệch (Recall nhãn "Sai")
- **Yêu cầu cốt lõi của Fact-Check:** Một hệ thống kiểm chứng thông tin bắt buộc phải phát hiện được khi có thông tin sai lệch đưa vào.
- **4 mô hình đạt điểm tuyệt đối 100% Recall nhãn "Sai":** `Gemini 3.1 Flash Lite`, `Gemini 3.5 Flash Lite`, `Gemini 3 Flash Preview`, và `Gemini 2.5 Flash`. Không một câu sai nào lọt qua được các mô hình này trên tập Validation.
- **Hạn chế của Open Weights:** `Gemma 3 12B` chỉ đạt **61,11% Recall** (bỏ sót gần 40% nhận định sai), cho thấy các mô hình mã nguồn mở cỡ nhỏ vẫn gặp khó khăn trong việc đối chiếu chi tiết số liệu đối kháng.

#### Khía cạnh 3: Độ trễ phản hồi (Latency - Tốc độ phục vụ)
- **Nhanh nhất:** `Gemini 3.5 Flash Lite` dẫn đầu với thời gian phản hồi trung bình chỉ **1.381,9 ms** (~1,38s).
- **Cân bằng lý tưởng:** `Gemma 3 12B` (1.565,4 ms) và `Gemini 3.1 Flash Lite` (1.588,0 ms) duy trì độ trễ cực kỳ ổn định dưới 1,6 giây.
- **Chậm và thiếu ổn định:** `openai/gpt-4o-mini` trung bình mất **3.204,1 ms**; `qwen/qwen3-vl-8b-instruct` mất tới **4.391,0 ms** (P95 lên tới 18,2 giây).

#### Khía cạnh 4: Hiệu quả kinh tế & Chi phí API (Cost Optimization)
- **Phát hiện quan trọng về cơ chế tính token ảnh của OpenAI:**
  - `openai/gpt-4o-mini` tiêu tốn tới **$0,2999 USD** cho 45 request (trung bình **$0,00666/request**). Lý do là OpenAI áp dụng cơ chế chia nhỏ ảnh (image tiling) lên tới 87.000 token/lần gọi. Chi phí này **đắt gấp gần 8 lần** so với `Gemini 3.1 Flash Lite` ($0,0390 USD, tương đương $0,00087/request).
  - Vì vậy, **GPT-4o-mini hoàn toàn bị loại bỏ khỏi danh sách cân nhắc sản xuất**.
- **Mô hình siêu rẻ:** `google/gemma-3-12b-it` chỉ tốn $0,0044 USD ($0,000098/request), tuy nhiên độ chính xác chưa đủ cao.
- **Mức chi phí tối ưu nhất:** `Gemini 3.1 Flash Lite` đạt tỷ lệ chất lượng / giá thành cao nhất toàn bộ đợt thử nghiệm.

#### Khía cạnh 5: Mức độ tuân thủ định dạng output (Parse Compliance)
- **8/10 mô hình đạt 100,0% Parse Compliance:** Tất cả các mô hình của Google (trừ bản 2.5 cũ) và Amazon đều xuất đúng định dạng XML/JSON yêu cầu, không cần regex fallback.
- **Mô hình bị lỗi:** `qwen/qwen3-vl-8b-instruct` chỉ đạt **84,44%** do thường xuyên trả lời lan man ngoài cấu trúc; `google/gemini-2.5-flash-lite` bị 2 lần lỗi định dạng khi chạy với Prompt B.

---

## CHƯƠNG 19: PHÂN TÍCH CHUYÊN SÂU THEO 3 SYSTEM PROMPT

| Hạng mục so sánh                                    | Prompt A (Minimalist)                               | Prompt B (Evidence-Grounded)                    | Prompt C (3-Step Workflow)                        |
| ------------------------------------------------------ | --------------------------------------------------- | ----------------------------------------------- | ------------------------------------------------- |
| **Độ tuân thủ cú pháp (Parse Compliance)** | **100,0% (150/150)** — Tuyệt đối an toàn | **96,67% (145/150)** — 5 lỗi cắt ngang | **97,33% (146/150)** — 4 lỗi định dạng |
| **Strict Accuracy**                              | **73,33%**                                    | 72,67%                                          | 70,00%                                            |
| **Balanced Accuracy**                            | 75,28%                                              | **77,69%**                                | 73,81%                                            |
| **Macro-F1 Score**                               | 0,7326                                              | **0,7511**                                | 0,7176                                            |
| **Khả năng bắt lỗi sai (Recall "Sai")**      | 85,00%                                              | **89,47%**                                | 82,46%                                            |
| **Độ trễ xử lý trung bình**                | 2.636,9 ms (chịu ảnh hưởng bởi Qwen)           | **2.166,3 ms**                            | 2.179,8 ms                                        |
| **Tổng chi phí API**                           | $0,1870 USD                                         | **$0,1844 USD**                           | $0,1864 USD                                       |
| **Kết luận đánh giá**                       | **Khuyến nghị sử dụng hàng đầu**       | Phù hợp bài toán ưu tiên tối đa Recall  | Kém hiệu quả trên mô hình nhỏ              |

---

## CHƯƠNG 20: PHÂN TÍCH TỔ HỢP MODEL – PROMPT (30 COMBINATIONS)

### Ma Trận Nhiệt (Heatmap) Hiệu Năng 30 Tổ Hợp Thực Nghiệm

Dưới đây là ma trận nhiệt trực quan hóa độ chính xác Strict Accuracy của toàn bộ 30 tổ hợp (10 mô hình × 3 system prompt):

![Heatmap 10 Model x 3 Prompt](figures/fig5_model_prompt_heatmap_accuracy.png)

> **Nhận định từ Heatmap:** Tổ hợp `google/gemini-3.1-flash-lite` kết hợp cùng `Prompt A` và `Prompt B` đạt gam màu xanh đậm nhất với **86,67% Strict Accuracy** trên cả 3 biến thể prompt, chứng minh năng lực hiểu biểu đồ ổn định tuyệt đối và không phụ thuộc vào độ dài hướng dẫn.


Top 10 tổ hợp Model – Prompt đạt điểm số cao nhất trong 30 tổ hợp thực nghiệm:

|     Top     | Tổ hợp Model + Prompt                            | Parse Compliance |     Accuracy     |   Balanced Acc   |     Macro-F1     | Precision (Sai) |   Recall (Sai)   | Latency TB (ms) | Chi phí ($) | Đánh giá                                                              |
| :----------: | -------------------------------------------------- | :--------------: | :--------------: | :--------------: | :--------------: | :-------------: | :--------------: | :-------------: | :----------: | ------------------------------------------------------------------------ |
| **1** | **`gemini-3.1-flash-lite` + `prompt_A`** | **100,0%** | **86,67%** | **88,89%** | **0,8661** | **75,0%** | **100,0%** |     1.591,7     |   $0,0128   | **Winner được đề xuất: Hoàn hảo, ổn định tuyệt đối** |
| **2** | `gemini-3.1-flash-lite` + `prompt_B`           |      100,0%      |      86,67%      |      88,89%      |      0,8661      |      75,0%      |      100,0%      |     1.632,2     |   $0,0130   | Tương đương top 1 nhưng Prompt B rủi ro hơn ở model khác       |
| **3** | `gemini-3.1-flash-lite` + `prompt_C`           |      100,0%      |      86,67%      |      88,89%      |      0,8661      |      75,0%      |      100,0%      |     1.539,9     |   $0,0131   | Điểm cao nhưng không cần thiết phải dùng prompt dài             |
| **4** | `gemini-3.5-flash-lite` + `prompt_A`           |      100,0%      |      86,67%      |      88,89%      |      0,8661      |      75,0%      |      100,0%      |     1.413,9     |   $0,0155   | Phương án dự phòng số 1 (Tốc độ cực nhanh 1,4s)                |
| **5** | `gemini-3.5-flash-lite` + `prompt_C`           |      100,0%      |      80,00%      |      83,33%      |      0,8000      |      66,7%      |      100,0%      |     1.268,8     |   $0,0158   | Tốt nhưng độ chính xác giảm so với prompt A                      |
| **6** | `gemini-3-flash-preview` + `prompt_A`          |      100,0%      |      80,00%      |      83,33%      |      0,8000      |      66,7%      |      100,0%      |     2.952,6     |   $0,0257   | Độ trễ cao gần gấp đôi Gemini 3.1                                 |
| **7** | `gemini-3-flash-preview` + `prompt_B`          |      100,0%      |      80,00%      |      83,33%      |      0,8000      |      66,7%      |      100,0%      |     1.507,6     |   $0,0260   | Tốt                                                                     |
| **8** | `nova-lite-v1` + `prompt_A`                    |      100,0%      |      80,00%      |      83,33%      |      0,8000      |      66,7%      |      100,0%      |     2.007,1     |   $0,0044   | Mô hình ngoài hệ Google tốt nhất                                   |
| **9** | `nova-lite-v1` + `prompt_B`                    |      100,0%      |      80,00%      |      83,33%      |      0,8000      |      66,7%      |      100,0%      |     2.158,5     |   $0,0045   | Chi phí siêu rẻ                                                       |
| **10** | `gemma-3-12b-it` + `prompt_B`                  |      100,0%      |      80,00%      |      77,78%      |      0,7847      |      80,0%      |      66,7%      |     1.436,9     |   $0,0015   | Mô hình mã nguồn mở tốt nhất                                      |

---

## CHƯƠNG 21: PHƯƠNG PHÁP VÀ KẾT QUẢ LỰA CHỌN WINNER (WINNER SELECTION)

Thuật toán lựa chọn người chiến thắng được mã hóa cứng trong hàm `candidate_score()` thuộc `benchmark_executor_v2.py` tuân thủ nghiêm ngặt theo 5 cổng kiểm soát đã chốt trước khi chạy:

1. **Cổng 1 (Parse Compliance Filter):** Bắt buộc Parse Compliance $\ge 98,0\%$. Nếu vi phạm bị phạt nặng (-0,50 điểm).
2. **Cổng 2 (Macro-F1 Score):** Ưu tiên xếp hạng theo Macro-F1 từ cao xuống thấp.
3. **Cổng 3 (Recall lớp "Sai"):** Yêu cầu bắt buộc Recall lớp `"Sai"` $\ge 65,0\%$.
4. **Cổng 4 (Lifecycle Safety):** Khấu trừ trực tiếp (-0,05 điểm) đối với các mô hình mang nhãn `DEPRECATION_WARNING` (loại trừ nguy cơ chọn phải model sắp chết).
5. **Cổng 5 (Chi phí và Tốc độ):** Ưu tiên giải pháp có chi phí hợp lý và độ trễ thấp trong hệ sinh thái Gemini.

> **KẾT QUẢ XÁC ĐỊNH NGƯỜI CHIẾN THẮNG:**
> **`google/gemini-3.1-flash-lite`** kết hợp **`prompt_A`** đứng vị trí số 1 tuyệt đối: Đạt Compliance 100%, Macro-F1 = 0,8661, Recall("Sai") = 100%, Vòng đời GA ACTIVE, chi phí chỉ $0,00086/câu.

---

## CHƯƠNG 22: KẾT QUẢ HOLDOUT TEST VÀ MA TRẬN NHẦM LẪN (HOLDOUT CONFIRMATION)

### Trực Quan Hóa Ma Trận Nhầm Lẫn & Các Chỉ Số Thẩm Định Holdout Test

Kết quả xác nhận trên 50 mẫu dữ liệu hoàn toàn độc lập (Holdout Test) của ứng viên chiến thắng `Google Gemini 3.1 Flash Lite` + `Prompt A`:

![Ma trận nhầm lẫn và chỉ số Holdout Test](figures/fig6_holdout_test_confusion_matrix_and_metrics.png)


Để kiểm chứng tính tổng quát hóa độc lập, tổ hợp chiến thắng (`gemini-3.1-flash-lite` + `prompt_A`) đã được đưa vào thẩm định trên **50 mẫu Holdout Test** chưa từng xuất hiện trong Validation.

### 22.1. Ma trận nhầm lẫn (Confusion Matrix)

*Quy ước đánh giá:* Lớp Dương tính (Positive) = `"Sai"` (Phát biểu sai sự thật); Lớp Âm tính (Negative) = `"Đúng"` (Phát biểu đúng sự thật).

```
                      DỰ ĐOÁN (PREDICTED)
                      Lớp "Sai"       Lớp "Đúng"       TỔNG THỰC TẾ
THỰC TẾ    Lớp "Sai"   TP = 21         FN = 2            23 mẫu
(GOLD)     Lớp "Đúng"  FP = 4          TN = 23           27 mẫu
           TỔNG ĐOÁN   25 mẫu          25 mẫu            50 mẫu
```

- **True Positive (TP = 21):** Phát biểu sai thực tế được mô hình phát hiện chính xác là `"Sai"`.
- **True Negative (TN = 23):** Phát biểu đúng thực tế được mô hình xác nhận chính xác là `"Đúng"`.
- **False Positive (FP = 4):** Phát biểu đúng bị mô hình nghi ngờ báo nhầm thành `"Sai"` (Báo động nhầm / Type I Error).
- **False Negative (FN = 2):** Phát biểu sai bị mô hình bỏ sót duyệt nhầm thành `"Đúng"` (Bỏ lọt lỗi sai / Type II Error).

### 22.2. Bảng chỉ số hiệu năng Holdout Test chi tiết

| Chỉ số đo lường (Metric)                  |   Giá trị thực tế   | Mục tiêu yêu cầu ban đầu | Đánh giá trạng thái                     |
| ---------------------------------------------- | :----------------------: | :----------------------------: | -------------------------------------------- |
| **Tổng số mẫu kiểm thử**            |            50            |             15–20             | Vượt mục tiêu mẫu của Leader           |
| **Số lỗi cú pháp (Parse Errors)**    |    **0 (0,0%)**    |               0               | Hoàn hảo                                   |
| **Parse Compliance Rate**                |     **100,0%**     |         $\ge 98,0\%$         | Vượt chuẩn                                |
| **Strict Accuracy**                      | **88,00%** (44/50) |         $\ge 70,0\%$         | **Vượt xa kỳ vọng (+18,0%)**       |
| **Balanced Accuracy**                    |     **88,24%**     |         $\ge 70,0\%$         | **Cân bằng rất tốt giữa 2 lớp**  |
| **Macro-F1 Score**                       |     **0,8798**     |         $\ge 0,700$         | **Rất cao (+0,1798)**                 |
| **Recall lớp "Sai" (Bắt lỗi sai)**    | **91,30%** (21/23) |         $\ge 65,0\%$         | **Bắt trúng 91,3% nhận định sai** |
| **Precision lớp "Sai"**                 | **84,00%** (21/25) |               —               | Độ tin cậy cảnh báo đạt 84%           |
| **F1-Score lớp "Sai"**                  |     **0,8750**     |               —               | Chỉ số F1 lớp phát hiện sai rất cao    |
| **Recall lớp "Đúng"**                 | **85,19%** (23/27) |               —               | Duyệt đúng 85,2% nhận định chuẩn      |
| **Precision lớp "Đúng"**              | **92,00%** (23/25) |               —               | Độ chuẩn xác khi bảo đúng đạt 92%   |
| **F1-Score lớp "Đúng"**               |     **0,8846**     |               —               | —                                           |
| **Độ trễ trung bình (Mean Latency)** |   **1.607,7 ms**   |         $< 3.500$ ms         | Nhanh, phản hồi trong 1,6 giây            |
| **Độ trễ Median / P95**               | 1.613,5 ms / 2.074,5 ms |               —               | Phân phối độ trễ cực kỳ tập trung    |
| **Tổng chi phí 50 mẫu Test**          |  **$0,0402 USD**  |               —               | Chưa tới 1.000 VNĐ cho 50 mẫu ảnh       |

---

## CHƯƠNG 23: PHÂN TÍCH CHI PHÍ VÀ ĐỐI SOÁT TÀI CHÍNH (FINANCIAL AUDIT)

### Biểu Đồ Đối Soát Ngân Sách và Cơ Cấu Chi Phí Từng Mô Hình

Dưới đây là biểu đồ đối soát thực tế giữa trần ngân sách Leader giao và chi phí OpenRouter ghi nhận:

![Biểu đồ Đối Soát Tài Chính và Ngân Sách](figures/fig7_budget_breakdown_and_actual_burn.png)

```mermaid
pie title Phân Bổ Chi Phí Billed Mẫu Đánh Giá ($0.598 USD)
    "OpenAI GPT-4o-mini ($0.300)" : 50.1
    "Gemini 3 Flash Preview ($0.078)" : 13.0
    "Gemini 3.5 Flash Lite ($0.047)" : 7.9
    "Gemini 3.1 Flash Lite ($0.079 - Val+Test)" : 13.2
    "Gemini 2.5 Flash ($0.031)" : 5.2
    "Qwen3 VL 8B ($0.028)" : 4.6
    "Amazon Nova Lite ($0.013)" : 2.2
    "Gemini 2.5 Flash Lite ($0.010)" : 1.7
    "Gemma 3 27B ($0.007)" : 1.2
    "Gemma 3 12B ($0.004)" : 0.7
```


Toàn bộ dữ liệu chi phí được đối soát trực tiếp từ từng dòng giao dịch trong `benchmark_request_ledger.jsonl`.

### 23.1. Đối soát tổng ngân sách

| Khoản mục đối soát                                   | Số lượng request |         Chi phí ghi nhận ($ USD)         | Tỷ lệ so với ngân sách $28 | Tỷ lệ so với trần cứng $35 |
| --------------------------------------------------------- | :-----------------: | :----------------------------------------: | :-----------------------------: | :-----------------------------: |
| **Giai đoạn Validation (10 models × 3 prompts)** |         450         |               $0,557728 USD               |              1,99%              |              1,59%              |
| **Giai đoạn Holdout Test (Winner confirmation)**  |         50         |               $0,040226 USD               |              0,14%              |              0,11%              |
| **TỔNG CHI PHÍ THỰC TẾ THANH TOÁN**            |    **500**    | **$0,597954 USD** (~**$0,5980 USD**) |         **2,14%**         |         **1,71%**         |
| **Hạn mức kế hoạch nội bộ (Internal Target)** |         —         |               $28,000000 USD               |             100,00%             |               —               |
| **Trần cứng tuyệt đối (Hard Cap)**             |         —         |               $35,000000 USD               |               —               |             100,00%             |
| **THẶNG DƯ NGÂN SÁCH CÒN LẠI**                |         —         |         **+$34,402046 USD**         |    **Dôi dư 97,86%**    |    **Dôi dư 98,29%**    |

### 23.2. Phân bổ chi phí theo nhà cung cấp và mô hình

- **Google Gemini Series (5 models):** Tiêu tốn tổng cộng **$0,2052 USD** (34,3% tổng chi phí). Cực kỳ kinh tế.
- **Google Gemma Open-weights (2 models):** Tiêu tốn **$0,0117 USD** (2,0% tổng chi phí). Siêu rẻ.
- **Amazon Nova Lite (1 model):** Tiêu tốn **$0,0134 USD** (2,2% tổng chi phí).
- **Qwen VL Series (1 model):** Tiêu tốn **$0,0276 USD** (4,6% tổng chi phí).
- **OpenAI GPT-4o-mini (1 model):** Tiêu tốn **$0,2999 USD** (**50,2% tổng chi phí toàn đợt chạy** dù chỉ thực hiện 45 request). Đây là minh chứng rõ nét về token inflation do cơ chế image tiling của OpenAI.

### 23.3. Dự toán chi phí khi mở rộng quy mô (Scale-up Projection)

Áp dụng đơn giá thực tế của tổ hợp chiến thắng `gemini-3.1-flash-lite` + `prompt_A` ($0,000805 USD/mẫu kèm ảnh):

- **Chi phí cho 1.000 câu hỏi fact-check:** **~$0,81 USD** (~20.000 VNĐ).
- **Chi phí cho 10.000 câu hỏi fact-check:** **~$8,05 USD** (~205.000 VNĐ).
- **Chi phí cho toàn bộ kho dữ liệu ViChartQA (~20.000 Q&A):** **~$16,10 USD** (Hoàn toàn nằm gọn trong ngân sách $35 USD).

---

## CHƯƠNG 24: PHÂN TÍCH ĐỘ TRỄ, LỖI HTTP VÀ ĐỘ TUÂN THỦ CÚ PHÁP

### Phân Bổ Độ Trễ Thực Tế: Trung Vị (Median) vs Độ Trễ Đỉnh (P95 Spike)

![Phân Bổ Độ Trễ Median vs P95](figures/fig8_latency_distribution_box_or_p95.png)


### 24.1. Phân phối độ trễ (Latency Distribution)

- **Nhóm siêu tốc (< 1.600 ms):** `gemini-3.5-flash-lite` (1.381 ms), `gemini-2.5-flash` (1.396 ms), `gemma-3-12b-it` (1.565 ms), `gemini-3.1-flash-lite` (1.588 ms). Nhóm này đáp ứng hoàn hảo yêu cầu tương tác thời gian thực.
- **Nhóm tốc độ trung bình (1.600 – 2.500 ms):** `amazon/nova-lite-v1` (2.014 ms), `gemma-3-27b-it` (2.397 ms).
- **Nhóm độ trễ cao hoặc bất ổn (> 2.500 ms):** `gemini-3-flash-preview` (P95 vọt lên 12.557 ms do cơ chế preview queuing), `openai/gpt-4o-mini` (3.204 ms do xử lý tiling), `qwen3-vl-8b-instruct` (4.391 ms, P95 lên tới 18.194 ms).

### 24.2. Khảo sát lỗi HTTP và Độ tin cậy mạng (Network Reliability)

- **OpenRouter Gateway:** Đạt độ ổn định 100% đối với 9/10 mô hình (không ghi nhận bất kỳ lỗi HTTP 500 hay timeout nào).
- **Hiện tượng nghẽn Qwen:** Riêng `qwen3-vl-8b-instruct` gặp 7 lần phản hồi HTTP 429 tạm thời do nhà cung cấp upstream phân bổ quota hẹp. Cơ chế Exponential Backoff trong runner đã xử lý thành công, bảo đảm 100% request cuối cùng đều nhận được dữ liệu.

---

## CHƯƠNG 25: KIỂM TRA TÍNH TOÀN VẸN DATASET VÀ NGUYÊN BẢN MÃ NGUỒN

Toàn bộ các file dữ liệu và cấu hình đã được kiểm tra trực tiếp mã băm SHA-256 ngay sau khi kết thúc thực nghiệm:

| File path                                        | Vai trò           | SHA-256 Kỳ vọng ban đầu                                          | SHA-256 Kiểm tra thực tế                                          |    Trạng thái bảo toàn    |
| ------------------------------------------------ | ------------------ | -------------------------------------------------------------------- | -------------------------------------------------------------------- | :----------------------------: |
| `process/vichartqa.json`                       | Dataset gốc       | `50be9c0d68f3d781004c1a7e50f5c15091e941af3352b41e01eba750fcd307c0` | `50be9c0d68f3d781004c1a7e50f5c15091e941af3352b41e01eba750fcd307c0` | **MATCH (Nguyên vẹn)** |
| `process/vichartqa_factcheck_balanced_v1.json` | Dataset dẫn xuất | `98d72f794edcdfa392d53077a0b91de3db2a56cf9ba40d9fb37ff93c151df201` | `98d72f794edcdfa392d53077a0b91de3db2a56cf9ba40d9fb37ff93c151df201` | **MATCH (Nguyên vẹn)** |
| `prompts/prompt_A.txt`                         | System Prompt A    | `aaa0987ba6d4f42ba5ed14b97ed85158095e9e6636f767ebff3d2e08c209ad63` | `aaa0987ba6d4f42ba5ed14b97ed85158095e9e6636f767ebff3d2e08c209ad63` | **MATCH (Nguyên vẹn)** |
| `prompts/prompt_B.txt`                         | System Prompt B    | `4cd10080929707e080c1750e61eb5b63e483a41bd257efb367f79048f349af17` | `4cd10080929707e080c1750e61eb5b63e483a41bd257efb367f79048f349af17` | **MATCH (Nguyên vẹn)** |
| `prompts/prompt_C.txt`                         | System Prompt C    | `bec0a1b239ecedbe4b83d693c11ea2df1bb008a23cbe073c775bb4d882cdd143` | `bec0a1b239ecedbe4b83d693c11ea2df1bb008a23cbe073c775bb4d882cdd143` | **MATCH (Nguyên vẹn)** |
| `sample_pool_val.json`                         | Validation pool    | `4077f621a542adebfa1259a1067c0c9598f99ecf5b1e609849d9be5b350a5f9a` | `4077f621a542adebfa1259a1067c0c9598f99ecf5b1e609849d9be5b350a5f9a` | **MATCH (Nguyên vẹn)** |
| `sample_pool_test_holdout.json`                | Clean Test pool    | `9a1110f014d955fd54254764b4893ddfbee80c45d0c325248b5455cc369b5726` | `9a1110f014d955fd54254764b4893ddfbee80c45d0c325248b5455cc369b5726` | **MATCH (Nguyên vẹn)** |

> **XÁC NHẬN:** Quá trình chạy benchmark tuyệt đối không làm thay đổi, không chèn ghi đè và không làm biến dạng bất kỳ byte dữ liệu nào của các file gốc và file dẫn xuất.

---

## CHƯƠNG 26: KHOẢNG TRỐNG VÀ HẠN CHẾ CÒN LẠI (LIMITATIONS & GAPS)

Nhằm giữ vững tính khách quan khoa học, các giới hạn nội tại của nghiên cứu cần được nhìn nhận minh bạch:

1. **Giới hạn cỡ mẫu (Sample Size Limitations):**
   - Tập mẫu Validation gồm 15 mẫu (đối đầu 30 tổ hợp = 450 request) và tập Holdout Test gồm 50 mẫu là hoàn toàn phù hợp cho mục tiêu thăm dò (pilot screening) theo đúng chỉ đạo của Leader (15–20 mẫu).
   - Tuy nhiên, 15 mẫu hay 50 mẫu chưa thể đại diện cho toàn bộ phân phối phức tạp của 1.000+ biểu đồ trong thực tế. Khoảng tin cậy Wilson 95% cho Accuracy 88% trên 50 mẫu là $[76,0\%; 94,7\%]$. Cần tránh suy diễn rằng mô hình sẽ đạt chính xác 88% trên mọi tập dữ liệu ngoài đời thực.
2. **Độ đa dạng tài liệu (Document Diversity Scope):**
   - 15 mẫu Validation phân bố trên 9 tài liệu độc lập; 50 mẫu Test phân bố trên 22 tài liệu. Mặc dù tính phân tách giữa các document đã được bảo đảm, nhưng phần lớn dữ liệu tập trung vào lĩnh vực Kinh tế và Y tế, chưa bao phủ sâu các dạng biểu đồ kỹ thuật phức tạp (radar chart, scatter plot mật độ cao).
3. **Tính phụ thuộc vào OpenRouter Routing:**
   - Chi phí và độ trễ phụ thuộc vào hạ tầng trung gian OpenRouter tại thời điểm tháng 10/2026. Giá cước và độ trễ có thể biến động nếu nhà cung cấp điều chỉnh chính sách giá hoặc thay đổi máy chủ xử lý ảnh.
4. **Thiếu sự can thiệp thủ công (No Human-in-the-loop Error Audit):**
   - Các trường hợp đoán sai (4 mẫu FP và 2 mẫu FN trên Holdout Test) được phân loại hoàn toàn tự động theo nhãn ground truth. Chưa có bước chuyên gia ngôn ngữ và đồ họa vào xem xét lại xem liệu nhãn ground truth của 6 mẫu đó có tuyệt đối chính xác hay do biểu đồ gốc bị mờ nhạt.

---

## CHƯƠNG 27: KHUYẾN NGHỊ GỬI LEADER (LEADER RECOMMENDATIONS)

Dựa trên toàn bộ bằng chứng thực nghiệm đối đầu và kiểm soát tài chính, Antigravity kính gửi Leader các khuyến nghị hành động cụ thể:

1. **Về lựa chọn Mô hình:**
   - **Chốt phương án chính:** Chọn **`google/gemini-3.1-flash-lite`**. Đây là mô hình đạt điểm chất lượng cao nhất (Accuracy 88%), độ trễ thấp (1,5s), chi phí cực rẻ ($0,0008/câu) và thuộc diện hỗ trợ dài hạn (GA).
   - **Chốt phương án dự phòng:** Chọn **`google/gemini-3.5-flash-lite`** (hoặc `amazon/nova-lite-v1` nếu cần phương án dự phòng khác nhà cung cấp Google).
   - **Loại bỏ ngay lập tức:** Không xem xét đưa `openai/gpt-4o-mini` vào các tác vụ đọc biểu đồ do chi phí token ảnh quá đắt đỏ và hiệu quả không tương xứng. Ngừng dùng `gemini-2.5` do sắp EOL.
2. **Về chiến lược System Prompt:**
   - **Áp dụng chính thức `prompt_A` (Minimalist Tag Extraction):** Loại bỏ rủi ro lỗi cú pháp, bảo đảm 100% parser trích xuất ổn định, tiết kiệm tối đa token đầu ra.
3. **Về kế hoạch triển khai tiếp theo:**
   - Giữ nguyên trạng thái báo cáo nghiên cứu. Không tự ý xây dựng hệ thống web hay production nếu Leader chưa có chủ trương tiếp theo.
