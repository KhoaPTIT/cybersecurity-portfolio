## Desynchronization Attack

Desynchronization attack không nhất thiết phá mạnh nội dung ảnh. Thay vào đó, nó làm lệch hệ tọa độ hoặc lưới block mà bộ trích xuất đang sử dụng.

Khi ảnh bị dịch, crop hoặc xoay nhẹ:

- block 8x8 khi trích xuất không còn trùng với block lúc nhúng
- hệ số DCT được lấy từ vùng ảnh sai
- quan hệ giữa các hệ số DCT bị thay đổi
- BER tăng và thông điệp trích xuất bị lỗi

---

## BER, PSNR và SSIM

Trong bài lab, sinh viên sử dụng file `metrics.csv` để quan sát:

- `BER`: Bit Error Rate, tỷ lệ bit trích xuất sai.
- `PSNR`: đo mức sai khác năng lượng giữa ảnh stego và ảnh attacked.
- `SSIM`: đo độ tương đồng cấu trúc giữa hai ảnh.

Một ảnh attacked có thể vẫn có PSNR/SSIM cao nhưng BER vẫn lớn nếu lưới DCT bị lệch.

---

# Tải bài lab

```bash
imodule https://github.com/KhoaPTIT/dct-geo-desync/raw/main/dct-geo-desync.tar
```

---

# Khởi động bài lab

```bash
cd ~/labtainer/trunk/scripts/labtainer-student
labtainer dct-geo-desync
```

---

# Nội dung thực hành

## Task 1 — Visualize block grid 8x8

Tạo ảnh hiển thị lưới block DCT 8x8.

```bash
python3 run_task.py --task grid
```

Kết quả mong đợi:

- tạo file `grid_8x8.pgm`
- tạo file `grid_report.txt`
- checkwork ghi nhận `task1_grid`

Sinh viên cần quan sát:

- ảnh được chia thành các block 8x8
- bộ nhúng và bộ trích xuất phụ thuộc vào cùng một lưới block

---

## Task 2 — Baseline extraction

Chạy trích xuất trên ảnh stego chưa bị tấn công.

```bash
python3 run_task.py --task baseline
```

Kết quả mong đợi:

- tạo file `baseline_report.txt`
- thông điệp trích xuất đúng
- `BIT_ERROR_RATE = 0.00`
- checkwork ghi nhận `task2_baseline`

Sinh viên cần hiểu rằng baseline đúng vì lưới block 8x8 chưa bị lệch.

---

## Task 3 — Translation nhỏ

Mở file cấu hình:

```bash
nano lab_config.yaml
```

Sửa phần:

```yaml
translate_small:
  enabled: false
  dx: 0
  dy: 0
```

thành ví dụ:

```yaml
translate_small:
  enabled: true
  dx: 3
  dy: 2
```

Chạy:

```bash
python3 run_task.py --task translate_small
```

Kết quả mong đợi:

- tạo file `attacked_translate_small.pgm`
- tạo file `translate_small_report.txt`
- thêm một dòng vào `metrics.csv`

Quan sát:

- ảnh chỉ bị dịch nhẹ
- PSNR/SSIM có thể vẫn tương đối cao
- BER tăng do block DCT bị lệch

---

## Task 4 — Translation lớn

Mở file cấu hình:

```bash
nano lab_config.yaml
```

Sửa:

```yaml
translate_large:
  enabled: false
  dx: 0
  dy: 0
```

thành ví dụ:

```yaml
translate_large:
  enabled: true
  dx: 11
  dy: -7
```

Chạy:

```bash
python3 run_task.py --task translate_large
```

Kết quả mong đợi:

- tạo file `attacked_translate_large.pgm`
- tạo file `translate_large_report.txt`
- cập nhật `metrics.csv`

Quan sát:

- dịch lớn làm sai lệch block rõ hơn
- BER thường cao hơn translation nhỏ
- thông điệp trích xuất bị lỗi nặng hơn

---

## Task 5 — Crop margin

Mở file cấu hình:

```bash
nano lab_config.yaml
```

Sửa:

```yaml
crop_margin:
  enabled: false
  left: 0
  top: 0
```

thành ví dụ:

```yaml
crop_margin:
  enabled: true
  left: 5
  top: 3
```

Chạy:

```bash
python3 run_task.py --task crop_margin
```

Kết quả mong đợi:

- tạo file `attacked_crop_margin.pgm`
- tạo file `crop_margin_report.txt`
- cập nhật `metrics.csv`

Quan sát:

- crop làm mất một phần ảnh
- phần ảnh còn lại bị đặt lại theo hệ tọa độ mới
- lưới DCT khi trích xuất không còn trùng với lưới ban đầu

---

## Task 6 — Crop window

Mở file cấu hình:

```bash
nano lab_config.yaml
```

Sửa:

```yaml
crop_window:
  enabled: false
  x0: 0
  y0: 0
  width: 256
  height: 256
```

thành ví dụ:

```yaml
crop_window:
  enabled: true
  x0: 9
  y0: 6
  width: 232
  height: 236
```

Chạy:

```bash
python3 run_task.py --task crop_window
```

Kết quả mong đợi:

- tạo file `attacked_crop_window.pgm`
- tạo file `crop_window_report.txt`
- cập nhật `metrics.csv`

Quan sát:

- cửa sổ crop nhỏ hơn ảnh gốc
- ảnh bị mất biên và được đặt lại vào canvas mới
- BER tăng do vị trí block bị thay đổi

---

## Task 7 — Rotation clockwise

Mở file cấu hình:

```bash
nano lab_config.yaml
```

Sửa:

```yaml
rotate_cw:
  enabled: false
  degrees: 0.0
```

thành ví dụ:

```yaml
rotate_cw:
  enabled: true
  degrees: 1.6
```

Chạy:

```bash
python3 run_task.py --task rotate_cw
```

Kết quả mong đợi:

- tạo file `attacked_rotate_cw.pgm`
- tạo file `rotate_cw_report.txt`
- cập nhật `metrics.csv`

Quan sát:

- ảnh chỉ xoay nhẹ theo chiều kim đồng hồ
- các pixel bị nội suy
- mỗi block DCT mới chứa dữ liệu trộn từ nhiều block ban đầu
- thông điệp trích xuất sai

---

## Task 8 — Rotation counter-clockwise

Mở file cấu hình:

```bash
nano lab_config.yaml
```

Sửa:

```yaml
rotate_ccw:
  enabled: false
  degrees: 0.0
```

thành ví dụ:

```yaml
rotate_ccw:
  enabled: true
  degrees: -1.4
```

Chạy:

```bash
python3 run_task.py --task rotate_ccw
```

Kết quả mong đợi:

- tạo file `attacked_rotate_ccw.pgm`
- tạo file `rotate_ccw_report.txt`
- cập nhật `metrics.csv`

Quan sát:

- xoay ngược chiều kim đồng hồ cũng gây desynchronization
- BER tăng dù góc xoay nhỏ
- PSNR/SSIM chưa chắc phản ánh đầy đủ lỗi trích xuất

---

## Task 9 — Viết báo cáo phân tích

Mở file báo cáo:

```bash
nano report.md
```

Sinh viên cần phân tích:

- vì sao lệch block 8x8 làm trích xuất DCT sai
- translation gây lệch block như thế nào
- crop gây thay đổi hệ tọa độ ra sao
- rotation nhẹ làm trộn nội dung các block như thế nào
- BER, PSNR, SSIM trong `metrics.csv` thể hiện điều gì

Sau khi viết xong, chạy:

```bash
python3 run_task.py --task report
```

Kết quả mong đợi:

- tạo file `report_check.txt`
- báo cáo đủ nội dung phân tích
- checkwork ghi nhận `task9_report`

---

# Kiểm tra metrics

Sau khi hoàn thành các attack, xem file:

```bash
cat metrics.csv
```

Kết quả gồm các cột:

```text
task,image,ber,psnr,ssim,decoded_message
```

Sinh viên cần có ít nhất 6 dòng tương ứng với 6 ảnh attacked:

```text
attacked_translate_small.pgm
attacked_translate_large.pgm
attacked_crop_margin.pgm
attacked_crop_window.pgm
attacked_rotate_cw.pgm
attacked_rotate_ccw.pgm
```

---

# Checkwork

Thoát khỏi container:

```bash
exit
```

Chạy trên host Labtainer:

```bash
checkwork dct-geo-desync
```

Kết quả mong đợi:

```text
Y - task1_grid
Y - task2_baseline
Y - task3_translate_small
Y - task4_translate_large
Y - task5_crop_margin
Y - task6_crop_window
Y - task7_rotate_cw
Y - task8_rotate_ccw
Y - task9_report
```

---

# Kết luận mong đợi

Sau bài lab, sinh viên cần rút ra:

- Giấu tin DCT 8x8 phụ thuộc mạnh vào đồng bộ block.
- Translation dù nhỏ cũng có thể làm lệch lưới DCT.
- Crop làm thay đổi hệ tọa độ ảnh và phá đồng bộ block.
- Rotation nhẹ làm trộn pixel giữa nhiều block, khiến hệ số DCT bị thay đổi.
- BER có thể tăng cao dù PSNR và SSIM vẫn tương đối tốt.
- PSNR/SSIM đánh giá chất lượng ảnh, nhưng không đủ để đánh giá khả năng trích xuất thông điệp.
- Desynchronization attack là một mối đe dọa quan trọng với kỹ thuật giấu tin dựa trên block.

---

# Thông tin học phần

Hoàng Anh Khoa - B22DCAT164

Lớp: D22CQAT04-B

Học phần: Kỹ thuật giấu tin (INT14102)

Học viện Công nghệ Bưu chính Viễn thông (PTIT)

Giảng viên hướng dẫn: PGS.TS. Đỗ Xuân Chợ
```
