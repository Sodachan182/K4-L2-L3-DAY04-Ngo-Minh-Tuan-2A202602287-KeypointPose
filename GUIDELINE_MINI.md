# Mini guideline - nhóm: Cá nhân  |  người gán: Ngô Minh Tuấn  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Nếu tâm khớp không nhìn thấy trực tiếp nhưng vẫn nằm trong ảnh và có thể suy ra theo giải phẫu, đặt điểm ước lượng và chọn Occluded (`v=1`). Không dùng `v=0` chỉ vì quần áo che. | Khớp vẫn tồn tại trong khung; bị che khác với ra ngoài ảnh. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Tâm tai còn nhìn đủ rõ thì dùng `v=2`; nếu phải suy ra do tóc hoặc mũ che thì dùng `v=1`; chỉ dùng `v=0` khi tai thật sự ngoài khung. | Visibility phụ thuộc bằng chứng quan sát được và vị trí khớp so với khung ảnh. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Khớp còn trong ảnh được gán bình thường, hoặc `v=1` nếu bị che. Khớp giải phẫu nằm ngoài khung dùng Outside (`v=0`) và không đoán tọa độ. | Không biến khớp ngoài ảnh thành một điểm ước lượng trong ảnh. |
| Cổ tay nằm sau tay lái / sau thân mình | Nếu cổ tay còn trong khung nhưng bị vật hoặc cơ thể che, đặt điểm giải phẫu ước lượng và dùng `v=1`. | Cổ tay bị che vẫn là keypoint hợp lệ để model học. |
| Hai người chồng lên nhau | Mỗi người có một skeleton riêng đủ 17 keypoint. Hoàn thiện từng người một; khớp bị người kia che nhưng còn suy ra được dùng `v=1`. | Giữ đúng định danh từng người và tránh kéo keypoint sang skeleton khác. |
| Người nhỏ đến mức nào thì không gán nữa | Với dataset DAY04, không loại người chỉ vì nhỏ; gán tất cả người thuộc core task. Ca khó xác định phải ghi vào mục ca mơ hồ, không tự bỏ skeleton. | Bộ dữ liệu đã chọn người đủ lớn và rubric yêu cầu bao phủ mọi người. |

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

Ảnh minh họa CVAT chưa được lưu trong repo. Các ảnh ở `outputs/vis_train/` là kết quả
visualization của công cụ, không phải screenshot CVAT.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_02`, người thứ `1`, khớp `left/right shoulder và hip`

- Mơ hồ ở chỗ nào: đầu người quay khác hướng với thân, nên thứ tự trái/phải của mắt trên trục X không cùng chiều với thứ tự trái/phải của vai và hông. Validator vì vậy từng cảnh báo dấu hiệu đảo trái/phải ở shoulder/hip.
- Bạn quyết thế nào: xác định trái/phải theo giải phẫu của chính người. Không đổi shoulder/hip chỉ để làm mất warning nếu chuỗi shoulder → elbow → wrist và hip → knee → ankle vẫn đúng giải phẫu.
- Vì sao: người có thể quay đầu độc lập với thân; heuristic so chiều của mắt với vai/hông có thể cho false positive.
- Nếu người khác quyết ngược lại thì model học sai cái gì: model có thể học sai semantic left/right của tay và chân.

![Ảnh core train_02 dùng để kiểm tra hướng đầu và thân](dataset/images/train/train_02.jpg)

### Ca 2 - ảnh `train_01`, người thứ `1 và 2`, khớp `left/right knee và ankle`

- Mơ hồ ở chỗ nào: phần chân phía dưới của cả hai người bị cắt bởi mép dưới ảnh; validator cảnh báo mỗi skeleton có nhiều điểm `v=0`.
- Bạn quyết thế nào: các knee/ankle có vị trí giải phẫu thật sự ngoài khung được đặt Outside (`v=0`); không đổi thành `v=1` chỉ để hết warning.
- Vì sao: luật DAY04 quy định `v=0` cho khớp nằm ngoài ảnh và không đoán tọa độ cho điểm đó.
- Nếu người khác quyết ngược lại thì model học sai cái gì: model và metric sẽ coi khớp ngoài ảnh như khớp bị che trong ảnh, làm sai visibility.

![Ảnh core train_01 có phần chân bị cắt ở mép dưới](dataset/images/train/train_01.jpg)

### Ca 3 - ảnh `train_03`, người thứ `1`, khớp `left_hip, left/right knee và left/right ankle`

- Mơ hồ ở chỗ nào: hông, chân và cổ chân của người phía trước bị quần áo, cơ thể và xe đạp che khuất nhưng vẫn nằm trong khung.
- Bạn quyết thế nào: đặt các điểm ở vị trí giải phẫu ước lượng và chọn Occluded (`v=1`).
- Vì sao: annotation thực tế của người #1 giữ các khớp này trong ảnh với `v=1`; bị che khác với ra ngoài khung.
- Nếu người khác quyết ngược lại thì model học sai cái gì: dùng `v=0` sẽ loại khỏi huấn luyện các keypoint vẫn còn trong ảnh và có thể suy ra.

![Ảnh core train_03 với người thứ 1 ở phía trước](dataset/images/train/train_03.jpg)

## 4. Sau khi so visibility report với bạn cùng nhóm

Không áp dụng — bài thực hiện cá nhân, không có dữ liệu visibility của partner để so sánh.
