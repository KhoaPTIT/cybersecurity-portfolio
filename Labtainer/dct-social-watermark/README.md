# DCT Social Watermark

Bài thực hành Labtainer về **giấu watermark trong ảnh bằng DCT và kiểm tra độ bền sau các pipeline mạng xã hội**.

Lab mô phỏng quá trình ảnh sau khi được đăng tải hoặc xử lý qua các nền tảng như Facebook, Zalo và Web Thumbnail. Sinh viên sẽ nhúng watermark vào ảnh bằng DCT, cho ảnh đi qua các pipeline nén/resize, sau đó trích xuất watermark để đánh giá độ bền bằng BER và confidence.

Chủ đề chính:

- Giấu watermark trong ảnh bằng DCT block.
- Đánh giá PSNR, BER và confidence.
- Mô phỏng pipeline xử lý ảnh của Facebook, Zalo và Web Thumbnail.
- Trích xuất watermark sau recompression và resize.
- So sánh mức độ phá hỏng watermark giữa các nền tảng.
- Tinh chỉnh cấu hình robust watermark.
- Viết memo phân tích trade-off giữa độ bền và chất lượng ảnh.

---

# Mục tiêu

Sau khi hoàn thành bài lab, sinh viên có thể:

- Hiểu cách nhúng watermark vào ảnh bằng DCT.
- Hiểu ảnh hưởng của JPEG compression, resize và color-space conversion tới watermark.
- Mô phỏng quá trình ảnh bị xử lý qua nhiều nền tảng mạng xã hội.
- Đo BER sau từng pipeline.
- So sánh nền tảng nào làm watermark suy giảm mạnh nhất.
- Tinh chỉnh `ALPHA` và `REPEAT` để tăng độ bền watermark.
- Phân tích trade-off giữa robustness và visual quality.
- Viết memo giải thích kết quả thực nghiệm.

---

# Kiến thức chính

## 1. DCT Watermarking

Trong bài lab, ảnh được chia thành các block và watermark được nhúng vào miền tần số bằng DCT.

Ý tưởng chính là thay đổi quan hệ giữa các hệ số DCT để biểu diễn bit watermark.

Tham số quan trọng:

```text
ALPHA  : cường độ nhúng watermark
REPEAT : số lần lặp mỗi bit để tăng khả năng bỏ phiếu khi trích xuất
```

`ALPHA` càng cao thì watermark thường bền hơn, nhưng ảnh có thể bị giảm chất lượng.

`REPEAT` càng cao thì khả năng khôi phục bit tốt hơn, nhưng dung lượng watermark giảm vì cần nhiều block hơn cho cùng một thông điệp.

---

## 2. Social Media Pipeline

Khi ảnh được tải lên mạng xã hội hoặc website, ảnh thường bị xử lý lại:

- Resize về kích thước nhỏ hơn.
- Nén JPEG lại.
- Thay đổi color space.
- Làm mất một phần chi tiết tần số cao.
- Thay đổi giá trị pixel và hệ số DCT.

Các thay đổi này có thể làm watermark bị lỗi dù ảnh vẫn nhìn bình thường bằng mắt.

---

## 3. BER và Confidence

Lab sử dụng hai chỉ số chính:

```text
BER        : Bit Error Rate, tỷ lệ bit watermark khôi phục sai
Confidence : độ tin cậy của quá trình vote/extract bit
```

Kết quả tốt là:

```text
BER thấp
Confidence cao
Ảnh stego vẫn giữ chất lượng chấp nhận được
```

---

# Tải bài lab


```bash
imodule https://github.com/KhoaPTIT/dct-social-watermark/raw/master/dct-social-watermark.tar
```

---

# Khởi động bài lab

```bash
cd ~/labtainer/labtainer-student
labtainer dct-social-watermark
```

---

# Nội dung thực hành

## Task 1 — Tạo cover

Task này tạo ảnh cover dùng làm ảnh gốc để nhúng watermark.

Chạy:

```bash
python3 generate_cover.py
eog cover.png &
cat cover_report.txt
```

Sinh viên cần quan sát:

- `cover.png`: ảnh gốc.
- Nội dung trong `cover_report.txt`.
- Ảnh có đủ vùng chi tiết để nhúng watermark.

Kết quả checkwork:

```text
Y - task1_cover
```

---

## Task 2 — Nhúng watermark

Task này nhúng watermark vào ảnh bằng DCT.

Chạy:

```bash
python3 embed_watermark.py --alpha 35 --repeat 12
eog stego.png &
cat embed_report.txt
```

Sinh viên cần quan sát:

- `stego.png`: ảnh sau khi nhúng watermark.
- PSNR sau khi nhúng.
- BER khi chưa qua pipeline mạng xã hội.
- Watermark có được khôi phục đúng ở trạng thái ban đầu hay không.

Kết quả checkwork:

```text
Y - task2_embed
```

---

## Task 3 — Facebook pipeline

Task này mô phỏng quá trình ảnh bị xử lý qua pipeline Facebook.

Chạy:

```bash
python3 social_pipeline.py --platform facebook
eog attacks/facebook_simulated.jpg &
cat facebook_report.txt
```

Sinh viên cần quan sát:

- `attacks/facebook_simulated.jpg`.
- Ảnh sau khi bị nén/resize theo mô phỏng Facebook.
- JPEG quality và kích thước ảnh sau pipeline.
- Nội dung trong `facebook_report.txt`.

Kết quả checkwork:

```text
Y - task3_facebook_pipeline
```

---

## Task 4 — Zalo pipeline

Task này mô phỏng quá trình ảnh bị xử lý qua pipeline Zalo.

Chạy:

```bash
python3 social_pipeline.py --platform zalo
eog attacks/zalo_simulated.jpg &
cat zalo_report.txt
```

Sinh viên cần quan sát:

- `attacks/zalo_simulated.jpg`.
- Mức độ thay đổi ảnh sau pipeline Zalo.
- Tác động của recompression và color-space roundtrip.
- Nội dung trong `zalo_report.txt`.

Kết quả checkwork:

```text
Y - task4_zalo_pipeline
```

---

## Task 5 — Web thumbnail pipeline

Task này mô phỏng quá trình ảnh bị resize mạnh và nén lại để tạo thumbnail web.

Chạy:

```bash
python3 social_pipeline.py --platform web
eog attacks/web_simulated.jpg &
cat web_report.txt
```

Sinh viên cần quan sát:

- `attacks/web_simulated.jpg`.
- Ảnh thumbnail sau resize.
- Mức độ mất chi tiết ảnh.
- Nội dung trong `web_report.txt`.

Kết quả checkwork:

```text
Y - task5_web_pipeline
```

---

## Task 6 — Extract Facebook

Task này trích xuất watermark từ ảnh đã qua pipeline Facebook.

Chạy:

```bash
python3 extract_watermark.py --image attacks/facebook_simulated.jpg
eog facebook_bit_errors.png &
cat extract_facebook_report.txt
```

Sinh viên cần quan sát:

- `facebook_bit_errors.png`: bản đồ lỗi bit sau Facebook pipeline.
- BER trong `extract_facebook_report.txt`.
- Confidence trung bình.
- Watermark còn khôi phục tốt hay không.

Kết quả checkwork:

```text
Y - task6_extract_facebook
```

---

## Task 7 — Extract Zalo

Task này trích xuất watermark từ ảnh đã qua pipeline Zalo.

Chạy:

```bash
python3 extract_watermark.py --image attacks/zalo_simulated.jpg
eog zalo_bit_errors.png &
cat extract_zalo_report.txt
```

Sinh viên cần quan sát:

- `zalo_bit_errors.png`.
- BER sau pipeline Zalo.
- Confidence sau pipeline Zalo.
- So sánh với Facebook pipeline.

Kết quả checkwork:

```text
Y - task7_extract_zalo
```

---

## Task 8 — Extract Web

Task này trích xuất watermark từ ảnh đã qua Web Thumbnail pipeline.

Chạy:

```bash
python3 extract_watermark.py --image attacks/web_simulated.jpg
eog web_bit_errors.png &
cat extract_web_report.txt
```

Sinh viên cần quan sát:

- `web_bit_errors.png`.
- BER sau Web Thumbnail pipeline.
- Confidence sau resize mạnh.
- Web pipeline có phá watermark mạnh hơn Facebook/Zalo không.

Kết quả checkwork:

```text
Y - task8_extract_web
```

---

## Task 9 — So sánh pipeline

Task này tổng hợp và so sánh kết quả giữa các pipeline.

Chạy:

```bash
python3 compare_pipelines.py
eog pipeline_contact_sheet.jpg &
cat comparison_report.txt
```

Sinh viên cần quan sát:

- `pipeline_contact_sheet.jpg`: ảnh tổng hợp các pipeline.
- Pipeline nào giữ watermark tốt nhất.
- Pipeline nào làm BER cao nhất.
- Sự khác nhau giữa Facebook, Zalo và Web Thumbnail.

Kết quả checkwork:

```text
Y - task9_compare
```

---

## Task 10 — Tune robust config

Task này yêu cầu sinh viên chỉnh cấu hình watermark để tăng độ bền sau pipeline mạng xã hội.

Mở file:

```bash
nano robust_config.py
```

Sửa ví dụ như sau, nhớ xóa `TODO`:

```python
ALPHA = 45
REPEAT = 20
TARGET_PLATFORM = "zalo"
RATIONALE = "I increased alpha and repeat to improve robustness after social-media recompression. Higher alpha increases DCT coefficient separation, while higher repeat provides more votes per bit. This improves BER and confidence at the cost of more visible image distortion."
```

Chạy tuning:

```bash
python3 tune_robust_config.py
eog robust_stego.png &
eog attacks/robust_zalo_simulated.jpg &
cat tuning_report.txt
```

Sinh viên cần quan sát:

- `robust_stego.png`: ảnh sau khi tăng độ bền watermark.
- `attacks/robust_zalo_simulated.jpg`: ảnh robust sau pipeline Zalo.
- BER sau khi tuning.
- Confidence sau khi tuning.
- Ảnh có bị giảm chất lượng rõ rệt không.

Kết quả checkwork:

```text
Y - task10_tuning
```

---

## Task 11 — Viết ROBUSTNESS_MEMO.md

Task này yêu cầu sinh viên viết memo phân tích kết quả sau các pipeline và sau tuning.

Mở file:

```bash
nano ROBUSTNESS_MEMO.md
```

Nội dung mẫu:

```markdown
# Robustness Memo

Facebook had the lowest BER because it resized less aggressively and used a higher JPEG quality. The average confidence remained high, so the DCT coefficient votes were still stable.

Zalo caused higher BER because it used stronger recompression and a color-space roundtrip. This changed the luma channel and weakened some DCT coefficient margins.

The web thumbnail pipeline was the strongest attack because it resized the image down to a much smaller maximum side and used lower JPEG quality. This removed more high-frequency detail and damaged the DCT watermark.

BER measures the percentage of incorrect recovered bits. Confidence measures how strongly the DCT coefficient pairs supported each recovered bit. A lower BER and higher confidence mean the watermark survived better.

I increased alpha and repeat in robust_config.py. Higher alpha makes the DCT coefficient difference larger, and higher repeat stores each bit in more blocks. This robust setting improves recovery after upload pipelines, but it can reduce visual quality because stronger embedding changes the image more.
```

Validate:

```bash
python3 validate_robustness_memo.py
cat robustness_memo_report.txt
```

Sinh viên cần đảm bảo:

- Không còn TODO.
- Có phân tích Facebook, Zalo và Web Thumbnail.
- Có giải thích BER và confidence.
- Có nêu đã thay đổi gì trong `robust_config.py`.
- Có nhắc tới trade-off giữa robustness và visual quality.

Kết quả checkwork:

```text
Y - task11_memo
```

---

## Task 12 — Summary và checkwork

Task cuối tạo báo cáo tổng hợp.

Chạy trong container:

```bash
python3 summary.py
cat summary_report.txt
```

Sau đó chạy checkwork ngoài terminal student:

```bash
cd ~/labtainer/labtainer-student
checkwork
```

`summary_report.txt` sẽ tổng hợp:

- PSNR của ảnh stego.
- BER sau Facebook pipeline.
- BER sau Zalo pipeline.
- BER sau Web Thumbnail pipeline.
- Confidence của từng pipeline.
- Kết quả tuning robust config.
- Trạng thái memo.

Kết quả checkwork:

```text
Y - task12_summary
```

---

# Checkwork

Sau khi hoàn thành đầy đủ, chạy:

```bash
cd ~/labtainer/labtainer-student
checkwork
```

Kết quả mong đợi:

```text
Labname dct-social-watermark

Y - task1_cover
Y - task2_embed
Y - task3_facebook_pipeline
Y - task4_zalo_pipeline
Y - task5_web_pipeline
Y - task6_extract_facebook
Y - task7_extract_zalo
Y - task8_extract_web
Y - task9_compare
Y - task10_tuning
Y - task11_memo
Y - task12_summary
```

---

# Dừng lab

Sau khi hoàn thành:

```bash
stoplab dct-social-watermark
```

---

# Thông tin học phần

Hoàng Anh Khoa — B22DCAT164

Lớp: D22CQAT04-B

Học phần: Kỹ thuật giấu tin (INT14102)

Học viện Công nghệ Bưu chính Viễn thông (PTIT)

Giảng viên hướng dẫn: PGS.TS. Đỗ Xuân Chợ
