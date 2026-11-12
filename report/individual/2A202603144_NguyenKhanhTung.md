# Báo cáo thực hành Day 3 — điền trực tiếp trong fork của bạn

**Cách dùng:** Giữ nguyên bốn mục và bảng để coach đọc nhanh. Viết ngắn, cụ thể theo ảnh/vùng; không cần thuật ngữ chuyên sâu.

- Mã học viên theo lớp: 2A202603144
- Ngày / CVAT local: 12/11/2026 / CVAT local v2.82.0 (`http://localhost:8080`, Docker)
- Công cụ đã dùng: CVAT local; công cụ Polygon (tự vẽ tay object đầu tiên ở `medium_instance`), Brush, Eraser; tiền gán nhãn tạo ngoài CVAT bằng mô hình thị giác mã nguồn mở công khai nạp qua `cvat-sdk` (EoMT DINOv2 Cityscapes cho semantic, RF-DETR Large + SAM 2.1 cho instance, EoMT DINOv3 Panoptic cho panoptic); không sử dụng ground truth để tạo gợi ý; kiểm tra, chỉnh sửa và xuất dữ liệu trực tiếp từ CVAT local.

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh [LMS](https://aithucchien.vinuni.edu.vn) đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

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

Toàn bộ 9/9 file ZIP là dữ liệu export chính thức từ CVAT local, được kiểm tra tính hợp lệ bằng công cụ `scripts/inspect_submissions.py` và đạt kết quả chuẩn hóa `[OK]`.

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh `000000181542.jpg`; người phụ nữ mặc áo dài sáng màu đang đi bộ qua đường ở vị trí trung tâm ảnh, phía trước chiếc xe máy chở người đàn ông bên trái. Tôi trực tiếp dùng công cụ Polygon vẽ đối tượng này khi task `medium_instance` chưa nạp gợi ý tự động.
- Class và quy tắc tôi dùng để chọn biên: Class `person`. Quy tắc chọn biên: Bao sát đường bao cơ thể thực tế nhìn thấy (visible contour). Đường polygon đi sát tóc, cổ, tay áo, tà áo dài và dừng ở ranh giới mép bàn chân tiếp xúc mặt đường; ở phía mạn sườn trái, tà áo dài giáp với đầu xe máy đã được tỉa dứt khoát theo nếp vải để tách biệt rõ người đi bộ và phương tiện cơ giới, không gom phần yếm xe máy vào người.
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Sau khi hoàn thành vẽ tay đối tượng đầu tiên, các gợi ý bổ sung cho các đối tượng khác trong ảnh được nạp vào task. Gợi ý tự động cho người phụ nữ này trùng vị trí đã được lọc bỏ để ưu tiên giữ nguyên polygon do tôi tự vẽ làm annotation chính thức (xác nhận trong export là object ID 7 với 24 đỉnh).
- Nếu không dùng gợi ý: ghi “không dùng”; vẫn giải thích một quyết định gán nhãn của mình.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: Task `cp1_holes` (ảnh `000000144300.jpg`), đối tượng xe máy trong ảnh giao thông.
- Lỗi thuộc loại: `biên / phủ vùng / khoét lỗ tùy tiện` (Holes artifact).
- Bằng chứng tôi nhìn thấy: Khi phân đoạn instance xe hai bánh, nếu áp dụng ngưỡng phân đoạn thông thường, các khe hở nan hoa căm xe hoặc khoảng trống nhỏ dưới chắn bùn rất dễ bị khoét rỗng li ti, làm mask bị đứt đoạn dạng tổ ong, vi phạm tính nguyên khối của vật thể.
- Quy tắc và hành động sửa: Theo quy ước gán nhãn phương tiện (vehicle annotation rule), xe cơ giới được xem là một khối đặc liền mạch (solid instance), không khoét các khe hở nhỏ dưới vài pixel. Xử lý lấp kín các khoảng hở nan hoa để tạo mask nguyên khối đồng nhất.
- Sau sửa đã Save và export lại chưa?: Đã lưu trên CVAT và xuất ra `cp1_holes.zip` trong `submissions/`, kiểm tra bằng `python scripts/inspect_submissions.py` đạt `[OK]`.

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa (ví dụ task, metric trước/sau nếu có): Sau khi xử lý lỗi khoét lỗ tùy tiện, mask xe đạt độ liền khối và không còn cảnh báo rỗng vùng. Đã chạy thử nghiệm scorecard nội bộ trên 3 tier chính sau khi nhận gói tham khảo từ coach để kiểm tra tính hợp lệ kỹ thuật, kết quả đạt 55.1 / 82 điểm tham khảo và không xuất hiện cờ cảnh báo bất thường nào (Flags: []). Điểm số chính thức của bài nộp sẽ do người phụ trách đánh giá toàn diện theo rubric 100 điểm của lớp. Không đưa file ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `cp4_curb` (ảnh `7d83710e-4697c3b2.jpg`, ranh giới lòng đường và vỉa hè) | Cách 1: Gán viên đá bó vỉa (curb) vào lớp `sidewalk` (vỉa hè).<br>Cách 2: Gán bó vỉa vào lớp `road` (mặt đường xe chạy) vì màu sắc bê tông ngả màu tương tự nhựa đường. | Quy tắc phân định theo công năng (Functional curb rule): Bó vỉa là gờ nâng cao bảo vệ người đi bộ, thuộc kết cấu biên của vỉa hè, không thuộc bề mặt lưu thông cơ giới. | Quyết định: Cắt mask theo mép chân ngoài cùng của bó vỉa (nơi tiếp xúc với mặt đường phẳng) và gán toàn bộ bề mặt gờ bó vỉa vào lớp `sidewalk`. |
| `cp5_occlusion` (ảnh `000000336232.jpg`, người đi bộ bị xe che khuất một phần thân) | Cách 1: Tách người bị che thành 2 instance/mask riêng biệt (thân trên và phần chân dưới gầm).<br>Cách 2: Gán thành 1 instance `person` duy nhất dạng multi-polygon. | Quy tắc che khuất (Occlusion rule): Một người dù bị che khuất tầm nhìn ở giữa vẫn là một thực thể sinh học duy nhất trong thế giới thực, không được tạo 2 ID khác nhau; đồng thời không vẽ phán đoán xuyên qua xe che. | Quyết định: Gom các vùng nhìn thấy thành cùng 1 ID instance cho người đó, chừa khoảng trống tại vùng bị thân xe che lấp hoàn toàn. |
| `cp3_thin` (ảnh `839f7736-abe28069.jpg`, cột biển báo giao thông hoặc cột đèn mảnh) | Cách 1: Bỏ qua cột vì bề ngang chỉ 1-2 pixel, vẽ có thể bị lem sang nền trời/tòa nhà.<br>Cách 2: Zoom lớn (400%–800%) để vẽ dải mask thanh mảnh nối từ chân đất lên đỉnh biển báo. | Quy tắc nét mảnh (Thin structures rule): Mọi vật thể dạng cột (`pole`) có ý nghĩa định vị hạ tầng giao thông đều cần được gán nhãn đầy đủ, tránh bỏ sót làm giảm recall mô hình. | Quyết định: Phóng to tối đa, dùng Polyline/Polygon bề rộng tối thiểu 2px chạy dọc thân cột, đảm bảo phân định rõ ranh giới với nền trời phía sau. |
