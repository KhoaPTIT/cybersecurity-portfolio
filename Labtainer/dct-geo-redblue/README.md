# DCT Geo RedBlue

Bài thực hành Labtainer về **tấn công và phòng thủ hình học vào kỹ thuật giấu tin trong ảnh dựa trên biến đổi miền tần số DCT**.

Chủ đề chính:

- Giấu tin trong ảnh bằng DCT block 8x8.
- Đánh giá PSNR, SSIM và BER.
- Thực hiện các tấn công hình học vào ảnh stego.
- Tối ưu tấn công để làm tăng BER nhưng vẫn giữ chất lượng ảnh.
- Thiết kế cơ chế phòng thủ bằng margin hardening, redundant embedding, offset voting và checksum recovery.
- Quan sát trực quan ảnh bị tấn công, bản đồ lỗi bit, bản đồ voting và kết quả phục hồi.

---

# Mục tiêu

Sau khi hoàn thành bài lab, sinh viên có thể:

- Hiểu cách nhúng thông điệp vào ảnh bằng DCT.
- Hiểu vì sao tấn công hình học làm sai lệch lưới block 8x8.
- Thực hiện các tấn công translation, crop-resize, rotation, scaling, perspective và composite.
- Đánh giá mức độ tấn công bằng BER, PSNR và SSIM.
- Tối ưu tham số tấn công để tăng BER nhưng ảnh vẫn ít biến dạng.
- Sử dụng margin hardening để tăng độ bền của bit nhúng.
- Sử dụng redundant quadrant embedding để lưu nhiều bản sao thông điệp.
- Sử dụng offset voting để thử nhiều offset lưới DCT khi trích xuất.
- Sử dụng checksum recovery để chọn bản sao thông điệp đáng tin cậy.
- Viết memo phân tích kết quả tấn công và phòng thủ.

---

# Kiến thức chính

## 1. DCT Watermarking

Trong bài lab, ảnh được chia thành các block 8x8. Thông điệp được nhúng vào miền tần số bằng DCT.

Ý tưởng chính là thay đổi quan hệ giữa các hệ số DCT trung tần để biểu diễn bit 0 hoặc bit 1.

DCT watermarking có ưu điểm là ít làm thay đổi ảnh nếu nhúng ở vùng tần số phù hợp. Tuy nhiên, phương pháp này phụ thuộc mạnh vào vị trí block ban đầu.

---

## 2. Geometric Attack

Tấn công hình học làm thay đổi vị trí hoặc hình dạng của ảnh. Khi ảnh bị dịch, cắt, xoay, scale hoặc warp, lưới block DCT ban đầu có thể bị lệch.

Khi extractor vẫn đọc theo lưới block cũ, các hệ số DCT được đọc ra không còn đúng với vị trí đã nhúng, làm BER tăng mạnh.

Các dạng tấn công trong lab:

- Translation attack
- Crop-resize attack
- Rotation attack
- Scaling attack
- Perspective attack
- Composite attack
- Optimized attack

---

## 3. Red Team và Blue Team

Lab được chia thành hai phần:

```text
Red Team  : tạo và tối ưu tấn công hình học để làm watermark bị lỗi.
Blue Team : thiết kế cơ chế phòng thủ để giảm BER và khôi phục thông điệp.
```

Red Team cần làm BER tăng nhưng không làm ảnh méo quá rõ.

Blue Team cần giảm BER bằng các kỹ thuật:

- Margin hardening
- Redundant quadrant embedding
- Offset voting
- Checksum recovery

---

# Tải bài lab

```bash
imodule https://github.com/KhoaPTIT/dct-geo-redblue/raw/master/dct-geo-redblue.tar
```

---

# Khởi động bài lab

```bash
labtainer dct-geo-redblue
```

Nếu đang phát triển hoặc chỉnh sửa lab:

```bash
rebuild dct-geo-redblue
```

---

# Nội dung thực hành

## Task 1 — Tạo ảnh cover

Task này tạo ảnh cover dùng làm ảnh gốc cho toàn bộ bài lab.

Chạy:

```bash
python3 generate_cover.py
eog cover.png &
cat cover_report.txt
```

Sinh viên cần quan sát:

- `cover.png`: ảnh gốc.
- Các vùng ảnh có texture khác nhau.
- Nội dung trong `cover_report.txt`.

Kết quả checkwork:

```text
Y - task1_cover
```

Marker được kiểm tra:

```text
Cover image generated
```

---

## Task 2 — Nhúng thông điệp DCT

Task này nhúng thông điệp vào ảnh bằng DCT.

Chạy:

```bash
python3 embed_dct_message.py --alpha 32 --repeat 5
eog stego.png &
cat embed_report.txt
```

Sinh viên cần quan sát:

- `stego.png`: ảnh sau khi nhúng thông điệp.
- PSNR sau khi nhúng.
- Clean BER khi chưa có tấn công.
- Thông điệp khôi phục ban đầu.

Điều kiện cần quan sát:

```text
PSNR >= 35 dB
Clean BER <= 2%
Recovered message đúng: DCT GEO REDBLUE B22DCAT164
```

Kết quả checkwork:

```text
Y - task2_embed
```

---

## Task 3 — Trực quan block DCT và phổ tần số

Task này hiển thị lưới block DCT và phổ tần số của ảnh.

Chạy:

```bash
python3 visualize_dct_blocks.py
eog dct_block_grid.png &
eog dct_spectrum.png &
cat dct_visual_report.txt
```

Sinh viên cần quan sát:

- `dct_block_grid.png`: lưới block 8x8.
- `dct_spectrum.png`: phổ tần số DCT.
- Vùng tần số thấp, trung bình và cao.
- Vì sao watermark thường được nhúng ở mid-frequency.

Kết quả checkwork:

```text
Y - task3_dct_visual
```

---

## Task 4 — Translation attack

Task này thực hiện tấn công dịch chuyển ảnh.

Chạy:

```bash
python3 attack_translation.py --dx 3 --dy 5
eog attacks/translation_attack.png &
cat translation_report.txt
```

Sinh viên cần quan sát:

- `attacks/translation_attack.png`.
- Ảnh bị dịch nhẹ theo trục x và y.
- Vùng biên xuất hiện padding.
- BER có thể tăng dù ảnh nhìn không méo nhiều.

Kết quả checkwork:

```text
Y - task4_translation
```

---

## Task 5 — Crop-resize attack

Task này cắt ảnh rồi resize lại về kích thước ban đầu.

Chạy:

```bash
python3 attack_crop_resize.py --crop 12
eog attacks/crop_resize_attack.png &
cat crop_resize_report.txt
```

Sinh viên cần quan sát:

- `attacks/crop_resize_attack.png`.
- Một phần vùng biên bị mất.
- Resize làm thay đổi giá trị pixel.
- Lưới DCT ban đầu bị sai lệch.

Kết quả checkwork:

```text
Y - task5_crop_resize
```

---

## Task 6 — Rotation và scaling attack

Task này thực hiện hai tấn công: xoay ảnh và scale ảnh.

Chạy rotation:

```bash
python3 attack_rotation.py --angle 4
eog attacks/rotation_attack.png &
cat rotation_report.txt
```

Chạy scaling:

```bash
python3 attack_scaling.py --scale 0.92
eog attacks/scaling_attack.png &
cat scaling_report.txt
```

Sinh viên cần quan sát:

- `attacks/rotation_attack.png`.
- `attacks/scaling_attack.png`.
- Rotation tạo vùng padding ở góc ảnh.
- Scaling gây nội suy và làm thay đổi hệ số DCT.
- BER thay đổi trong các report.

Kết quả checkwork:

```text
Y - task6_rotation_scaling
```

---

## Task 7 — Perspective attack

Task này tạo biến dạng phối cảnh nhẹ lên ảnh.

Chạy:

```bash
python3 attack_perspective.py --strength 0.035
eog attacks/perspective_attack.png &
cat perspective_report.txt
```

Sinh viên cần quan sát:

- `attacks/perspective_attack.png`.
- Ảnh bị biến dạng phối cảnh.
- Lưới block bị sai lệch không đều.
- BER trong `perspective_report.txt`.

Kết quả checkwork:

```text
Y - task7_perspective
```

---

## Task 8 — Composite attack

Task này kết hợp nhiều phép biến đổi hình học cùng lúc.

Chạy:

```bash
python3 attack_composite.py --dx 4 --dy -3 --crop 8 --angle 3 --scale 0.95
eog attacks/composite_attack.png &
cat composite_report.txt
```

Sinh viên cần quan sát:

- `attacks/composite_attack.png`.
- Ảnh bị crop, dịch, xoay và scale đồng thời.
- BER thường cao hơn các attack đơn lẻ.
- Composite attack phá đồng bộ block DCT mạnh hơn.

Kết quả checkwork:

```text
Y - task8_composite
```

---

## Task 9 — Red Team tối ưu attack

Task này yêu cầu sinh viên chỉnh cấu hình để tạo attack mạnh nhưng vẫn giữ ảnh ở mức nhìn chấp nhận được.

Mở file cấu hình:

```bash
nano attack_config.py
```

Sửa thành ví dụ sau, quan trọng là xóa dòng `TODO`:

```python
MAX_ROTATION = 5
MAX_CROP = 14
MAX_TRANSLATION = 8
MIN_SCALE = 0.90
MAX_SCALE = 1.04
MAX_PERSPECTIVE = 0.04

MIN_PSNR = 28.0
MIN_SSIM = 0.82
TARGET_BER = 35.0
```

Chạy optimizer:

```bash
python3 attack_optimizer.py
eog attacks/optimized_attack.png &
eog raw_bit_error_map.png &
cat optimized_attack_report.txt
```

Sinh viên cần quan sát:

- `attacks/optimized_attack.png`.
- `raw_bit_error_map.png`.
- Optimized BER.
- PSNR và SSIM sau attack.
- Attack có làm ảnh méo quá rõ hay không.

Điều kiện pass:

```text
Optimized BER >= 35%
PSNR >= 28 dB
SSIM >= 0.82
attack_config.py không còn TODO
```

Kết quả checkwork:

```text
Y - task9_attack_optimizer
```

---

## Task 10 — Viết ATTACK_MEMO.md

Task này yêu cầu sinh viên viết memo phân tích kết quả tấn công.

Mở file:

```bash
nano ATTACK_MEMO.md
```

Có thể dùng nội dung mẫu này, nhưng nên thay số liệu theo report thật:

```markdown
# Attack Memo

## Strongest attack

The optimized attack caused the highest BER. In my run, the optimized BER was about 93.75 percent with PSNR about 37.10 dB and SSIM about 0.910. It was strongest because it shifted the original 8x8 DCT block grid while keeping the image visually acceptable.

## Visual quality

The optimized attack kept the image visually acceptable while still increasing BER. PSNR and SSIM were used to prevent overly obvious distortion.

## Why DCT extraction failed

DCT extraction failed because the extractor assumed the original 8x8 block grid. Geometric changes moved or distorted the grid, so extracted mid-frequency coefficient pairs no longer matched the embedded coefficients.

## Attack trade-off

Increasing crop, rotation, scale change, translation, or perspective strength can raise BER, but it also lowers PSNR and SSIM and may make the attack obvious.
```

Validate:

```bash
python3 validate_attack_memo.py
cat attack_memo_report.txt
```

Sinh viên cần đảm bảo:

- Không còn TODO.
- Có đủ các mục phân tích.
- Có nhắc tới BER.
- Có nhắc tới PSNR và SSIM.
- Có giải thích vì sao DCT extraction thất bại.

Kết quả checkwork:

```text
Y - task10_attack_memo
```

---

## Task 11 — Margin hardening defense

Task này tăng độ tách giữa các hệ số DCT dùng để biểu diễn bit.

Chạy:

```bash
python3 defense_margin_hardening.py --alpha 40 --margin 18
eog defended_stego.png &
cat margin_defense_report.txt
```

Sinh viên cần quan sát:

- `defended_stego.png`.
- PSNR sau khi tăng margin.
- BER sau optimized attack có giảm không.
- Trade-off giữa chất lượng ảnh và độ bền watermark.

Điều kiện pass:

```text
Defense PSNR >= 32 dB
BER after optimized attack giảm so với raw optimized BER
```

Kết quả checkwork:

```text
Y - task11_margin_defense
```

---

## Task 12 — Redundant quadrant embedding

Task này nhúng thông điệp lặp lại ở nhiều vùng ảnh khác nhau.

Chạy:

```bash
python3 defense_redundant_embed.py --copies 4 --alpha 36
eog defended_stego.png &
cat redundant_defense_report.txt
```

Sinh viên cần quan sát:

- Watermark được nhúng ở nhiều quadrant.
- Nếu một vùng bị hỏng, vùng khác vẫn còn dữ liệu.
- Dung lượng giảm do phải lưu nhiều bản sao.
- Nội dung trong `redundant_defense_report.txt`.

Kết quả checkwork:

```text
Y - task12_redundant_embed
```

---

## Task 13 — Offset voting defense

Task này thử nhiều offset lưới DCT khi trích xuất và vote kết quả.

Mở file cấu hình:

```bash
nano defense_config.py
```

Sửa thành ví dụ sau, quan trọng là xóa dòng `TODO`:

```python
OFFSET_RADIUS = 4
VOTE_MODE = "confidence"
MIN_CONFIDENCE = 0.55
USE_QUADRANT_VOTING = True
USE_CHECKSUM = True
```

Chạy offset voting:

```bash
python3 defense_offset_voting.py --image attacks/optimized_attack.png
eog offset_vote_map.png &
cat offset_voting_report.txt
```

Sinh viên cần quan sát:

- `offset_vote_map.png`.
- Offset nào cho kết quả tốt hơn.
- BER sau offset voting.
- Vote theo confidence có giúp giảm lỗi không.

Điều kiện pass:

```text
OFFSET_RADIUS >= 4
USE_QUADRANT_VOTING = True
BER after offset voting <= 18%
```

Kết quả checkwork:

```text
Y - task13_offset_voting
```

---

## Task 14 — Checksum recovery

Task này dùng checksum để chọn bản sao thông điệp đáng tin cậy.

Chạy:

```bash
python3 defense_checksum_recovery.py --image attacks/optimized_attack.png
eog checksum_recovery_map.png &
cat checksum_recovery_report.txt
```

Sinh viên cần quan sát:

- `checksum_recovery_map.png`.
- Bản sao nào được giữ lại.
- Bản sao nào bị loại bỏ.
- Final BER.
- Thông điệp sau recovery.

Điều kiện pass:

```text
Recovered message contains "DCT GEO REDBLUE"
Final BER <= 12%
```

Kết quả checkwork:

```text
Y - task14_checksum_recovery
```

---

## Task 15 — Tạo bằng chứng trực quan

Task này tạo contact sheet cho cả tấn công và phòng thủ.

Chạy:

```bash
python3 visualize_attack_effect.py
eog attack_contact_sheet.png &
cat attack_visual_report.txt
```

Chạy tiếp:

```bash
python3 visualize_defense_effect.py
eog defense_contact_sheet.png &
cat defense_visual_report.txt
```

Sinh viên cần quan sát:

- `attack_contact_sheet.png`.
- `defense_contact_sheet.png`.
- Ảnh gốc, ảnh stego, ảnh attacked.
- Bản đồ lỗi raw bit.
- Offset vote map.
- Checksum recovery map.
- Sự khác biệt trước và sau phòng thủ.

Kết quả checkwork:

```text
Y - task15_visual_evidence
```

---

## Task 16 — Viết DEFENSE_MEMO.md và tổng kết

Task này yêu cầu sinh viên viết memo phân tích phòng thủ, sau đó tạo summary và chạy checkwork.

Mở file:

```bash
nano DEFENSE_MEMO.md
```

Có thể dùng nội dung mẫu này, nhớ chỉnh số liệu theo report thật:

```markdown
# Defense Memo

## Raw attack result

The optimized geometric attack increased BER because the DCT extractor used the original block grid while the attacked image was shifted, cropped, rotated, scaled, or slightly warped. In my run, the raw optimized BER was about 93.75 percent.

## Margin hardening

Margin hardening increased the difference between DCT coefficient pairs. This improved robustness but reduced PSNR because stronger embedding changed the image more.

## Redundant embedding

Redundant quadrant embedding improved recovery because the same message was stored in multiple regions. If one region was damaged, another region could still contain usable bits.

## Offset voting

Offset voting reduced BER by testing nearby DCT block offsets and selecting reliable bits using confidence-based voting. In my run, offset voting reduced BER below 18 percent.

## Checksum recovery

Checksum recovery helped reject corrupted message copies and select the most reliable recovered payload. In my run, final BER was below 12 percent.

## Remaining weakness

The defense can still fail under severe crop, large rotation, perspective warp, strong compression, or attacks that damage all redundant regions.
```

Validate memo và tạo summary:

```bash
python3 validate_defense_memo.py
cat defense_memo_report.txt

python3 summary.py
cat summary_report.txt
```

Chạy checkwork cuối cùng:

```bash
checkwork
```

Kết quả checkwork:

```text
Y - task16_summary
```

---

# Checkwork

Sau khi hoàn thành đầy đủ, chạy:

```bash
checkwork
```

Kết quả mong đợi:

```text
Labname dct-geo-redblue

Y - task1_cover
Y - task2_embed
Y - task3_dct_visual
Y - task4_translation
Y - task5_crop_resize
Y - task6_rotation_scaling
Y - task7_perspective
Y - task8_composite
Y - task9_attack_optimizer
Y - task10_attack_memo
Y - task11_margin_defense
Y - task12_redundant_embed
Y - task13_offset_voting
Y - task14_checksum_recovery
Y - task15_visual_evidence
Y - task16_summary
```

---

# Dừng lab

Sau khi hoàn thành:

```bash
stoplab dct-geo-redblue
```


---

# Thông tin học phần

Hoàng Anh Khoa — B22DCAT164

Lớp: D22CQAT04-B

Học phần: Kỹ thuật giấu tin (INT14102)

Học viện Công nghệ Bưu chính Viễn thông (PTIT)

Giảng viên hướng dẫn: PGS.TS. Đỗ Xuân Chợ
