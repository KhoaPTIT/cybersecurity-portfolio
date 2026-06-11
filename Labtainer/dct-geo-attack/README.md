# DCT Geo Attack

Bài thực hành Labtainers khảo sát tấn công hình học vào kỹ thuật giấu tin trong ảnh bằng DCT (Discrete Cosine Transform).

Sinh viên sẽ trực tiếp quan sát ảnh hưởng của translation, crop, rotation, scaling và composite geometric attack tới BER và khả năng khôi phục thông điệp.

---

# Mục tiêu

Sau khi hoàn thành bài lab, sinh viên có thể:

- Hiểu cơ chế giấu tin bằng DCT block 8x8.
- Hiểu vai trò của hệ số mid-frequency.
- Quan sát ảnh hưởng của geometric attack tới BER.
- Phân tích sự mất đồng bộ block DCT.
- Thử nghiệm defense bằng geometric resynchronization.
- Đánh giá trade-off giữa robustness, PSNR và capacity.

---

# Kiến thức chính

## DCT

DCT biến đổi ảnh từ miền không gian sang miền tần số.

Trong bài lab, thông điệp được nhúng vào cặp hệ số mid-frequency:

```text
(3,4) và (4,3)
```

Ý tưởng nhúng:

```text
bit = 1 nếu coeff(3,4) > coeff(4,3)
bit = 0 nếu coeff(3,4) < coeff(4,3)
```

---

## Geometric Attack

Các tấn công hình học làm mất đồng bộ block DCT:

- Translation
- Crop
- Rotation
- Scaling
- Composite attack

Khi block extraction không còn trùng với block nhúng, BER tăng mạnh.

---

# Tải bài lab

```bash
imodule https://github.com/KhoaPTIT/dct-geo-attack/raw/main/dct-geo-attack.tar
```

---

# Khởi động bài lab

```bash
labtainer dct-geo-attack
```

Nếu cần rebuild:

```bash
rebuild dct-geo-attack
```

---

# Nội dung thực hành

## Task 1 — DCT Embedding

Sinh viên tiến hành nhúng thông điệp vào ảnh bằng kỹ thuật DCT watermarking và kiểm tra khả năng khôi phục thông điệp ở trạng thái baseline.

Nhúng thông điệp:

```bash
python3 embed_message.py --alpha 35 --repeat 5
```

Trích xuất thông điệp:

```bash
python3 extract_message.py --image stego.png --repeat 5
```

Mở ảnh để quan sát:

```bash
display cover.png
display stego.png
```

Sinh viên cần quan sát:

- sự khác biệt giữa cover image và stego image.
- giá trị PSNR hiển thị trên terminal.
- BER baseline của hệ thống.
- thông điệp khôi phục sau khi extract.

Kết quả mong đợi:

```text
BER = 0.00%
```

---

## Task 2 — DCT Visualization

Sinh viên trực quan hóa phổ DCT để quan sát phân bố năng lượng trong miền tần số.

Chạy:

```bash
python3 visualize_dct.py
```

Mở ảnh phổ DCT:

```bash
display dct_spectrum_before.png
```

Sinh viên cần quan sát:

- năng lượng tập trung nhiều ở vùng low-frequency.
- vùng mid-frequency được sử dụng để nhúng thông điệp.
- sự khác biệt giữa low-frequency và high-frequency.

Kết luận cần rút ra:

- nhúng vào low-frequency dễ gây méo ảnh.
- nhúng vào high-frequency dễ mất khi ảnh bị biến đổi.
- mid-frequency giúp cân bằng giữa robustness và imperceptibility.

---

## Task 3 — Translation Attack

Sinh viên thực hiện translation attack để khảo sát ảnh hưởng của dịch chuyển hình ảnh tới BER.

Thực hiện các mức dịch chuyển:

```bash
python3 attack_translation.py --dx 2 --dy 2
python3 attack_translation.py --dx 5 --dy 5
python3 attack_translation.py --dx 10 --dy 10
```

Mở ảnh attacked:

```bash
display attacks/translation_10_10.png
```

Sinh viên cần quan sát:

- ảnh bị dịch khỏi vị trí ban đầu.
- vùng biên xuất hiện padding.
- BER tăng theo mức dịch chuyển.
- extractor đọc sai block DCT do mất đồng bộ.

Kết luận cần rút ra:

- translation attack phá block alignment.
- DCT watermarking phụ thuộc mạnh vào vị trí block 8x8.

---

## Task 4 — Crop Attack

Sinh viên khảo sát ảnh hưởng của crop attack tới khả năng khôi phục thông điệp.

Thực hiện crop ở nhiều mức:

```bash
python3 attack_crop.py --percent 5
python3 attack_crop.py --percent 10
python3 attack_crop.py --percent 20
```

Mở ảnh attacked:

```bash
display attacks/crop_20.png
```

Sinh viên cần quan sát:

- ảnh bị mất vùng biên.
- resize làm thay đổi block alignment.
- BER tăng khi crop mạnh hơn.

Kết luận cần rút ra:

- crop attack làm mất dữ liệu watermark.
- resize sau crop gây sai lệch block extraction.

---

## Task 5 — Rotation Attack

Sinh viên thực hiện rotation attack để khảo sát mức độ phá hủy đồng bộ của phép xoay ảnh.

Chạy:

```bash
python3 attack_rotation.py --angle 1
python3 attack_rotation.py --angle 3
python3 attack_rotation.py --angle 5
```

Mở ảnh attacked:

```bash
display attacks/rotation_5.png
```

Sinh viên cần quan sát:

- ảnh bị xoay quanh tâm.
- xuất hiện vùng padding ở góc ảnh.
- BER tăng rất nhanh dù góc xoay nhỏ.
- rotation phá đồng bộ mạnh hơn translation.

Kết luận cần rút ra:

- rotation attack là một trong những geometric attack nguy hiểm nhất với DCT watermarking.
- quá trình nội suy pixel sau rotation làm thay đổi hệ số DCT.

---

## Task 6 — Scaling Attack

Sinh viên khảo sát ảnh hưởng của scaling attack tới khả năng khôi phục thông điệp.

Chạy các mức scaling:

```bash
python3 attack_scaling.py --scale 90
python3 attack_scaling.py --scale 75
python3 attack_scaling.py --scale 50
```

Mở ảnh attacked:

```bash
display attacks/scaling_75.png
```

Sinh viên cần quan sát:

- ảnh bị resize xuống rồi resize lại.
- nội suy làm thay đổi giá trị pixel.
- BER tăng khi scale nhỏ hơn.
- hệ số DCT thay đổi sau scaling.

Kết luận cần rút ra:

- scaling attack gây biến đổi miền tần số.
- watermark bị suy giảm do nội suy ảnh.

---

## Task 7 — Composite Attack

Sinh viên thực hiện composite geometric attack bằng cách kết hợp nhiều phép biến đổi hình học cùng lúc.

Chạy:

```bash
python3 attack_composite.py --angle 3 --dx 5 --dy 5 --crop 10
```

Mở ảnh attacked:

```bash
display attacks/composite_attacked.png
```

Sinh viên cần quan sát:

- ảnh bị nhiều biến đổi đồng thời.
- BER thường cao hơn từng attack đơn lẻ.
- block alignment bị phá hủy nghiêm trọng.

Kết luận cần rút ra:

- composite attack nguy hiểm hơn geometric attack riêng lẻ.
- nhiều biến đổi hình học cộng dồn làm extractor khó đồng bộ lại block DCT.

---

## Task 8 — Resynchronization Defense

Sinh viên thử nghiệm defense bằng geometric resynchronization để giảm BER sau attack.

Chạy defense:

```bash
python3 registration_defense.py
```

Nếu defense chưa hoạt động ổn định, thử điều chỉnh các tham số trong file:

```bash
nano registration_defense.py
```

Ví dụ:

```python
SEARCH_RADIUS = 8
ROTATION_CANDIDATES = [-5, -3, -1, 0, 1, 3, 5]
SCALE_CANDIDATES = [0.90, 0.95, 1.0, 1.05, 1.10]
```

Sau khi chỉnh sửa, chạy lại:

```bash
python3 registration_defense.py
```

Sinh viên cần quan sát:

- defense thử nhiều cấu hình alignment khác nhau.
- BER giảm sau resynchronization.
- defense không thể khôi phục hoàn hảo trong mọi trường hợp.

Kết luận cần rút ra:

- geometric resynchronization có thể cải thiện robustness.
- defense giúp giảm BER nhưng phụ thuộc mạnh vào mức độ attack.

---

# Checkwork

```bash
checkwork
```

Kết quả mong đợi:

```text
Y - task1_embed
Y - task2_visual
Y - task3_translation
Y - task4_crop
Y - task5_rotation
Y - task6_scaling
Y - task7_composite
Y - task8_defense
```

---

# Kết luận mong đợi

Sau bài lab, sinh viên cần rút ra:

- DCT watermarking phụ thuộc mạnh vào block alignment.
- Geometric attack gây tăng BER do mất đồng bộ.
- Rotation và composite attack thường nguy hiểm nhất.
- Tăng alpha và repeat giúp tăng robustness nhưng giảm imperceptibility hoặc capacity.
- Resynchronization defense có thể giảm BER nhưng không khôi phục hoàn hảo.

---

# Thông tin học phần

Hoàng Anh Khoa — B22DCAT164

Lớp: D22CQAT04-B

Học phần: Kỹ thuật giấu tin (INT14102)

Học viện Công nghệ Bưu chính Viễn thông (PTIT)

Giảng viên hướng dẫn: PGS.TS. Đỗ Xuân Chợ
