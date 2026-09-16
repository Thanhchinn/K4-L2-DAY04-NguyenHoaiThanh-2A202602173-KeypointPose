# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 15.93 khớp có v > 0 mỗi người
- Tổng: v=2 344 | v=1 118 | v=0 31

So sánh với `gold\labels\train` (29 skeleton).
Cột **lệch** là hiệu số phần trăm v=1 - chỗ nào lệch nhiều nhất là chỗ guideline chưa nói rõ.

| # | Khớp | %v=1 (bạn) | %v=1 (đối chiếu) | lệch |
| ---: | --- | ---: | ---: | ---: |
| 3 | left_ear | 66% | 3% | 62 |
| 4 | right_ear | 45% | 14% | 31 |
| 9 | left_wrist | 34% | 7% | 28 |
| 1 | left_eye | 31% | 3% | 28 |
| 2 | right_eye | 24% | 0% | 24 |
| 0 | nose | 24% | 3% | 21 |
| 7 | left_elbow | 21% | 7% | 14 |
| 13 | left_knee | 14% | 21% | 7 |
| 8 | right_elbow | 10% | 3% | 7 |
| 10 | right_wrist | 28% | 21% | 7 |
| 16 | right_ankle | 17% | 24% | 7 |
| 5 | left_shoulder | 10% | 7% | 3 |
| 6 | right_shoulder | 3% | 3% | 0 |
| 11 | left_hip | 28% | 28% | 0 |
| 12 | right_hip | 21% | 21% | 0 |
| 14 | right_knee | 14% | 14% | 0 |
| 15 | left_ankle | 17% | 17% | 0 |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
