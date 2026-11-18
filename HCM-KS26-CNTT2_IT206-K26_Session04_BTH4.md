

## 1. Phân tích bài toán & Ràng buộc kỹ thuật

Khi nhờ AI nhận xét điểm số sinh viên, AI rất hay mắc hai lỗi:
1. **Tính nhầm số liệu** hoặc gộp chung nhận xét mà không có số liệu chứng minh cụ thể.
2. **Tự bịa ra nguyên nhân** (ví dụ tự phán sinh viên lười học, mải chơi game hay bài Tuần 3 quá khó dù bảng điểm không hề nói tới).

Để giải quyết, em thiết kế Prompt áp dụng **Chain-of-Thought (Suy luận từng bước)** theo đúng trình tự 4 chặng: **Tính toán $\rightarrow$ So sánh $\rightarrow$ Nhận định $\rightarrow$ Đề xuất**, đồng thời khóa chặt quy tắc chỉ dùng dữ liệu trong bảng và liệt kê các câu hỏi cần giảng viên xác minh thêm thay vì tự đoán mò nguyên nhân.

## 2. Thiết kế Prompt hoàn chỉnh

```
Bạn là trợ lý phân tích dữ liệu học tập cho giảng viên môn Lập trình. Nhiệm vụ của bạn là phân tích bảng điểm kiểm tra 3 tuần của 5 sinh viên một cách chính xác, khách quan.

Hãy suy nghĩ từng bước một (Chain-of-Thought) theo đúng trình tự 4 bước: Tính toán -> So sánh -> Nhận định -> Đề xuất.

### RÀNG BUỘC KỸ THUẬT BẮT BUỘC:
1. Chỉ sử dụng duy nhất dữ liệu số trong bảng được cung cấp.
2. Tuyệt đối KHÔNG tự suy diễn nguyên nhân khiến sinh viên giảm điểm (như lười học, bệnh tật, đề khó...). Mọi điều chưa rõ phải đưa vào mục "Vấn đề cần giảng viên xác minh thêm".
3. Mọi nhận định đưa ra đều phải kèm theo con số cụ thể từ bảng điểm để chứng minh.

### QUY TRÌNH LẬP LUẬN TỪNG BƯỚC:
- Bước 1 (Tính toán): Với từng sinh viên, hãy thực hiện phép tính rõ ràng:
  + Mức thay đổi Tuần 1 -> Tuần 2 (= Điểm Tuần 2 - Điểm Tuần 1).
  + Mức thay đổi Tuần 2 -> Tuần 3 (= Điểm Tuần 3 - Điểm Tuần 2).
  + Tổng mức thay đổi sau 3 tuần (= Điểm Tuần 3 - Điểm Tuần 1) và Điểm trung bình 3 tuần.
  + Tổng hợp lại thành một Bảng phân tích kết quả 5 sinh viên bằng Markdown Table.
- Bước 2 (So sánh & Phân nhóm): So sánh kết quả giữa các sinh viên (cao nhất, thấp nhất, tốc độ giảm). Xác định rõ nhóm sinh viên có dấu hiệu sa sút (giảm liên tục qua cả 2 giai đoạn hoặc giảm từ 3 điểm trở lên).
- Bước 3 (Nhận định & Xác minh):
  + Rút ra đúng 3 nhận định quan trọng nhất từ dữ liệu (mỗi nhận định phải có số liệu chứng minh).
  + Liệt kê những vấn đề giảng viên cần xác minh thêm để hiểu đúng thực tế.
- Bước 4 (Đề xuất): Đề xuất đúng 3 giải pháp hỗ trợ học tập cụ thể, bám sát từng nhóm sinh viên đã phân loại ở Bước 2.

### DỮ LIỆU ĐẦU VÀO (BẢNG ĐIỂM 3 TUẦN):
| Sinh viên | Tuần 1 | Tuần 2 | Tuần 3 |
| :--- | :---: | :---: | :---: |
| An | 8 | 7 | 5 |
| Bình | 7 | 6 | 4 |
| Chi | 9 | 8 | 8 |
| Dũng | 6 | 5 | 3 |
| Hà | 8 | 8 | 7 |
```

## 3. Kết quả chạy thử thực tế (Đầu ra của AI)

Dưới đây là kết quả phản hồi của AI sau khi chạy Prompt trên với bảng điểm đề bài cung cấp:

### Bước 1: Tính toán chi tiết & Bảng phân tích kết quả

* **Chi tiết phép tính từng sinh viên:**
  * **An:** Tuần 1 $\rightarrow$ Tuần 2: $7 - 8 = -1$ điểm; Tuần 2 $\rightarrow$ Tuần 3: $5 - 7 = -2$ điểm. Tổng thay đổi: $5 - 8 = -3$ điểm. Trung bình: $(8 + 7 + 5) / 3 = 6.67$ điểm.
  * **Bình:** Tuần 1 $\rightarrow$ Tuần 2: $6 - 7 = -1$ điểm; Tuần 2 $\rightarrow$ Tuần 3: $4 - 6 = -2$ điểm. Tổng thay đổi: $4 - 7 = -3$ điểm. Trung bình: $(7 + 6 + 4) / 3 = 5.67$ điểm.
  * **Chi:** Tuần 1 $\rightarrow$ Tuần 2: $8 - 9 = -1$ điểm; Tuần 2 $\rightarrow$ Tuần 3: $8 - 8 = 0$ điểm. Tổng thay đổi: $8 - 9 = -1$ điểm. Trung bình: $(9 + 8 + 8) / 3 = 8.33$ điểm.
  * **Dũng:** Tuần 1 $\rightarrow$ Tuần 2: $5 - 6 = -1$ điểm; Tuần 2 $\rightarrow$ Tuần 3: $3 - 5 = -2$ điểm. Tổng thay đổi: $3 - 6 = -3$ điểm. Trung bình: $(6 + 5 + 3) / 3 = 4.67$ điểm.
  * **Hà:** Tuần 1 $\rightarrow$ Tuần 2: $8 - 8 = 0$ điểm; Tuần 2 $\rightarrow$ Tuần 3: $7 - 8 = -1$ điểm. Tổng thay đổi: $7 - 8 = -1$ điểm. Trung bình: $(8 + 8 + 7) / 3 = 7.67$ điểm.

* **Bảng phân tích kết quả của 5 sinh viên:**

| Sinh viên | Tuần 1 | Tuần 2 | Tuần 3 | Thay đổi T1$\rightarrow$T2 | Thay đổi T2$\rightarrow$T3 | Tổng thay đổi (T1$\rightarrow$T3) | Điểm TB | Xu hướng |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| **An** | 8 | 7 | 5 | $-1$ | $-2$ | **$-3$** | $6.67$ | Giảm liên tục, giảm nhanh ở T3 |
| **Bình** | 7 | 6 | 4 | $-1$ | $-2$ | **$-3$** | $5.67$ | Giảm liên tục, dưới trung bình ở T3 |
| **Chi** | 9 | 8 | 8 | $-1$ | $0$ | **$-1$** | $8.33$ | Giảm nhẹ T2, giữ ổn định ở mức cao |
| **Dũng** | 6 | 5 | 3 | $-1$ | $-2$ | **$-3$** | $4.67$ | Giảm liên tục, thấp nhất lớp |
| **Hà** | 8 | 8 | 7 | $0$ | $-1$ | **$-1$** | $7.67$ | Ổn định 2 tuần đầu, giảm nhẹ T3 |

### Bước 2: So sánh & Xác định nhóm sinh viên sa sút

* **So sánh giữa các sinh viên:**
  * **Nhóm dẫn đầu:** Chi có điểm trung bình cao nhất ($8.33$) và điểm Tuần 3 cao nhất ($8$), theo sau là Hà với điểm trung bình $7.67$ và điểm Tuần 3 đạt $7$. Cả hai đều chỉ giảm tổng cộng $1$ điểm sau 3 tuần.
  * **Nhóm cuối bảng:** Dũng có điểm xuất phát thấp nhất ($6$ điểm) và kết thúc Tuần 3 cũng thấp nhất ($3$ điểm, trung bình $4.67$). Bình đứng thứ 4 với điểm Tuần 3 là $4$ (trung bình $5.67$).
  * **Về biên độ giảm:** An, Bình và Dũng có cùng mức giảm tổng cộng là $-3$ điểm, cao gấp $3$ lần mức giảm của Chi và Hà ($-1$ điểm).

* **Xác định nhóm sinh viên có dấu hiệu sa sút:**
  * Nhóm có dấu hiệu sa sút rõ rệt gồm **3 sinh viên: An, Bình và Dũng**.
  * *Dữ liệu chứng minh:* Cả 3 bạn đều giảm điểm liên tục qua tất cả các tuần với cùng một kịch bản: giảm $1$ điểm ở Tuần 2 và giảm mạnh thêm $2$ điểm ở Tuần 3 (An: $8 \rightarrow 7 \rightarrow 5$; Bình: $7 \rightarrow 6 \rightarrow 4$; Dũng: $6 \rightarrow 5 \rightarrow 3$). Đặc biệt, Bình ($4$ điểm) và Dũng ($3$ điểm) đã rơi xuống mức dưới trung bình ($< 5$ điểm) ở Tuần 3.

### Bước 3: Ba nhận định quan trọng & Vấn đề cần xác minh thêm

* **Ba nhận định quan trọng từ dữ liệu:**
  1. **Xu hướng giảm điểm diễn ra trên diện rộng nhưng phân hóa thành 2 nhóm rõ rệt:** $100\%$ sinh viên ($5/5$ bạn) đều có điểm Tuần 3 thấp hơn Tuần 1. Tuy nhiên, Chi và Hà chỉ giảm nhẹ $1$ điểm và vẫn duy trì mức khá/giỏi ($7 - 8$ điểm), trong khi An, Bình, Dũng giảm sâu tới $3$ điểm.
  2. **Giai đoạn từ Tuần 2 sang Tuần 3 là thời điểm rơi điểm mạnh nhất của nhóm sa sút:** Ở Tuần 2, mức giảm của An, Bình, Dũng chỉ là $-1$ điểm, nhưng sang Tuần 3 mức giảm tăng gấp đôi lên $-2$ điểm mỗi bạn (kéo điểm trung bình của 3 bạn từ $6.0$ ở Tuần 2 xuống còn $4.0$ ở Tuần 3).
  3. **Nền tảng ban đầu càng thấp thì rủi ro rơi xuống ngưỡng yếu càng cao:** Cả An, Bình, Dũng đều giảm $3$ điểm, nhưng An xuất phát từ $8$ điểm nên Tuần 3 vẫn giữ được mức $5$ điểm; ngược lại Bình (xuất phát $7$) và Dũng (xuất phát $6$) đều đã rớt xuống ngưỡng yếu ($4$ và $3$ điểm).

* **Những vấn đề giảng viên cần xác minh thêm (không tự suy diễn):**
  1. Nội dung kiến thức và độ khó của bài kiểm tra Tuần 3 so với Tuần 1, Tuần 2 có tăng đột biến hay chuyển sang phần lập trình mới (ví dụ: vòng lặp, mảng, hàm) hay không?
  2. Tình trạng chuyên cần (đi học đầy đủ, đi muộn, vắng học) và mức độ hoàn thành bài tập về nhà của An, Bình, Dũng trong Tuần 2 và Tuần 3 như thế nào?
  3. Bài làm kiểm tra Tuần 3 của An, Bình, Dũng bị mất điểm ở phần nào (lỗi cú pháp, sai tư duy thuật toán hay không kịp thời gian làm bài)?
  4. Các yếu tố khách quan khác (vấn đề sức khỏe, tâm lý, lịch thi môn khác hoặc thiết bị thực hành lập trình gặp sự cố).

### Bước 4: Ba giải pháp hỗ trợ học tập cụ thể

1. **Tổ chức buổi phụ đạo chuyên đề theo nội dung bài kiểm tra Tuần 2 - Tuần 3 cho nhóm sa sút (An, Bình, Dũng):**
   * *Cách làm:* Giảng viên rà soát lại bài kiểm tra Tuần 3 của 3 bạn này để xem phần kiến thức nào khiến cả 3 cùng mất $2$ điểm, sau đó mở 1 buổi hướng dẫn lại từ căn bản và cho làm bài tập thực hành có giảng viên kèm trực tiếp trước khi sang bài mới.
2. **Áp dụng mô hình "Đôi bạn cùng tiến" ghép cặp nhóm dẫn đầu với nhóm cuối bảng:**
   * *Cách làm:* Ghép Chi (điểm TB $8.33$, giữ vững $8$ điểm ở Tuần 3) và Hà (điểm TB $7.67$) hỗ trợ trực tiếp cho Dũng (điểm Tuần 3 là $3$) và Bình (điểm Tuần 3 là $4$) trong các giờ thực hành code trên lớp để kịp thời gỡ lỗi khi các bạn bị kẹt.
3. **Thiết lập cảnh báo sớm và bài kiểm tra ngắn (Mini-test) đầu giờ cho riêng nhóm có nguy cơ (An, Bình, Dũng):**
   * *Cách làm:* Giao thêm các bài tập nhỏ chia theo từng bước dễ đến khó trong tuần, kiểm tra nhanh $10$ phút đầu buổi học tiếp theo để theo dõi xem điểm của An có phục hồi về mức $7-8$ và Bình, Dũng có vượt qua ngưỡng $5$ điểm hay không.
