# IT4772E — Capstone Projects

Mỗi capstone project nằm trong một thư mục riêng.

| Thư mục | Nội dung |
|---|---|
| [`NER/`](NER/) | Nhận dạng thực thể có tên (NER) bằng CRF trên bộ dữ liệu CoNLL-2003 — [`CoNLL2003.ipynb`](NER/CoNLL2003.ipynb). Chuyển thể từ notebook của [sklearn-crfsuite](https://github.com/TeamHG-Memex/sklearn-crfsuite/blob/master/docs/CoNLL2002.ipynb) (MIT). |
| [`Sentiment/`](Sentiment/) | Phân loại cảm xúc bình luận phim IMDb: fine-tune BERT (`bert-base-uncased`) và so sánh với baseline TF-IDF + Logistic Regression (accuracy, precision, recall, F1, kiểm định McNemar) — [`IMDb.ipynb`](Sentiment/IMDb.ipynb). Phần BERT nên chạy trên GPU (ví dụ Google Colab). |

Dữ liệu (`data/`) không được đưa lên repo; mỗi notebook tự tải dữ liệu khi chạy.
