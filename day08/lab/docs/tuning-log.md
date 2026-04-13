# Tuning Log — RAG Pipeline (Day 08 Lab)

> Template: Ghi lại mỗi thay đổi và kết quả quan sát được.
> A/B Rule: Chỉ đổi MỘT biến mỗi lần.

---

## Baseline (Sprint 2)

**Ngày:** ___________  
**Config:**
```
retrieval_mode = "dense"
chunk_size = _____ tokens
overlap = _____ tokens
top_k_search = 10
top_k_select = 3
use_rerank = False
llm_model = _____
```

**Scorecard Baseline:**
| Metric | Average Score |
|--------|--------------|
| Faithfulness | ? /5 |
| Answer Relevance | ? /5 |
| Context Recall | ? /5 |
| Completeness | ? /5 |

**Câu hỏi yếu nhất (điểm thấp):**
> TODO: Liệt kê 2-3 câu hỏi có điểm thấp nhất và lý do tại sao.
> Ví dụ: "q07 (Approval Matrix) - context recall = 1/5 vì dense bỏ lỡ alias."

**Giả thuyết nguyên nhân (Error Tree):**
- [ ] Indexing: Chunking cắt giữa điều khoản
- [ ] Indexing: Metadata thiếu effective_date
- [ ] Retrieval: Dense bỏ lỡ exact keyword / alias
- [ ] Retrieval: Top-k quá ít → thiếu evidence
- [ ] Generation: Prompt không đủ grounding
- [ ] Generation: Context quá dài → lost in the middle

---

## Variant 1 (Sprint 3)

**Ngày:** 2026-04-13  
**Biến thay đổi:** Hybrid retrieval (`retrieve_dense()` + `retrieve_sparse()` + RRF)  
**Lý do chọn biến này:**
> Chọn hybrid vì corpus hiện tại có cả câu tự nhiên (policy, SOP, HR) lẫn keyword/mã ngắn như `P1`, `ERR-403-AUTH`, và alias kiểu `Approval Matrix`.
> Dense retrieval mạnh ở ngữ nghĩa, nhưng dễ kéo nhầm chunk khi query chứa keyword ngắn; BM25 giúp giữ exact match ở section/từ khóa, rồi RRF gộp hai tín hiệu mà vẫn chỉ đổi một biến trong pipeline.

**Config thay đổi:**
```
retrieval_mode = "hybrid"
# Các tham số còn lại giữ nguyên như baseline
```

**Scorecard Variant 1:**
| Metric | Baseline | Variant 1 | Delta |
|--------|----------|-----------|-------|
| Faithfulness | ?/5 | ?/5 | +/- |
| Answer Relevance | ?/5 | ?/5 | +/- |
| Context Recall | ?/5 | ?/5 | +/- |
| Completeness | ?/5 | ?/5 | +/- |

**Nhận xét:**
> Kỳ vọng hybrid cải thiện các query có alias/keyword rõ như `Approval Matrix` hoặc mã lỗi, vì sparse retrieval kéo được exact term còn dense giữ được ngữ nghĩa tổng thể.
> Cần chạy scorecard Sprint 4 để đo chính thức mức tăng/giảm trên toàn bộ bộ câu hỏi.

**Kết luận:**
> Đã implement end-to-end variant hybrid. Kết luận định lượng sẽ chốt sau khi chạy evaluation/scorecard ở Sprint 4.
> Bằng chứng hiện tại là pipeline `retrieve_hybrid()` đã chạy được và có thể so sánh trực tiếp với baseline bằng `compare_retrieval_strategies()`.

---

## Variant 2 (nếu có thời gian)

**Biến thay đổi:** ___________  
**Config:**
```
# TODO
```

**Scorecard Variant 2:**
| Metric | Baseline | Variant 1 | Variant 2 | Best |
|--------|----------|-----------|-----------|------|
| Faithfulness | ? | ? | ? | ? |
| Answer Relevance | ? | ? | ? | ? |
| Context Recall | ? | ? | ? | ? |
| Completeness | ? | ? | ? | ? |

---

## Tóm tắt học được

> TODO (Sprint 4): Điền sau khi hoàn thành evaluation.

1. **Lỗi phổ biến nhất trong pipeline này là gì?**
   > _____________

2. **Biến nào có tác động lớn nhất tới chất lượng?**
   > _____________

3. **Nếu có thêm 1 giờ, nhóm sẽ thử gì tiếp theo?**
   > _____________
