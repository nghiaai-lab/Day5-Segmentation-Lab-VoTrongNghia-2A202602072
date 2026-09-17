# Lab Map — Day 5 Segmentation

Repo cá nhân: `https://github.com/nghiaai-lab/Day5-Segmentation-Lab-VoTrongNghia-2A202602072`
Đường dẫn local dùng để làm bài: `D:\VinUni\Day5-Segmentation-Lab-VoTrongNghia-2A202602072`

## Phạm vi và thứ tự

- 9 task, 14 ảnh JPG, tối đa 100 điểm: Easy 20, Medium 32, Hard 30, sáu checkpoint × 3.
- Thứ tự: `easy_semantic` → `medium_instance` → `hard_panoptic` → `cp1_holes` → `cp2_slice` → `cp5_occlusion` → `cp3_thin` → `cp4_curb` → `cp6_coverage`.
- Mỗi task là một task CVAT riêng. Chỉ đưa JPG trong `images/` của chính task đó vào CVAT. `classes.json` quyết định tên class; `cvat-labels.json` là nội dung cho **Labels → Raw**. Đã đối chiếu: tên và thứ tự nhãn của hai file khớp ở cả 9 task.
- Shape cần là vùng **Mask/Polygon**, không phải polyline hoặc bounding box. Vẽ phần nhìn thấy; kiểm biên, class, vật thiếu/thừa và vùng bỏ sót; Save rồi chuyển ảnh/quay lại xác nhận.
- Sau Save, xuất ZIP trực tiếp từ **Job → Menu → Export job dataset**. Chỉ đổi tên ZIP bên ngoài; không sửa JSON/PNG bên trong.

## Ảnh, nhãn, export và đầu ra

Mọi đường dẫn trong bảng dưới đây tính từ gốc repo local nêu trên. Danh sách JPG đã đối chiếu với file thực tế trên đĩa và đủ **14/14 ảnh**.

| Task | JPG cần chọn trong CVAT | Class chính xác | Format CVAT | ZIP tại `submissions/` | Điểm | Điểm kiểm đặc thù |
| --- | --- | --- | --- | --- | ---: | --- |
| `easy_semantic` | `data/tiers/easy_semantic/images/7ee6d192-89e2408b.jpg`<br>`data/tiers/easy_semantic/images/817bca71-00000000.jpg`<br>`data/tiers/easy_semantic/images/81ae7cbb-6bc63a4a.jpg` | `road`, `sidewalk`, `building`, `vegetation`, `sky` | Segmentation mask 1.1 | `easy_semantic.zip` | 20 | Vùng semantic; ranh road–sidewalk dựa vào chức năng/bó vỉa, không chỉ màu. |
| `medium_instance` | `data/tiers/medium_instance/images/000000181542.jpg`<br>`data/tiers/medium_instance/images/000000373353.jpg`<br>`data/tiers/medium_instance/images/000000458325.jpg` | `person`, `bicycle`, `car`, `motorcycle`, `bus`, `truck` | COCO 1.0 | `medium_instance.zip` | 32 | Mỗi vật một instance. Tự vẽ object đầu tiên trước gợi ý; ghi ảnh, vị trí, class và lý do chọn biên vào report. |
| `hard_panoptic` | `data/tiers/hard_panoptic/images/000000350023.jpg`<br>`data/tiers/hard_panoptic/images/000000460147.jpg` | `road`, `sidewalk`, `building`, `vegetation`, `sky`, `person`, `car`, `bus`, `truck`, `motorcycle`, `bicycle`, `traffic light` | COCO 1.0 | `hard_panoptic.zip` | 30 | Năm class đầu là stuff; bảy class còn lại là thing cần tách từng instance. Kiểm chồng lấn và khoảng trống. |
| `cp1_holes` | `data/checkpoints/cp1_holes/images/000000144300.jpg` | `person`, `bicycle`, `car`, `motorcycle`, `bus`, `truck` | COCO 1.0 | `cp1_holes.zip` | 3 | Kính/khe ở trong mask vật theo quy tắc trạm; không khoét tùy tiện. |
| `cp2_slice` | `data/checkpoints/cp2_slice/images/000000017627.jpg` | `person`, `bicycle`, `car`, `motorcycle`, `bus`, `truck` | COCO 1.0 | `cp2_slice.zip` | 3 | Hai xe sát nhau vẫn là hai instance. |
| `cp5_occlusion` | `data/checkpoints/cp5_occlusion/images/000000336232.jpg` | `person`, `bicycle`, `car`, `motorcycle`, `bus`, `truck` | COCO 1.0 | `cp5_occlusion.zip` | 3 | Vật bị che vẫn được đếm là một instance; không tô xuyên phần khuất. |
| `cp3_thin` | `data/checkpoints/cp3_thin/images/839f7736-abe28069.jpg` | `pole`, `traffic sign`, `sky`, `road` | Segmentation mask 1.1 | `cp3_thin.zip` | 3 | Phóng to, dùng brush nhỏ khoảng 2–3 px cho cột/biển; `traffic sign` có dấu cách. |
| `cp4_curb` | `data/checkpoints/cp4_curb/images/7d83710e-4697c3b2.jpg` | `road`, `sidewalk` | Segmentation mask 1.1 | `cp4_curb.zip` | 3 | Ranh theo chức năng/bó vỉa dù màu mặt đường gần giống. |
| `cp6_coverage` | `data/checkpoints/cp6_coverage/images/7daa6479-67988f3f.jpg` | `road`, `sidewalk`, `building`, `vegetation`, `sky`, `car`, `person` | Segmentation mask 1.1 | `cp6_coverage.zip` | 3 | Phủ vùng nhìn thấy thuộc các class của task, kiểm khe trống; ghi ca không chắc thay vì tô đoán. |

## Mốc kiểm và bằng chứng

1. **Trước mỗi task:** kiểm tên task, đúng tập ảnh, đúng số/tên label từ `classes.json`. Nếu task đã có annotation, không dán đè Raw.
2. **Trong CVAT:** tạo mask/Polygon **Shape**, kiểm Objects trên từng frame. Với instance, hai vật cùng class sát nhau vẫn hai object; một vật bị che có thể có nhiều phần nhìn thấy nhưng vẫn một instance.
3. **Sau mỗi task:** bấm Save, chuyển frame và quay lại, export đúng format, đặt ZIP đúng tên vào `submissions/`.
4. **Tự kiểm cấu trúc:** xem GitHub Actions **Day 5 self-check → Summary** sau push, hoặc chạy `scripts/inspect_submissions.py --dir submissions` bằng Python 3.10+ tại gốc repo. Máy hiện tại chưa có lệnh `py`/`python` trong PATH; Codex đã chạy được script bằng Python bundled với tùy chọn `-X utf8`. `OK` chỉ xác nhận cấu trúc ảnh/class/mask, không chứng minh đường biên hoặc số vật đúng.
5. **Report:** điền bốn mục `REPORT.md`: danh sách task/ảnh/ZIP thực tế; object Medium đầu tự vẽ; một lỗi thật đã sửa; ba ca cụ thể đã cân nhắc. Không bịa điểm, PASS, bonus hay tình trạng Save/export.
6. **Reference:** chỉ dùng ground truth khi người phụ trách phát chính thức trong 60 phút cuối; không truy cập sớm, không commit/push hoặc đưa vào ZIP bài nộp. Checkpoint không nằm trong reference ba tier trừ khi được công bố riêng.
7. **Kết thúc:** đủ ZIP của task đã làm, `REPORT.md` không còn dấu `…`, self-check cấu trúc đạt, commit/push lên chính repo cá nhân trên `origin`. Nộp link repo qua VLearn theo hạn lớp công bố.

Nguồn quy tắc: `README.md`, `GUIDE.md`, `RUBRIC.md`, `REPORT.md`, `guideline-mini-sheet.md`, `CVAT_SETUP.md`, `lab-guide.html`, `data/manifest.json` và các cặp `classes.json`/`cvat-labels.json` của từng task.
