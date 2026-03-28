# YouTube Comment Cleaner & Sentiment Analyzer

Một script Python hoàn chỉnh để làm sạch và phân tích sentiment từ YouTube comments.

---

## 📋 Yêu cầu

- Python 3.11+
- Anthropic API Key

---

## ⚙️ Cài đặt

```bash
pip install -r requirements.txt
```

---

## 🔑 Cấu hình API Key

```bash
# macOS / Linux
export ANTHROPIC_API_KEY="sk-ant-xxxxxxxxxxxxxxxx"

# Windows (Command Prompt)
set ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxxxxx

# Windows (PowerShell)
$env:ANTHROPIC_API_KEY="sk-ant-xxxxxxxxxxxxxxxx"
```

---

## 🚀 Cách dùng

### Chạy cơ bản

```bash
python youtube_comment_analyzer.py data/comments.csv
```

### Tùy chỉnh options

```bash
python youtube_comment_analyzer.py data/comments.xlsx \
    --output results/analyzed.xlsx \
    --min-len 40 \
    --max-len 500 \
    --batch-size 20
```

### Chỉ clean, không gọi AI (test nhanh)

```bash
python youtube_comment_analyzer.py data/comments.csv --skip-ai
```

---

## 📂 Format file input

File CSV hoặc Excel phải có **đúng 5 cột** (tên cột không phân biệt hoa thường):

| Column    | Mô tả                        |
|-----------|------------------------------|
| username  | Tên người dùng               |
| timestamp | Thời gian bình luận          |
| content   | Nội dung bình luận           |
| language  | Ngôn ngữ (en, vi, …)         |
| likes     | Số lượt like của comment     |

---

## 🧹 Cleaning Pipeline

| Bước | Hành động                                      |
|------|------------------------------------------------|
| 1    | Bỏ comment trống / NaN                         |
| 2    | Giữ comment từ 40–500 ký tự                    |
| 3    | Xóa comment chỉ chứa emoji                     |
| 4    | Xóa spam / quảng cáo / nội dung thấp giá trị  |
| 5    | Xóa comment trùng lặp (giữ lần xuất hiện đầu) |

---

## 🤖 AI Analysis Output

Mỗi comment được phân tích thêm 4 cột mới:

| Cột              | Mô tả                                         |
|------------------|-----------------------------------------------|
| topics           | Tối đa 3 chủ đề (bằng tiếng Anh)             |
| keywords         | Tối đa 5 từ khóa quan trọng                  |
| sentiment_label  | Positive / Neutral / Negative                 |
| sentiment_score  | Điểm từ −1.0 (rất tiêu cực) đến +1.0 (rất tích cực) |

---

## 💡 Tips

- **batch-size 20** là tối ưu — đủ nhanh, tránh rate limit.
- Script tự động **retry 3 lần** nếu API lỗi hoặc JSON parse fail.
- Hỗ trợ **đa ngôn ngữ** — không cần filter theo language trước.
- File output có **2 sheet**: dữ liệu đầy đủ + Summary sentiment.
- Màu sắc trong Excel: 🟢 Positive, 🟡 Neutral, 🔴 Negative.

---

## 📊 Ví dụ output

```
[CLEAN] Original rows     : 5,234
[CLEAN] After blank drop  : 5,198
[CLEAN] After length filter: 3,741
[CLEAN] After emoji-only  : 3,690
[CLEAN] After spam filter : 3,612
[CLEAN] After dedup       : 3,488

[ANALYZE] Processing 1–20 / 3,488 …
[ANALYZE] Processing 21–40 / 3,488 …
...

[DONE] Saved → comments_analyzed.xlsx

── Sentiment Summary ──────────────────
  Positive  : 1,823  (52.3%)
  Neutral   :   941  (27.0%)
  Negative  :   724  (20.7%)

  Avg sentiment score : +0.2841
────────────────────────────────────────
```
