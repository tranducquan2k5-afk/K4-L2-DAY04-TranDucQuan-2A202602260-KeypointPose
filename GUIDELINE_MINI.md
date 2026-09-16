# Mini guideline - nhóm:  |  người gán: Trần Đức Quân  |  ngày: 16/09/2026

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
| Hông của người mặc quần áo dài | Xác định mấu chuyển lớn xương đùi (greater trochanter). Nếu mặc quần dài ôm/vừa: đặt chấm ngang nếp gấp đáy chậu hoặc điểm lồi xương hông và gán `v = 2`. Nếu mặc áo khoác dài, áo choàng, hoặc váy xòe che mất đường nét cơ thể: ước lượng vị trí đối xứng qua trục xương sống và gán `v = 1` (Occluded). | Hông là khớp gốc định vị cho toàn bộ chi dưới. Quần áo che mất bề mặt da nhưng phom xương vẫn cố định; gán đúng `v = 1` giúp model học suy luận vị trí khớp bị che thay vì dự đoán sai lệch. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nếu tóc hoặc mép mũ che >50% diện tích vành tai hoặc đội mũ bảo hiểm trùm kín đầu: gán `v = 1`, đặt chấm tại gốc ống tai ngoài (ngang tầm mắt). Nếu chỉ có vài sợi tóc bay qua (<50%) vành tai vẫn nhận diện rõ: gán `v = 2`. | Tránh biến đặc trưng ngoại cảnh (tóc, mũ) thành đặc trưng của tai (`v = 2`), đồng thời bảo toàn tọa độ giải phẫu của đầu. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp nằm hoàn toàn ra ngoài viền ảnh (đầu gối, cổ chân): tick Outside (`o`), ra file `v = 0`, không đặt tọa độ chấm. Khớp nằm sát mép ảnh nhưng tâm khớp giải phẫu vẫn còn trong biên: tick `v = 1`. | Quy tắc COCO-17: khớp ra ngoài biên ảnh bắt buộc là `v = 0` để loại khỏi hàm tính loss định vị (không phạt sai số toạ độ ngoài biên). |
| Cổ tay nằm sau tay lái / sau thân mình | Ước lượng vị trí cổ tay dựa trên hướng kéo dài và độ dài xương cẳng tay từ khuỷu tay; tick Occluded (`q`), ra file `v = 1`, đặt chấm tại vị trí tay cầm hoặc sau lưng. | Cẳng tay là xương cứng có tỷ lệ nhân trắc học cố định, cho phép suy luận tọa độ cổ tay với sai số nằm trong bán kính OKS dung sai. |
| Hai người chồng lên nhau | Gán dứt điểm từng người một (không vẽ xen kẽ). Khớp của người bị người kia đè lên -> gán `v = 1` nếu vẫn trong khung ảnh, cẩn thận không bắt nhầm khớp của người đứng trước. | Ngăn ngừa lỗi "nhầm người" (swapping limbs) - lỗi nghiêm trọng làm model học sai cấu trúc liên kết xương của từng cá thể. |
| Người nhỏ đến mức nào thì không gán nữa | Gán tất cả mọi người có thể phân biệt được đầu và thân mình (chiều cao bounding box >= 20px). Chỉ bỏ qua hình nộm, người trong tranh vẽ/áp phích, hoặc người phản chiếu mờ qua kính. | Đảm bảo độ bao phủ (coverage) 100% người thật trong bộ ảnh benchmark COCO, tránh bị trừ điểm thiếu người. |

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

- **Ảnh mẫu Hông mặc quần dài:** `outputs/vis_train/train_08.jpg` (người 1: quần dài ôm, hông thấy rõ nếp gấp đáy chậu -> `v = 2`).
- **Ảnh mẫu Tai bị tóc/mũ che:** `outputs/vis_train/train_02.jpg` (người 1: tai bị tóc và góc nghiêng che khuất -> `v = 1`).
- **Ảnh mẫu Người bị cắt ở mép ảnh:** `outputs/vis_train/train_04.jpg` (người 1: nửa thân dưới bị mép ảnh/bàn che -> chân ngoài khung đánh `v = 0`).
- **Ảnh mẫu Cổ tay sau tay lái / vật che:** `outputs/vis_train/train_05.jpg` (người 1 đi xe đạp: cổ tay đặt trên ghi đông -> `v = 1`).
- **Ảnh mẫu Hai người chồng lên nhau:** `outputs/vis_train/train_03.jpg` (2 người đứng sát nhau, phân tách rõ từng bộ xương không kéo chéo).
- **Ảnh mẫu Người nhỏ trong bối cảnh:** `outputs/vis_train/train_01.jpg` (người đi phía sau có kích thước nhỏ nhưng vẫn đủ 17 khớp).

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_04.jpg`, người thứ `1`, khớp `left_knee` / `right_knee`

- Mơ hồ ở chỗ nào: Người ngồi đối diện tại bàn ăn, toàn bộ đùi và đầu gối bị mặt bàn và khăn trải bàn che khuất hoàn toàn. Phân vân giữa việc tick Outside (`v = 0`) vì không nhìn thấy gì hay Occluded (`v = 1`) và chấm ước lượng dưới gầm bàn.
- Bạn quyết thế nào: Quyết định chọn `v = 1` (Occluded) và đặt chấm ước lượng tại vị trí giải phẫu đầu gối nằm dưới mặt bàn theo tư thế ngồi gập 90 độ.
- Vì sao: Toàn bộ người nằm gọn giữa ảnh, khoảng cách tới viền ảnh còn rất xa. Khớp chắc chắn còn trong khung hình (in-frame), chỉ bị vật thể (mặt bàn) che khuất, do đó theo contract chuẩn bắt buộc phải là `v = 1`.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu đánh `v = 0`, model sẽ học sai rằng hễ người ngồi sau bàn hoặc bị vật che phía trước là chân bị cắt cụt ngoài mép ảnh, làm hỏng khả năng suy luận dáng người bị che khuất (occlusion reasoning).

### Ca 2 - ảnh `train_02.jpg`, người thứ `1`, khớp `left_shoulder` / `right_shoulder`

- Mơ hồ ở chỗ nào: Người đứng quay lưng về phía máy ảnh nhưng ngoái đầu nhìn sang bên. Trục hai mắt hướng ngược chiều so với trục hai vai, khiến script kiểm tra hình dáng cảnh báo nghi ngờ đảo trái/phải.
- Bạn quyết thế nào: Giữ nguyên quy ước vai trái là vai bên trái của cơ thể người (nằm bên trái ảnh do nhìn từ sau lưng), không đổi nhãn theo hướng quay của khuôn mặt.
- Vì sao: Quy ước COCO-17 định danh khớp trái/phải theo hệ quy chiếu giải phẫu sinh học của cơ thể (anatomical body frame), không phụ thuộc vào góc xoay của đầu hay hướng quan sát của camera.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model sẽ bị học sai nhãn đảo trái/phải (`dao_trai_phai`). Khi áp dụng data augmentation lật ảnh ngang (`fliplr = 0.5`), model sẽ bị dạy hai mẫu mâu thuẫn hoàn toàn, dẫn đến phá hủy biểu diễn hình học của bộ xương người.

### Ca 3 - ảnh `train_13.jpg`, người thứ `3`, khớp `left_ankle` / `right_ankle`

- Mơ hồ ở chỗ nào: Người đứng trong khu vực bóng râm cạnh phương tiện giao thông, bàn chân và mắt cá chân bị bóng tối và bánh xe che khuất một phần, ranh giới mắt cá chân không rõ rệt.
- Bạn quyết thế nào: Gán `v = 1`, đặt chấm tại vị trí trên đế giày khoảng 4cm dọc theo phương thẳng đứng của cẳng chân từ đầu gối hạ xuống.
- Vì sao: Chiều dài cẳng chân có tỷ lệ nhân trắc học ổn định so với đùi và thân thể, cho phép nội suy tọa độ mắt cá chân nằm gọn trong bán kính dung sai OKS ($\kappa = 0.089$).
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu đánh `v = 0` hoặc chấm trượt xuống mặt đường, model sẽ học sai chiều dài cẳng chân và có xu hướng dự đoán khớp mắt cá chân bám vào bóng đổ hoặc mặt đất.

## 4. Sau khi so visibility report với bạn cùng nhóm


