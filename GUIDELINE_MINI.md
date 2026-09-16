# Mini guideline - nhóm: Không có | người gán: Nguyễn Hoài Thanh | ngày: 17/09/2026

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
| Hông của người mặc quần áo dài | Ước lượng vị trí chỏm xương hông dựa vào nếp gấp eo/đáy quần và dáng đứng; gắn cờ `v = 1`. | Khung xương cần bảo toàn tỷ lệ thân người (từ vai xuống hông và từ hông xuống đùi). Dù bị vải che nhưng cấu trúc xương vẫn dự đoán được, giúp model học đúng tỷ lệ. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Dóng đường ngang từ khóe mắt sang thái dương để ước lượng vị trí lỗ tai; gắn cờ `v = 1`. | Khớp vẫn nằm trong khung hình nhưng bị che khuất bề mặt. Để `v=1` giúp model nhận biết vùng đầu có đủ hai tai dù có vật cản. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp ra ngoài khung ảnh (đầu gối, cổ chân) để cờ `v = 0`, tọa độ `(0, 0)` và **không đặt chấm**. Các khớp còn trong ảnh chấm bình thường. | Đúng quy chuẩn COCO: Khớp ra ngoài khung ảnh thì `v=0`. Không suy đoán điểm ra ngoài ảnh để tránh làm hỏng tọa độ chuẩn hóa `[0, 1]`. |
| Cổ tay nằm sau tay lái / sau thân mình | Dóng theo hướng cẳng tay (từ khuỷu tay kéo dài ra) để chấm vào vị trí cổ tay ẩn sau vật cản; gắn cờ `v = 1`. | Giữ liên kết liền mạch cho cánh tay (vai - khuỷu - cổ tay), giúp model học được pose cầm lái hoặc chắp tay sau lưng. |
| Hai người chồng lên nhau | Gán riêng từng skeleton cho từng người. Khớp của người phía sau bị người phía trước che thì chấm ước lượng và để `v = 1`. Tuyệt đối không chấm mượn khớp của người phía trước. | Đảm bảo tính độc lập của từng đối tượng (*instance*), tránh việc model học nhầm khớp của người này sang người kia. |
| Người nhỏ đến mức nào thì không gán nữa | Người có chiều cao nhỏ hơn ~30 pixel, hoặc quá mờ không phân biệt được đầu - thân - chân thì bỏ qua, không gán skeleton. | Người quá nhỏ hoặc nhòe sẽ không thể xác định chính xác 17 khớp, nếu cố gán sẽ tạo ra dữ liệu nhiễu (noise) làm hỏng model khi huấn luyện. |

*(Ghi chú: Đính kèm ảnh screenshot từ CVAT vào thư mục assets/ nếu cần minh họa chi tiết thêm cho từng ca).*

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_02.jpg`, người thứ `1`, khớp `left_ear`

- Mơ hồ ở chỗ nào: Người đứng góc chụp nghiêng, tai trái bị tóc che khuất một phần bề mặt, khó nhìn thấy vành tai rõ ràng.
- Bạn quyết thế nào: Dóng theo trục mắt - thái dương để ước lượng tâm lỗ tai và gán cờ `v = 1` (occluded).
- Vì sao: Tuân thủ quy định của lớp: khớp còn nằm trong khung hình nhưng bị che thì vẫn đặt chấm ước lượng và gắn cờ `v = 1`.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu xoá khớp hoặc để `v = 0`, model sẽ học sai rằng người ở góc nghiêng này bị khuyết tai, làm giảm khả năng nhận diện pose khi bị vật thể che một phần.

### Ca 2 - ảnh `train_13.jpg`, người thứ `1`, khớp `left_ankle`

- Mơ hồ ở chỗ nào: Người mặc quần dài ống thụng và mang giày, mắt cá chân bị che khuất hoàn toàn bởi lớp vải dày và cổ giày.
- Bạn quyết thế nào: Ước lượng vị trí mắt cá chân nằm ngay phía trên đế giày và thẳng hàng với cẳng chân, gán cờ `v = 1`.
- Vì sao: Giữ tính toàn vẹn của chuỗi khớp chân (hông -> đầu gối -> cổ chân), đảm bảo tỷ lệ cơ thể nhất quán khi đứng trên mặt đất.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu chấm ra ngoài mép giày hoặc bỏ qua, model sẽ dự đoán chân bị co ngắn hoặc bàn chân lơ lửng, làm giảm độ chính xác OKS.

### Ca 3 - ảnh `train_01.jpg`, người thứ `1`, khớp `left_knee` / `left_ankle`

- Mơ hồ ở chỗ nào: Phần chân dưới của người bị vật thể che khuất hoàn toàn ở vùng bên dưới thân mình.
- Bạn quyết thế nào: Với đầu gối vẫn suy đoán được hướng gập của đùi thì chấm ước lượng và gán `v = 1`; phần bàn chân hoàn toàn không có căn cứ giải phẫu để xác định thì để `v = 0`.
- Vì sao: Tránh chấm bừa vào khoảng trống khi không có căn cứ giải phẫu đáng tin cậy.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu chấm võ đoán vào vùng không có căn cứ, model sẽ học hiện tượng "ảo giác" (hallucination), tự sinh keypoint ở những vị trí vô lý.

## 4. Sau khi so visibility report với bạn cùng nhóm (đối chiếu gold)

- Khớp lệch `%v=1` nhiều nhất: `left_ear` (bạn `66%` / đối chiếu `3%`)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Do guideline khác nhau. Bản COCO gốc (gold) thường để `v = 0` hoặc không gán cho tai bị tóc/góc chụp che, trong khi quy tắc của lớp chặt chẽ hơn: còn trong khung ảnh và đoán được vị trí giải phẫu thì phải gán `v = 1` và đặt chấm.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Đối với tai ở góc chụp nghiêng hoặc bị tóc che, miễn là xác định được vị trí tương đối trên hộp sọ thì bắt buộc phải chấm và để cờ `v = 1`, không được để `v = 0`.
