# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Lê Ngọc Nam<br>
**MSSV:** 2A202602060<br>
**Hình thức:** Cá nhân<br>
**Mã cặp:** `SOLO`

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33`.
- Bốn mã ảnh: `drive_008`, `drive_022`, `drive_033`, `drive_038`.
- Số vật thể thực tế trong bài sau sửa: **104 hộp** (trước sửa: 105 hộp). Mốc 40–60 chỉ là mục tiêu khối lượng của buổi học, không phải ngưỡng đạt.
- Mã SHA-256 của gói YOLO sau khi xuất lại từ CVAT: `b79c614dcfadb53d22454d9e281d20270cce06abf9f931cf5809d7fa0993366c`.
- Mã SHA-256 của gói CVAT gốc sau khi xuất lại: `d4c0e3730f81c61e5adbbbbe3d9997d57246421e508fab4b3f22ec0db01cf14f`.
- Hai gói sau sửa cùng có 104 hộp; kiểm tra chéo cho kết quả `same_annotation_state=true`, 104 hộp ghép được và IoU chéo nhỏ nhất `0.999900338` (mức kiểm `0.995`).
- Nguồn đối chiếu: bộ tham chiếu giảng dạy do Lab Coach cung cấp sau khi bài riêng đã được xuất và khóa.
- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b`.
- Mã lần phát: `day2-reference-4img-v1`; thời điểm nhận ghi nhận theo tệp tải về: **16:31 ngày 14/09/2026 (UTC+7)**.

Bài của tôi độc lập vì hai gói ban đầu đã được xuất trước khi nhận bộ tham chiếu: YOLO có SHA-256 `35fe37d1d9901c41f3af836b66ec807a979796e222bea1e59904fe77561a1310`, CVAT-native có SHA-256 `82d592f205ea87d3a2f913e69f67bec43cb6d3fffd19a95f5656bb48b0f8d737`; cả hai khác mã của gói đối chiếu. Tôi chỉ mở nguồn đối chiếu sau bước khóa bài riêng, rồi dùng sai khác quan sát được để rà lại trong CVAT.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| `drive_022`, xe lớn ở tiền cảnh, hộp xấp xỉ `(103.76, 351.65)–(387.78, 574.21)` px | `bus` | Thân xe chở khách dài, cao và có nhiều cửa sổ hành khách liên tiếp | Gán `bus` khi thấy thân xe khách dài cùng nhiều cửa sổ/hàng ghế; không đổi lớp chỉ vì nguồn đối chiếu khác ý kiến |

Ví dụ về lớp và thuộc tính: vật thể trên có lớp `bus`, cho biết **đó là loại xe gì**; còn `visibility=clear`, `boundary=inside`, `review_state=confident` mô tả **điều kiện quan sát và trạng thái rà soát**. Lớp và thuộc tính vì vậy không thay thế cho nhau; bản YOLO chỉ giữ lớp và hình học, không giữ ba thuộc tính này.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| `drive_033`, dòng YOLO 23: `0 0.484781 0.056328 0.009063 0.013125`; hộp `car` tại `(307.36, 31.85)–(313.16, 40.25)` px, chỉ khoảng `5.80 × 8.40` px; `visibility=unclear` | Phạm vi | Khi phóng ảnh 100%, vùng này chỉ là một đốm xe rất xa, không thấy dấu hiệu sedan/hatchback/SUV/taxi để kết luận `car`. Hộp đỏ đứng riêng trên ảnh phủ và không có dòng ghép tương ứng trong bảng đối chiếu. | Xóa đúng hộp này trong task CVAT, lưu rồi xuất lại cả YOLO và `CVAT for images 1.1`; không sửa TXT/XML. `drive_033` giảm 30 → 29 hộp, lớp `car` giảm 27 → 26 và toàn bài giảm 105 → 104. Quy tắc: vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ thì không đoán và không gán. |

- Số hộp `needs_review` trước và sau lần sửa phạm vi: **3 → 3**. Hộp bị xóa vốn có `review_state=confident`, nên thao tác này không làm thay đổi ba hộp đang chờ xem lại.
- Quyết định chưa đủ bằng chứng: `drive_008`, hộp tại `(623.01, 273.90)–(639.77, 342.80)` px chỉ còn một dải mỏng ở mép phải, hiện là `car`, `visibility=unclear`, `boundary=truncated`, `review_state=needs_review`. Tôi sẽ gửi ảnh cắt ở mức phóng 100% cùng tọa độ cho Lab Coach và hỏi phần nhìn thấy đã đủ dấu hiệu phân lớp hay chưa; trong lúc chờ, tôi giữ `needs_review` và không tự đổi lớp.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `0 0.262375 0.565906 0.133000 0.078219` trong `drive_022`.
- Tên lớp và tọa độ điểm ảnh `xyxy`: lớp `0 = car`; với ảnh 640 × 640 px, hộp là xấp xỉ `(125.36, 337.15)–(210.48, 387.21)` px.
- Dòng có đúng năm trường và tọa độ hợp lệ chỉ chứng minh cú pháp có thể đọc được. Người gán vẫn có thể chọn nhầm lớp, gán một vật thể ngoài phạm vi hoặc vẽ hộp quá rộng/quá hẹp.

## 5. Huấn luyện và dự đoán thử

- Cấu hình: Ultralytics `8.4.145`, `yolo11n.pt`, 8 epoch, seed 42, device `0`, thời gian 42.07 giây.
- Lần huấn luyện này dùng bản YOLO đã khóa trước khi đối chiếu, SHA-256 `35fe37d1d9901c41f3af836b66ec807a979796e222bea1e59904fe77561a1310`; tôi không tuyên bố đã huấn luyện lại bằng bản 104 hộp sau sửa.
- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`.
- Mã ảnh thẩm định: `drive_008`.
- Kết quả quan sát trong `detect_result.jpg`: ảnh `drive_008` không có hộp, nhãn lớp hoặc độ tin cậy nào được vẽ; vì vậy không có dự đoán vật thể cụ thể để mô tả một cách trung thực.
- Hiện tượng không có hộp gợi ý cần kiểm lại ngưỡng dự đoán, log số detection và chất lượng/độ nhất quán của nhãn, nhất là khi tập huấn luyện chỉ có ba ảnh và nhiều vật thể rất nhỏ.
- Minh chứng có thể bác bỏ nhận định trên: log dự đoán thô cho thấy mô hình thực sự trả về hộp nhưng bước lưu/plot ảnh bị lỗi, hoặc chạy lại cùng trọng số và cùng ngưỡng trên ảnh gốc tạo được hộp ổn định.
- Bốn ảnh cùng một miền cảnh quá ít, trong đó chỉ một ảnh dùng thẩm định; không có tập kiểm thử độc lập hay độ đa dạng điều kiện. Vì vậy kết quả này chỉ hỗ trợ tìm câu hỏi về dữ liệu, không phải phép đánh giá mô hình dùng trong thực tế.

## 6. Đối chiếu nhãn

Các số dưới đây đã được tính lại trực tiếp với gói YOLO 104 hộp sau sửa và cùng bộ tham chiếu; việc bỏ hộp không ghép làm `unmatched_mine` giảm một, còn 47 cặp ghép và các thống kê trên những cặp đó không đổi.

- Số hộp ghép được: **47**.
- IoU trung bình và trung vị: **0.817749** và **0.831612**.
- Mức đồng thuận lớp: **0.702128** (xấp xỉ 70.21%).
- Số hộp phía tôi không ghép được sau sửa: **57** (trước sửa: 58).
- Số hộp phía đối chiếu không ghép được: **3**.
- Một điểm khác biệt cụ thể: hộp `car` rất nhỏ tại `drive_033`, tọa độ `(307.36, 31.85)–(313.16, 40.25)` px, chỉ có ở bài của tôi và không đủ dấu hiệu nhìn thấy để phân lớp.
- Quy tắc/hành động phát sinh: áp dụng quy tắc không đoán với vật thể quá nhỏ hoặc mờ; xóa hộp trong CVAT, xuất lại cả hai định dạng và kiểm tra chúng vẫn cùng trạng thái.
- Mức đồng thuận cao không chứng minh mọi nhãn đều đúng vì hai nguồn có thể cùng bỏ sót hoặc cùng áp dụng một quy tắc sai. Việc ghép cũng dựa trên hình học chứ không dùng lớp, và bộ tham chiếu ở đây là công cụ phản hồi giảng dạy chứ không phải kết luận chất lượng sản xuất.

## 7. Kiểm tra kho GitHub cá nhân

- [ ] Có phiếu quy tắc với ba tình huống mơ hồ — cần hoàn thiện `GUIDELINE_MINI_SHEET.md` trước khi tải lên.
- [ ] Cần chạy lại các ô kiểm bằng hai gói xuất sau sửa để `my_export_audit.json` và `my_native_export_audit.json` trong `day2_lab_outputs/` phản ánh 104 hộp.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [ ] Cần tạo lại tóm tắt, bảng và ảnh phủ để bộ tệp đầu ra không còn phản ánh trạng thái 105 hộp trước sửa.
- [x] Bộ tệp dự kiến tải lên không chứa gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Bộ tệp dự kiến tải lên không chứa dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất là chuỗi bằng chứng có thể tái lập cho hộp ngoài phạm vi ở `drive_033`: dòng YOLO, tọa độ CVAT, kích thước điểm ảnh, trạng thái `visibility=unclear`, ảnh phủ không có hộp ghép, quy tắc áp dụng và kết quả kiểm sau sửa 105 → 104 hộp. Câu hỏi còn lại cho Lab Coach: nên mô tả mức bằng chứng nhìn thấy tối thiểu như thế nào để mọi người loại nhất quán các xe rất xa mà không biến quy tắc thành một ngưỡng kích thước điểm ảnh tùy tiện?
