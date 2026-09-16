# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Trần Đức Quân   Nhóm: 2A   Ngày: 16/09/2026

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

<!-- Lấy số từ reports/visibility_report.md hoặc outputs/visibility_report.json sau Chặng 4.
Số ảnh phải là 20; số skeleton là tổng số người trong 20 ảnh. Thời gian trung bình = tổng
thời gian gán / 20. -->

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 349 / 119 / 25 |
| Thời gian trung bình mỗi ảnh | 3.8 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear`: 59% (17 / 29)
2. `right_ear`: 52% (15 / 29)
3. `left_wrist`: 28% (8 / 29) *(tiếp theo là right_wrist 24%, nose 24%)*

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Không hoàn toàn. Khớp tai (`left_ear`, `right_ear`) có tỉ lệ `v = 1` cao nhất do đặc thù góc nhìn nghiêng/sau hoặc bị tóc và mũ che phủ, nhưng đây không phải là khớp khó định vị nhất vì vị trí giải phẫu của tai dễ dàng nội suy theo đường ngang mắt và cung gò má. Ngược lại, khớp khó gán nhất trên thực tế là **hông** (`left_hip`, `right_hip`, 21% `v = 1`) và **cổ tay** (`left_wrist`, `right_wrist`) khi bị che bởi quần áo rộng hoặc vật cầm nắm; việc tìm đúng mấu chuyển lớn xương đùi (greater trochanter) dưới nhiều lớp vải đòi hỏi phải căn cứ vào trục xương sống, nếp gấp háng và hướng cử động của đùi thay vì quan sát trực tiếp.

## 2. Chấm với gold

<!-- Lấy hai cột từ outputs/eval_vs_gold.json: một lần ngay khi protected release mở và một
lần sau rework. Đếm số phần tử trong từng danh sách lỗi, không tự làm tròn. -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.8920 | 0.9435 |
| OKS@0.50 | 0.9655 | 1.0000 |
| OKS@0.75 | 0.8966 | 1.0000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

<!-- Mỗi dòng phải có: tên ảnh + người thứ mấy + keypoint + thao tác sửa. Không viết “đã sửa
lại một số lỗi”. -->

- `train_04.jpg` + người 2 + `left_hip`, `right_hip`, `left_knee`, `right_knee`: chuyển các khớp bị mặt bàn che khuất từ tọa độ lệch ngoài viền sang chấm ước lượng chuẩn tâm giải phẫu dưới mặt bàn với cờ `v = 1`.
- `train_13.jpg` + người 3 + `left_ankle`, `right_ankle`: kéo điểm mắt cá chân từ vị trí sát mặt đất lên đúng đỉnh mắt cá trong/ngoài của chân (giảm độ lệch từ 18px về mức dung sai).
- `train_16.jpg` + người 1 + `nose`, `right_eye`: tinh chỉnh tâm khớp từ viền ngoài khuôn mặt vào đúng gốc sống mũi và đuôi mắt (giảm độ lệch từ 20px / 14px về tâm giải phẫu).
- `train_02.jpg` + người 1 + `left_ear`, `right_hip`: kiểm tra lại tư thế quay lưng ngoái đầu, chuẩn hóa tọa độ tai trái và hông phải theo khung xương giải phẫu.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh (lỗi `dao_trai_phai`: 0).
Các cảnh báo ban đầu từ script `check_pose_labels.py` ở `train_02.txt`, `train_13.txt`, `train_16.txt` là do đối tượng đứng xoay nghiêng hoặc ngoái đầu nhìn lại phía sau khiến vector hai mắt ngược chiều với vector hai vai/hông. Khi kiểm tra trực quan bằng `tools/visualize_pose.py` và đối chiếu với gold dataset, toàn bộ nhãn trái/phải đều tuân thủ chính xác theo giải phẫu cơ thể người.

## 3. Kiểm chéo

Bạn cùng nhóm: Nguyễn Văn A (Nhóm 2A)

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_ear` | 59% | 41% | 18% | Guideline: tiêu chí phân biệt tóc mỏng che tai (v=2) vs che khuất vành tai (v=1) chưa rõ ràng |
| `left_hip` | 21% | 45% | 24% | Guideline: cách xác định cờ cho khớp hông khi mặc quần áo dài chưa thống nhất định lượng |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

<!-- Viết một rule kiểm chứng được: điều kiện nhìn thấy/căn cứ vị trí → chọn v=1 hoặc v=0.
Không chỉ ghi “cẩn thận hơn khi gán”. -->

- Tai nếu bị tóc hoặc mép mũ che >50% diện tích vành tai thì bắt buộc gán `v = 1` kèm chấm ước lượng tại gốc ống tai ngoài; nếu tóc thưa che <50% vẫn thấy rõ sụn vành tai thì giữ `v = 2`.
- Hông khi người mặc quần dài ôm vừa vặn nhìn rõ hõm hông và nếp gấp đùi-hông thì gán `v = 2`; chỉ gán `v = 1` khi mặc váy xòe rộng hoặc áo khoác dài trùm qua mông làm mất hoàn toàn trục chuyển động của đùi.

## 4. Model

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. “Chênh” = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

*(Ghi chú bổ sung: `box_mAP50` gốc là 0.9785, sau fine-tune là 0.9600, chênh -0.0185).*

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model
   điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?

   - `pose_mAP50-95` tăng nhẹ từ 0.6853 lên 0.6908 (+0.0055, tương đương +0.55%), `pose_precision` cũng tăng từ 0.9734 lên 0.9792 (+0.0058), trong khi `pose_mAP50` và `pose_recall` giữ nguyên ở mức 0.8450 và 0.8462.
   - Nhãn 20 ảnh của bài được gán với độ chính xác hình học cao (mean OKS = 0.9435 so với gold), hoàn toàn sạch lỗi đảo trái/phải và nhầm người. Điều này dạy model ước lượng vị trí khớp chặt chẽ hơn ở các ngưỡng IoU/OKS khắt khe (tăng mAP50-95). Tuy nhiên, vì kích thước tập dữ liệu cực nhỏ (20 ảnh), model có dấu hiệu bắt đầu overfit nhẹ vào đặc trưng bối cảnh của 20 ảnh train, thể hiện qua việc `box_mAP50-95` giảm nhẹ (-0.0078 từ 0.8119 xuống 0.8041) do detector bị thu hẹp nhẹ vùng tổng quát hóa bounding box.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm
   *khớp* dễ hơn? Vì sao?

   - Ở baseline gốc: `box_mAP50-95` đạt 0.8119 trong khi `pose_mAP50-95` đạt 0.6853 (chênh 0.1266, tương đương 12.66%); ở mAP50: `box_mAP50` đạt 0.9785 còn `pose_mAP50` là 0.8450 (chênh 0.1335).
   - Model tìm *người* (bounding box) dễ hơn tìm *khớp* (keypoints) rất nhiều.
   - Vì bounding box chỉ cần nắm bắt các đặc trưng ngữ cảnh vùng thô (vùng hình chữ nhật bao quanh toàn thân người với dung sai lớn). Ngược lại, keypoints đòi hỏi định vị chính xác ở cấp độ điểm ảnh (pixel-level) cho từng khớp nối giải phẫu. Hơn nữa, thước đo OKS áp dụng hệ số dung sai $\kappa$ theo từng khớp rất khắt khe; các khớp như cổ tay, khuỷu tay, mắt cá chân thường xuyên bị biến dạng phi tuyến, co gập, quay góc hoặc bị che khuất, khiến bài toán hồi quy 17 điểm phức tạp và nhạy cảm hơn nhiều so với dự đoán 4 tọa độ hộp bao.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43
   (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):

   - Trong ảnh `test_04.jpg` (người ngồi ăn tại bàn): Người ngồi ở góc bàn ăn, thân dưới bị che khuất hoàn toàn bởi mặt bàn và đĩa thức ăn. Model gặp lỗi **lệch nhẹ** ở khuỷu tay trên mặt bàn và lỗi **trượt hẳn** khi cố đoán khung xương chân ở vùng tối dưới gầm bàn.
   - Trong ảnh `test_07.jpg` (người phụ nữ bán hàng sau quầy bánh): Chỉ có nửa thân trên xuất hiện sau tủ kính, nửa thân dưới hoàn toàn nằm ngoài ảnh. Model phát hiện đúng box người nhưng gặp lỗi **trượt hẳn** khi phóng chiếu các khớp hông, gối, cổ chân xuống đáy tủ kính thay vì nhận diện là khớp ngoài khung.
   - Trong ảnh `test_02.jpg` (người đứng xa bên trái): Do đối tượng quá nhỏ và mờ, model phát hiện box (`person 0.31`) nhưng các khớp đầu và thân bị **lệch nhẹ** và cụm lại một chỗ.

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?

   - Theo bảng so sánh OKS giữa nhãn và model (ở Mục 6 notebook): ảnh có OKS thấp nhất là `train_13` (OKS = 0.559 cho người thứ nhất và 0.671 cho người thứ hai), tiếp theo là `train_06` (OKS = 0.620). Ngoài ra `train_10` và `train_03` bị lệch số người.
   - **Nhãn của bạn đúng, model sai.**
   - Căn cứ: Đối chiếu với kết quả chấm chuẩn `outputs/eval_vs_gold.json`, nhãn của bạn trên ảnh `train_13` đạt OKS so với gold lần lượt là 0.7941, 0.8545, và 0.9884 (không có lỗi đảo trái/phải hay nhầm người). Trong `train_13`, đối tượng ở tư thế phức tạp, có sự che khuất chéo giữa người và phương tiện/bóng đổ. Model YOLO26n-pose bị hạn chế khi suy luận các tư thế bị che khuất cục bộ (occluded pose) và đoán lệch nhiều khớp, trong khi nhãn của bạn đã xác định đúng trục giải phẫu và đặt cờ `v = 1` chính xác.

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó
   nói gì về bức ảnh đó?

   - Có. Ảnh nhãn của bạn có điểm OKS thấp nhất so với gold chính là `train_13` (người 1 đạt OKS 0.7941, thấp nhất trong 29 skeleton). Đồng thời, ở bảng so sánh với model, `train_13` cũng đứng đầu danh sách OKS thấp nhất giữa model và bạn (OKS = 0.559).
   - Điều này khẳng định `train_13` là một **mẫu dữ liệu khó khách quan** (inherently ambiguous / hard sample): Bức ảnh có nhiều đối tượng đứng gần nhau, nhiều khớp bị che khuất và bóng đổ phức tạp. Cả tri giác của con người khi gán nhãn lẫn biểu diễn nơ-ron của model học sâu đều có độ bất định (uncertainty) cao nhất tại bức ảnh này.

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người,
khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.

Trong ảnh `train_04.jpg`, người thứ 1 (người ngồi đối diện tại bàn ăn), tôi phải quyết định trạng thái cờ cho khớp `left_knee` (đầu gối trái). Về mặt thị giác, phần thân trên và hai cánh tay đặt trên bàn ăn nhìn thấy rất rõ, trong khi toàn bộ đùi và đầu gối bị mặt bàn và khăn trải bàn che khuất hoàn toàn; tuy nhiên, đối tượng ngồi trọn vẹn ở trung tâm khung hình và sàn nhà dưới gầm bàn vẫn nằm trong ảnh. Tôi quyết định chọn cờ `v = 1` (Occluded) và chấm điểm ước lượng tại vị trí giải phẫu đầu gối gập dưới bàn thay vì chọn `v = 0`. Lý do là khớp đầu gối chắc chắn vẫn nằm trong biên ảnh (in-frame), chỉ bị vật thể ngoại cảnh che khuất, nên theo đúng quy định contract của lab bắt buộc phải gắn `v = 1` để dạy model khả năng suy luận dáng người bị che.
