# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Nguyễn Hoài Thanh | Nhóm: Không có | Ngày: 17/09/2026

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

| Chỉ số                       |        Giá trị |
| ---------------------------- | -------------: |
| Số ảnh đã gán                |             20 |
| Số skeleton                  |             29 |
| v=2 / v=1 / v=0              | 344 / 118 / 31 |
| Thời gian trung bình mỗi ảnh |      ~2.5 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` — 66% (19/29)
2. `right_ear` — 45% (13/29)
3. `left_wrist` — 34% (10/29)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Không hoàn toàn. Hai khớp tai (`left_ear`, `right_ear`) có tỉ lệ `%v=1` cao nhất chủ yếu do đây là những vị trí **hay bị che** bởi tóc, mũ nón hoặc do góc quay nghiêng của đầu, tuy nhiên vị trí giải phẫu của tai lại tương đối dễ suy đoán nhờ dóng ngang từ trục mắt sang thái dương. Khớp thực sự **khó xác định vị trí giải phẫu nhất** khi gán là `left_wrist` (cổ tay) và hai khớp hông (`left_hip`, `right_hip`), vì cổ tay thường bị khuất sau tay lái/thân mình hoặc lẫn vào người bên cạnh, còn hông thì bị quần áo rộng hoặc áo khoác phủ kín làm mất hoàn toàn mốc xương chậu, buộc phải dựa vào nếp gấp eo và trục đùi để ước lượng.

## 2. Chấm với gold

| Chỉ số                | Trước rework | Sau rework |
| --------------------- | -----------: | ---------: |
| OKS trung bình        |        0.961 |      0.961 |
| OKS@0.50              |        1.000 |      1.000 |
| OKS@0.75              |        1.000 |      1.000 |
| Lỗi `dao_trai_phai`   |            0 |          0 |
| Lỗi `nham_nguoi`      |            0 |          0 |
| Lỗi `xoa_khop_bi_che` |            0 |          0 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

- Toàn bộ 29 skeleton đều đạt OKS ≥ 0.75 ngay từ lần chạy đầu tiên, hệ thống thông báo _"Không có skeleton nào cần rework"_ nên các chỉ số được bảo toàn ở mức Xuất sắc.
- Điểm lệch duy nhất được ghi nhận là ở `train_13.jpg`, người #1, khớp `left_ankle`: lệch 19 px (1.1 lần bán kính dung sai) do gấu quần dài và đế giày che khuất mắt cá chân thật.
- Kiểm tra lại trực quan qua công cụ `visualize_pose.py`, toàn bộ khung xương đều ăn khớp tự nhiên với giải phẫu cơ thể người trong 20 ảnh core.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ, bạn nghĩ vì sao mình vẫn sai?

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh. Trước khi xuất nhãn, tôi đã kiểm tra kỹ lưỡng bằng công cụ trực quan hoá `tools/visualize_pose.py` (với quy ước màu xanh bên trái cơ thể, màu cam bên phải cơ thể) để đảm bảo không bị nhầm góc nhìn của ảnh với cơ thể người.

## 3. Kiểm chéo

Bạn cùng nhóm: Đối chiếu với nhãn Gold (làm độc lập)

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp        | Bạn |  Họ | Lệch | Nguyên nhân (guideline hay gán sai?)                                                                                                                                                                               |
| ----------- | --: | --: | ---: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `left_ear`  | 66% |  3% |  62% | **Guideline khác nhau:** Bản COCO gốc (gold) thường để `v=0` hoặc bỏ qua tai bị tóc/góc chụp che, trong khi tôi tuân thủ chặt chẽ guideline của lớp: còn trong khung ảnh thì bắt buộc chấm ước lượng và gán `v=1`. |
| `right_ear` | 45% | 14% |  31% | **Guideline khác nhau:** Tương tự tai trái, do sự khác biệt trong quy định gắn cờ `v=1` (bị che) đối với các khớp trên khuôn mặt ở góc nhìn nghiêng.                                                               |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Đối với tai ở góc chụp nghiêng hoặc bị tóc/mũ che, miễn là xác định được vị trí tương đối trên hộp sọ từ trục mắt sang thái dương thì bắt buộc phải đặt chấm ước lượng và gán cờ `v = 1`, không được để `v = 0` hay xoá khớp.

## 4. Model

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. “Chênh” = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số         | yolo26n-pose gốc | Sau fine-tune |   Chênh |
| -------------- | ---------------: | ------------: | ------: |
| pose_mAP50     |           0.8450 |        0.8450 | +0.0000 |
| pose_mAP50-95  |           0.6853 |        0.6908 | +0.0055 |
| pose_precision |           0.9734 |        0.9792 | +0.0058 |
| pose_recall    |           0.8462 |        0.8462 | +0.0000 |
| box_mAP50-95   |           0.8119 |        0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model
   điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?

Chỉ số `pose_mAP50-95` tăng nhẹ **+0.0055** (từ **0.6853** lên **0.6908**). Mặc dù tập train chỉ có 20 ảnh (rất nhỏ so với quy mô COCO), việc gán nhãn chuẩn xác theo giải phẫu và cẩn thận gắn cờ `v = 1` cho các khớp bị che đã giúp mô hình học thêm được cách ước lượng khớp trong các góc chụp nghiêng và bị che khuất một phần, giúp cải thiện độ chính xác mà không làm hỏng tri thức nền tảng ban đầu.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm _người_ dễ hơn hay tìm
   _khớp_ dễ hơn? Vì sao?

Sau fine-tune, `box_mAP50-95` đạt **0.8041** trong khi `pose_mAP50-95` đạt **0.6908**, chênh lệch **0.1133** (box cao hơn pose). Model tìm *người* dễ hơn tìm *khớp* rất nhiều. Hộp bao (bounding box) chỉ cần xác định vùng bao quát toàn bộ thân thể, trong khi bài toán pose phải định vị chính xác vị trí giải phẫu của từng khớp trong số 17 khớp, đồng thời các khớp này thường xuyên bị che khuất (occlusion), chồng lấn nhiều người và dễ bị nhầm lẫn giữa bên trái và bên phải.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43
   (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):

Ảnh `test_07`: Model mắc lỗi **lệch nhẹ** ở vùng mắt cá chân và cổ tay của người đứng phía xa do độ phân giải vùng cơ thể nhỏ và bị quần áo che phủ, các chấm dự đoán của mô hình bị lệch nhẹ ra ngoài đế giày thay vì nằm đúng tâm khớp mắt cá.

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?

Ảnh `train_01` (người #1 bị che khuất nửa dưới) là bức ảnh có OKS thấp nhất giữa tôi và model. Trong ca này, **tôi đúng**: tôi đã phân tích đúng tư thế người đứng sau vật cản, đánh cờ `v = 1` cho đầu gối và để `v = 0` cho phần bàn chân hoàn toàn không thể xác định được căn cứ giải phẫu; trong khi model dự đoán võ đoán khớp bàn chân đâm xuyên vào thân của vật cản phía trước.

5. Ảnh bạn gán tệ nhất có _cũng_ là ảnh model đoán tệ nhất không? Nếu có, điều đó
   nói gì về bức ảnh đó?

**Có**, ảnh `train_01` và `train_13` vừa là những ảnh có OKS thấp nhất trong bài gán của tôi so với gold, vừa là những bức ảnh mô hình gặp nhiều khó khăn nhất khi dự đoán. Điều này cho thấy đây là những ca khó mang tính khách quan của dữ liệu (nhiều người đứng chen chúc, quần áo thụng che mất mốc xương, góc chụp bị che khuất nặng), gây khó khăn cho cả người gán nhãn lẫn thuật toán học sâu.

## 5. Một rule evidence bạn đã dùng

Ảnh `train_01.jpg`, người thứ #1, khớp `left_knee`. Người này đứng sau vật cản che khuất nửa thân dưới, tuy nhiên phần đùi trên vẫn lộ rõ hướng chuyển động xuôi xuống và vị trí đầu gối chắc chắn vẫn nằm gọn bên trong khung ảnh chứ chưa vượt ra ngoài mép ảnh. Theo quy tắc của lớp, khớp còn trong khung hình nhưng bị che khuất thì bắt buộc phải đặt chấm ước lượng và gắn cờ `v = 1` (Occluded), tuyệt đối không để `v = 0` (Outside). Việc gán `v = 1` giúp mô hình học được mối liên kết xương tự nhiên giữa hông và đầu gối khi gặp vật cản, thay vì hiểu nhầm là người này bị khuyết chi.
