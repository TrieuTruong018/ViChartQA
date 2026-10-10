# HƯỚNG DẪN TRA CỨU TÀI LIỆU VÀ BÁO CÁO (DÀNH CHO LEADER REVIEW)

Thư mục này chứa toàn bộ tài liệu nghiên cứu, kết quả benchmark thực nghiệm và các tập dữ liệu dẫn xuất phục vụ nghiệm thu dự án **ViChartQA Fact-Check**.

Các tài liệu được đánh số thứ tự logic theo quy trình từ Tổng quan Kết quả -> Phương án Nghiên cứu -> Kỹ thuật Dữ liệu:

---

## 📌 DANH MỤC TÀI LIỆU VÀ BÁO CÁO

### 🌟 1. BÁO CÁO CHÍNH THỨC NỘP LEADER
* **[`01_BAO_CAO_TONG_KET_BENCHMARK_10_MODEL_VA_PROMPT_FACTCHECK.md`](./01_BAO_CAO_TONG_KET_BENCHMARK_10_MODEL_VA_PROMPT_FACTCHECK.md)**
  * **Nội dung:** Báo cáo thực nghiệm tổng kết toàn diện nhất (27 chương, tích hợp biểu đồ đồ họa và Mermaid). Trả lời trực diện mọi câu hỏi của Leader: Xếp hạng 10 model, so sánh 3 prompt A/B/C, phân tích chi phí ($0.598 USD vs ngân sách $35), và chọn ra Winner toàn diện: **`Google Gemini 3.1 Flash Lite` + `Prompt A`**.

---

### 📋 2. KẾ HOẠCH & PHƯƠNG ÁN NGHIÊN CỨU BAN ĐẦU
* **[`02_PHUONG_AN_NGHIEN_CUU_VA_SANG_LOC_MODEL_PROMPT.md`](./02_PHUONG_AN_NGHIEN_CUU_VA_SANG_LOC_MODEL_PROMPT.md)**
  * **Nội dung:** Báo cáo phương án sàng lọc sơ bộ danh mục 10 mô hình OpenRouter và cơ sở lý thuyết thiết kế 3 System Prompt A/B/C trước khi bấm máy chạy.
* **[`03_KE_HOACH_THI_NGHIEM_VA_DU_TOAN_NGAN_SACH_35_USD.md`](./03_KE_HOACH_THI_NGHIEM_VA_DU_TOAN_NGAN_SACH_35_USD.md)**
  * **Nội dung:** Kế hoạch kiểm soát trần ngân sách 35 USD (mục tiêu nội bộ $28 USD) và ma trận phân bổ số lượng mẫu validation/test pool.

---

### 🔬 3. QUY TRÌNH KỸ THUẬT CÂN BẰNG DỮ LIỆU (BALANCING DATASET)
* **[`04_LICH_SU_VA_QUY_TRINH_CAN_BANG_DATASET_FACTCHECK.md`](./04_LICH_SU_VA_QUY_TRINH_CAN_BANG_DATASET_FACTCHECK.md)**
  * **Nội dung:** Toàn bộ lịch sử kỹ thuật sinh 548 câu hỏi mang nhãn "Sai", quy trình thẩm định 5 vòng loại bỏ nhiễu và bẫy suy luận.
* **[`05_THONG_KE_CHI_TIET_PHAN_BO_NHAN_DUNG_SAI_V1.md`](./05_THONG_KE_CHI_TIET_PHAN_BO_NHAN_DUNG_SAI_V1.md)**
  * **Nội dung:** Biên bản kiểm đếm trực tiếp trên file JSON: Tỷ lệ Đúng/Sai trước (81.2% / 18.8%) và sau khi cân bằng (53.7% / 46.3%) trên từng tập Train/Val/Test.
* **[`06_BIEN_BAN_HAU_KIEM_TOAN_VEN_DATASET_SAU_MERGE.md`](./06_BIEN_BAN_HAU_KIEM_TOAN_VEN_DATASET_SAU_MERGE.md)**
  * **Nội dung:** Biên bản hậu kiểm bảo toàn toàn vẹn dữ liệu gốc (SHA-256 khớp 100%), kiểm tra ID, schema và loại bỏ trùng lặp.
* **[`07_BAO_CAO_QUY_TRINH_MERGE_DATASET_V1.md`](./07_BAO_CAO_QUY_TRINH_MERGE_DATASET_V1.md)**
  * **Nội dung:** Báo cáo kỹ thuật tóm tắt quá trình merge 548 mẫu vào dataset gốc.

---

### 📊 4. TỆP DỮ LIỆU & HÌNH ẢNH BIỂU ĐỒ
* **`figures/`** — Thư mục chứa các biểu đồ trực quan hóa độ phân giải cao (300 DPI):
  * `fig1_dataset_balance_comparison.png`: Biểu đồ so sánh phân bố nhãn Đúng/Sai trước và sau khi bổ sung 548 câu Sai.
  * `fig2_models_multidimensional_comparison.png`: Biểu đồ so sánh đa chiều 10 mô hình (Accuracy, Recall Sai, Parse Compliance).
  * `fig3_model_tradeoff_accuracy_cost_latency.png`: Ma trận đánh đổi Pareto (Độ chính xác vs Độ trễ vs Chi phí API).
* **`vichartqa_factcheck_balanced_v1.json`** — Tập dữ liệu Fact-Check đã cân bằng hoàn chỉnh (9.399 QA pairs, 1.242 documents).
* **`vichartqa.json`** — Bản sao lưu của tập dữ liệu gốc ban đầu.


---

## 🖼️ HỆ THỐNG BIỂU ĐỒ TRỰC QUAN HÓA TOÀN DỰ ÁN (14 FIGURES)

Leader có thể xem trực quan tất cả kết quả mà không cần đọc dài:

| STT | Tên file biểu đồ (Thư mục igures/) | Nội dung trực quan hóa | Xuất hiện tại |
|:---:|:---|:---|:---|
| **01** | ig1_dataset_balance_comparison.png | Phân bố Đúng/Sai trước (81.2% / 18.8%) vs sau cân bằng (53.7% / 46.3%) theo từng Split | Báo cáo 01 (Mục 6.3) |
| **02** | ig2_models_multidimensional_comparison.png | So sánh đa chiều 10 model (Accuracy, Macro-F1, Recall 'Sai', Parse Compliance) | Báo cáo 01 (Mục 18.1) |
| **03** | ig3_model_tradeoff_accuracy_cost_latency.png | Ma trận đánh đổi Pareto 3 chiều: Độ chính xác vs Độ trễ vs Chi phí API | Báo cáo 01 (Mục 18.1) |
| **04** | ig4_prompt_comparison_head_to_head.png | Đối đầu trực diện 3 Prompt A/B/C (Hiệu năng, Parse compliance, Lỗi cắt cụt output) | Báo cáo 01 (Mục 14) |
| **05** | ig5_model_prompt_heatmap_accuracy.png | Ma trận nhiệt (Heatmap) 30 tổ hợp Model x Prompt | Báo cáo 01 (Mục 20) |
| **06** | ig6_holdout_test_confusion_matrix_and_metrics.png | Ma trận nhầm lẫn (Confusion Matrix) & Chỉ số kiểm chứng Holdout Test 50 mẫu | Báo cáo 01 (Mục 22) |
| **07** | ig7_budget_breakdown_and_actual_burn.png | Đối soát ngân sách  vs Chi phí thực tế .598 tiêu tốn & Tỷ trọng từng model | Báo cáo 01 (Mục 23) |
| **08** | ig8_latency_distribution_box_or_p95.png | Phân bổ độ trễ Median vs P95 Spike của 10 mô hình | Báo cáo 01 (Mục 24) |
| **09** | ig02_model_candidates_pricing.png | Bảng so sánh giá token Input/Output trên OpenRouter của 10 ứng viên | Báo cáo 02 (Mục 1) |
| **10** | ig03_budget_allocation_plan.png | Cơ cấu phân bổ ngân sách dự kiến (.00 USD) | Báo cáo 03 (Mục 1) |
| **11** | ig04_augmentation_batches_breakdown.png | Đóng góp của 4 batch tăng cường (+548 câu Sai) và phân bổ lĩnh vực | Báo cáo 04 (Mục 1) |
| **12** | ig05_question_types_distribution.png | Phân bổ toàn bộ 9.399 QA pairs theo Question Type | Báo cáo 05 (Mục 2) |
| **13** | ig06_audit_gate_pass_checklist.png | Dashboard nghiệm thu 7 cổng kiểm định toàn vẹn dữ liệu (100% PASS) | Báo cáo 06 (Mục 1) |
| **14** | ig07_merge_workflow_summary.png | Sơ đồ tiến trình hợp nhất (Merge Workflow) dữ liệu | Báo cáo 07 (Mục 1) |
