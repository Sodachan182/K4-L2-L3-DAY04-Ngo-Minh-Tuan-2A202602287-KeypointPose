# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Ngô Minh Tuấn   Nhóm: Cá nhân   Ngày: 16/09/2026

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 359 / 105 / 29 |
| Thời gian trung bình mỗi ảnh | Chưa có dữ liệu trong repo |

Ba khớp có `%v=1` cao nhất:

1. `left_ear`: 14/29, tương đương 48%.
2. `right_ear`: 9/29, tương đương 31%.
3. `left_wrist`: 9/29, tương đương 31%.

Tai và cổ tay là các vị trí thường bị tóc, mũ, tay hoặc vật thể che nên tỷ lệ `v=1` cao. Theo guideline, tôi dùng `v=1` khi khớp còn trong ảnh và có thể suy ra vị trí, còn `v=0` chỉ dành cho khớp thật sự ngoài khung. Các giá trị `v=0` tập trung ở đầu gối và cổ chân, phù hợp với những ảnh bị cắt phần chân; từng trường hợp vẫn cần được đối chiếu với ảnh thay vì chỉ dựa vào bảng đếm.

## 2. Chấm với gold

Không lưu snapshot eval trước rework; báo cáo sử dụng kết quả cuối cùng sau khi sửa.

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | Chưa có dữ liệu | 0.9207 |
| OKS@0.50 | Chưa có dữ liệu | 1.0000 |
| OKS@0.75 | Chưa có dữ liệu | 1.0000 |
| Lỗi `dao_trai_phai` | Chưa có dữ liệu | 0 |
| Lỗi `nham_nguoi` | Chưa có dữ liệu | 0 |
| Lỗi `xoa_khop_bi_che` | Chưa có dữ liệu | 0 |

Kết quả cuối cùng ghép đủ 29/29 người, không thiếu hoặc thừa skeleton. Mean OKS 0.9207 và OKS@0.75 bằng 1.0000 đều vượt gate của rubric. Kết quả không còn `dao_trai_phai`, `nham_nguoi`, `xoa_khop_bi_che` hay `truot_han`. Các finding còn lại gồm 11 `lech_nhe`, 44 `co_khac_gold` và 72 `gold_khong_gan_nhan`; hai nhóm cuối không bị trừ OKS theo README và RUBRIC.

**Tôi đã sửa gì giữa hai lần chạy:** Không có dữ liệu lưu lại. Kết quả cuối vẫn ghi nhận 11 điểm lệch nhẹ; annotation thấp nhất là `train_15.jpg`, người #1, OKS 0.8544, với `right_elbow` bị lệch nhẹ.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Kết quả cuối cùng không còn lỗi `dao_trai_phai`; không có dữ liệu lưu lại để xác định lỗi trước rework từng xuất hiện ở ảnh nào.

## 3. Kiểm chéo

Không áp dụng — bài thực hiện cá nhân, không có dữ liệu partner.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

Ngoài các chỉ số trong bảng, `box_mAP50` giảm từ 0.9785 xuống 0.9600, tức giảm 0.0185.

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` tăng 0.0055, từ 0.6853 lên 0.6908. `pose_mAP50` và recall không đổi, trong khi precision tăng 0.0058. Như vậy fine-tune tạo cải thiện nhỏ về chất lượng pose và precision trên tập test, nhưng chưa làm tăng tỷ lệ dự đoán đạt ngưỡng mAP50 hay recall.

2. Ở baseline, `box_mAP50-95` cao hơn `pose_mAP50-95` 0.1266 (0.8119 so với 0.6853). Sau fine-tune, chênh lệch còn 0.1133 (0.8041 so với 0.6908). Theo các số này, model tìm người dễ hơn định vị chính xác các khớp. Sau fine-tune, pose tốt lên nhẹ nhưng chất lượng box giảm nhẹ.

3. Ở `test_07.jpg`, model đặt các keypoint phần thân dưới xuống mặt bàn/quầy dù phần chân của người bị che phía sau quầy. Hai đầu gối và hai cổ chân bị kéo xa khỏi vị trí giải phẫu hợp lý, nên tôi xếp trường hợp này vào lỗi `truot_han`.

4. OKS model-vs-label thấp nhất là `0.417` ở `train_13.jpg`. Ảnh có ba người, trong đó hai người ở hậu cảnh nhỏ và bị mờ nên model dễ định vị sai. Annotation của tôi hợp lý hơn ở cấp độ toàn ảnh vì gold ghép đủ cả ba người, với OKS lần lượt là 0.8565, 0.8631 và 0.9424; không có lỗi đảo trái/phải, nhầm người hay trượt hẳn. Tuy nhiên output notebook không chỉ rõ giá trị 0.417 thuộc người nào, nên tôi không kết luận cụ thể model sai keypoint nào trên skeleton đó.

5. Ảnh annotation-vs-gold thấp nhất là `train_15.jpg`, người #1, OKS 0.8544; ảnh model-vs-label thấp nhất lại là `train_13.jpg`, OKS 0.417. Hai kết quả không trùng ảnh, cho thấy sai khác annotation so với gold và sai khác của model so với annotation không nhất thiết tập trung ở cùng một ảnh. `train_13.jpg` đặc biệt khó cho model vì có hai người nhỏ, mờ ở hậu cảnh.

## 5. Một rule evidence tôi đã dùng

Trong `train_03`, người #1 đứng phía trước có `left_hip`, hai đầu gối và hai cổ chân bị quần áo, cơ thể và xe đạp che khuất. Các khớp này vẫn nằm trong khung và có thể suy ra từ tư thế cùng các phần cơ thể liền kề, nên tôi đặt điểm giải phẫu ước lượng và chọn Occluded (`v=1`). Tôi không dùng `v=0` vì trạng thái Outside chỉ dành cho khớp thật sự nằm ngoài ảnh.
