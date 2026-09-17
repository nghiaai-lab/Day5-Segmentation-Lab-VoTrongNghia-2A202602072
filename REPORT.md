# Báo cáo Day 5 — repo cá nhân

- Mã học viên theo tên repo: 2A202602072
- Ngày / CVAT local: 17/09/2026 / http://localhost:8080
- Công cụ đã dùng: CVAT local (vẽ polygon, Save và export), Codex hỗ trợ thao tác và kiểm ZIP bằng `scripts/inspect_submissions.py`. Không dùng Colab, Jupyter hoặc SAM.

## 1. Bài đã nộp

Các số dưới đây là số ảnh đã chú thích và Save trong CVAT. Cột điểm là điểm tối đa, không phải điểm tự chấm.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Các mask được vẽ bằng polygon thủ công. Nhiều ranh nhỏ và vùng bị che vẫn còn thô; kiểm cấu trúc ZIP không xác nhận độ chính xác hình học. Sau khi được xác nhận đáp án đã phát, tôi chạy scorer local trên ba tier: Easy 13,0/20 (mIoU 0,694), Medium 0,0/32 (metric 0,311), Hard 7,3/30 (PQ 0,310), tổng **20,3/82**. Đây là phản hồi kỹ thuật, không phải điểm chính thức, PASS hay bonus. Easy đã được sửa trong CVAT sau khi xem reference; bản export trước đó còn trong commit `7edc9cd` và bản sửa không phải bằng chứng độc lập trước khi phát đáp án. Không có ground truth trong repo hay ZIP bài nộp.

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: `000000181542.jpg`, người phụ nữ mặc đồ sáng ở tiền cảnh gần giữa ảnh.
- Class và quy tắc chọn biên: `person`; theo đường bao phần cơ thể/quần áo nhìn thấy (đầu, vai, tay, thân và chân), dừng ở ranh với mặt đường và vật che. Đây là một instance riêng.
- Gợi ý tự động: không dùng. Tôi giữ đối tượng người riêng khỏi các xe xung quanh theo ranh nhìn thấy, không lấy vùng nền nằm giữa hai chân vào mask người.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: `medium_instance`, `000000181542.jpg`, ranh xe buýt với taxi.
- Loại lỗi: biên và gộp nhầm vùng.
- Bằng chứng: polygon xe buýt vẽ lần đầu lấn sang phần taxi liền kề trên ảnh.
- Hành động sửa: dùng Undo, vẽ lại phần xe buýt theo thân xe còn nhìn thấy và để taxi là instance riêng.
- Sau sửa: đã Save trong CVAT và export lại `medium_instance.zip`.
- Kiểm cấu trúc ZIP: script báo `[OK]` cho task. Sau khi đáp án được phát, scorer local cho Medium metric 0,311 và 0,0/32; metric này đánh giá cả task, không đo riêng lỗi xe buýt đã sửa.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `000000181542.jpg`, taxi sát xe buýt | Một khối xe chung hoặc hai instance | Hai thân xe có ranh và độ che khuất khác nhau | Tách xe buýt và taxi; polygon theo phần nhìn thấy. |
| `7d83710e-4697c3b2.jpg`, mép vỉa hè và dải đất sát đường | Gộp dải đất vào sidewalk hoặc chỉ lấy mặt vỉa rõ | Mép bó vỉa là đường đổi cao độ/bề mặt; dải đất không phải mặt đi bộ rõ ràng | Tách road và sidewalk ở mép bó vỉa; dải đất không gán sidewalk. |
| `7daa6479-67988f3f.jpg`, xe buýt lớn và xe tải trái ảnh | Gán chung `car` để phủ hết hoặc để nền | Bộ nhãn CP6 có `car` nhưng không có `bus`/`truck`; hình xe buýt và xe tải rõ khác ô tô | Để hai xe ở nền thay vì gán sai class; mong coach xác nhận cách xử lý phạm vi nhãn. |
