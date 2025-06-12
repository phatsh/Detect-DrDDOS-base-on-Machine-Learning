# Phát Hiện Tấn Công DDoS Sử Dụng Random Forest

## 1. Giới thiệu

Dự án này sử dụng thuật toán Random Forest để phát hiện tấn công DDoS trong lưu lượng mạng, dựa trên bộ dữ liệu DrDoS_DNS. Quá trình gồm các bước: tiền xử lý dữ liệu, cân bằng dữ liệu, huấn luyện mô hình, đánh giá hiệu suất và trực quan hóa kết quả.

---

## 2. Lý thuyết về Random Forest

### 2.1. Random Forest là gì?

Random Forest là một thuật toán học máy thuộc nhóm ensemble learning, kết hợp nhiều cây quyết định (decision trees) để cải thiện độ chính xác và giảm overfitting. Mỗi cây được huấn luyện trên một tập con dữ liệu và đặc trưng ngẫu nhiên, sau đó kết quả được tổng hợp (đa số phiếu với phân loại).

### 2.2. Cách hoạt động

- **Bootstrap Aggregation (Bagging):** Lấy mẫu ngẫu nhiên có hoàn lại từ tập dữ liệu gốc để huấn luyện từng cây.
- **Random Feature Selection:** Ở mỗi node, chỉ chọn ngẫu nhiên một tập con đặc trưng để chia nhánh, giúp các cây đa dạng hơn.
- **Voting:** Kết quả cuối cùng là dựa trên đa số phiếu (classification) hoặc trung bình (regression) của các cây.

### 2.3. Ưu điểm

- Giảm overfitting so với cây quyết định đơn lẻ.
- Hiệu quả với dữ liệu lớn, nhiều chiều.
- Có thể đánh giá tầm quan trọng của từng đặc trưng.

---

## 3. Quy trình xây dựng mô hình

### 3.1. Tiền xử lý dữ liệu

- Đọc dữ liệu theo từng chunk để tránh tràn RAM.
- Loại bỏ các cột không cần thiết: `Unnamed: 0`, `Flow ID`, `Source IP`, `Destination IP`, `Timestamp`, `SimillarHTTP`.
- Chuyển nhãn về nhị phân: `1` cho DDoS, `0` cho BENIGN.
- Xử lý giá trị thiếu, vô cực và âm.

### 3.2. Cân bằng dữ liệu

- Sử dụng **SMOTE** để tạo thêm mẫu cho lớp thiểu số, giúp mô hình học tốt hơn.

### 3.3. Huấn luyện mô hình

- Chuẩn hóa dữ liệu với `StandardScaler`.
- Chia dữ liệu thành tập huấn luyện và kiểm tra.
- Huấn luyện mô hình `RandomForestClassifier` với 350 cây.

### 3.4. Đánh giá mô hình

- Dự đoán trên tập kiểm tra.
- Tính toán các chỉ số: Accuracy, Precision, Recall, F1-score.
- Lưu mô hình và scaler để sử dụng lại.

---

## 4. Giải thích các hình trực quan

### 4.1. Phân bố nhãn

![](assets/label_distribution.png)

- **Ý nghĩa:** Cho thấy tỷ lệ giữa các nhãn BENIGN và DDoS trong dữ liệu.
- **Nhận xét:** Nếu dữ liệu mất cân bằng, cần cân bằng lại để tránh mô hình thiên lệch.

### 4.2. Tỷ lệ BENIGN và DDoS

![](assets/label_pie.png)

- **Ý nghĩa:** Biểu đồ tròn thể hiện tỷ lệ phần trăm giữa hai lớp.
- **Nhận xét:** Giúp đánh giá mức độ mất cân bằng.

### 4.3. Phân bố các đặc trưng

![](assets/feature_distribution.png)

- **Ý nghĩa:** Biểu đồ histogram cho từng đặc trưng chính, phân biệt theo nhãn.
- **Nhận xét:** Cho thấy sự khác biệt về phân bố giữa BENIGN và DDoS.

### 4.4. Boxplot các đặc trưng

![](assets/feature_boxplot.png)

- **Ý nghĩa:** Boxplot thể hiện phân bố, giá trị ngoại lệ của từng đặc trưng theo nhãn.
- **Nhận xét:** Dễ dàng nhận diện đặc trưng phân biệt tốt hai lớp.

### 4.5. Ma trận tương quan

![](assets/correlation_heatmap.png)

- **Ý nghĩa:** Ma trận heatmap thể hiện mức độ tương quan giữa các đặc trưng chính.
- **Nhận xét:** Phát hiện các đặc trưng có thể trùng lặp thông tin.

### 4.6. Scatter Plot

![](assets/scatter_plot.png)

- **Ý nghĩa:** Quan hệ giữa hai đặc trưng chính, phân biệt theo nhãn.
- **Nhận xét:** Kiểm tra khả năng phân tách hai lớp.

### 4.7. Ma trận nhầm lẫn

![](assets/confusion_matrix.png)

- **Ý nghĩa:** Số lượng dự đoán đúng/sai cho từng lớp.
- **Nhận xét:** Đánh giá trực quan hiệu suất mô hình.

### 4.8. Đường cong ROC

![](assets/roc_curve.png)

- **Ý nghĩa:** Đánh giá khả năng phân biệt giữa hai lớp của mô hình.
- **Nhận xét:** Diện tích dưới đường cong (AUC) càng gần 1 càng tốt.

---

## 5. Kết luận

- Mô hình Random Forest cho kết quả tốt trong phát hiện DDoS.
- Việc cân bằng dữ liệu và chọn đặc trưng phù hợp rất quan trọng.
- Các hình trực quan giúp hiểu rõ hơn về dữ liệu và hiệu suất mô hình.

---

## 6. Tham khảo

- [Tài liệu scikit-learn về Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#forest)
- [Tài liệu về SMOTE](https://imbalanced-learn.org/stable/references/generated/imblearn.over_sampling.SMOTE.html)
