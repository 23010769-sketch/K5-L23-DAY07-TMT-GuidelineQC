# Guideline gán nhãn — v0.1-proposal — 157

> Bản đề xuất được Codex soạn theo yêu cầu của người dùng sau khi đã có bản 106 và 127. Đây là tài liệu để thành viên 157 đối chiếu và xác nhận, không phải bằng chứng 157 đã tự khảo sát độc lập, gán nhãn hoặc duyệt ảnh. Các ca có tên ảnh bên dưới tham chiếu mô tả của tài liệu lab; chưa có xác nhận của 157 hay quyết định phê duyệt của nhóm.

- Mã thành viên: `157`; nhóm: `TMT`.
- Phiên bản: `v0.1-proposal-157`; ngày soạn: `2026-11-17`.
- Công cụ: CVAT, Rectangle 2D, xuất COCO 1.0.
- Phạm vi đọc ảnh của Pha 1: đủ 40 ảnh; số ảnh được giao để gán ở Pha 3: **27**, theo [phân công 157](../../qc/assignments/157.txt).
- Căn cứ: [tài liệu lab](../../../K5-L23-Day07-Guideline-Taxonomy-QC.md), [khung guideline](../../../guideline/TEMPLATE_guideline.md), [guideline v0](../../../guideline/guideline_v0_starter.md), [hướng dẫn CVAT](../../../docs/CVAT_SETUP.md).

## 1. Mục tiêu

Tạo bộ bounding box cho bài thực hành phát hiện người và phương tiện thật trên ảnh đường phố. Quy tắc cần đủ cụ thể để hai người nhìn cùng ảnh có thể chọn cùng cá thể, cùng lớp và gần cùng biên box mà không hỏi nhau khi gán độc lập.

Ưu tiên kiểm tra theo thứ tự: vật có thuộc phạm vi không → có bỏ sót / gán thừa không → lớp có đúng không → box có ôm sát phần thấy không → thuộc tính có đúng không. Đặc biệt tránh gán vật giả hoặc phản chiếu thành đối tượng thật và tránh gộp người với phương tiện vào một box.

Đầu ra là vị trí và loại vật trên ảnh 2D, không chứng minh khoảng cách, tốc độ hoặc khả năng sử dụng ngoài bài lab. Một bộ nhãn hợp lệ về JSON vẫn cần người kiểm nội dung trên ảnh.

## 2. Lớp và thuộc tính

### 2.1. Phạm vi lớp đề xuất

Đề xuất dùng bảy lớp đối tượng và một lớp vùng bỏ qua. Mỗi cá thể thuộc đúng một lớp đối tượng; không gán hai lớp cho cùng một vật để tránh phải chọn.

| Lớp | Định nghĩa | Ví dụ / dấu hiệu | Phản ví dụ / cần phân biệt |
|---|---|---|---|
| `car` | Ô tô chở người dạng sedan, hatchback, SUV hoặc crossover, không có thùng hàng riêng tách với cabin. | Xe con có khoang hành khách là phần thân chính. | Pickup là `truck`; thân dạng van là `van`; ô tô đồ chơi không thuộc phạm vi. |
| `truck` | Xe có cabin và kết cấu chở hàng tách biệt, gồm pickup, xe tải và xe chuyên dụng chở xe. | Xe chở ô tô ở ca `img_002.jpg`; pickup lắp nắp thùng là ca `img_037.jpg`. | Van có thân liền khối; tượng pickup ở `img_027.jpg`. |
| `van` | Xe có thân dạng hộp liền khối chứa cabin và khoang hàng / hành khách, không có thùng hàng riêng như pickup. | Panel van, minibus có kết cấu thân van. | Bus chuyên dụng; pickup lắp nắp cao vẫn là truck nếu nhận biết được kết cấu. |
| `bus` | Xe buýt / xe khách chuyên dụng có khoang hành khách kéo dài, phân biệt với van bằng kết cấu nhìn thấy. | Các tình huống bus được lab nêu ở `img_009.jpg`, `img_037.jpg`. | Không đoán bus chỉ từ số ghế hoặc kích thước phối cảnh; thiếu dấu hiệu thì cần review. |
| `tuk_tuk` | Phương tiện cơ giới ba bánh dạng tuk-tuk, xe lam hoặc xe ba gác máy, có khoang chở người / sàn hàng. | Tình huống tuk-tuk ở `img_004.jpg`, `img_035.jpg`. | Xích lô đạp không động cơ; mô tô có sidecar; mô hình xe ba bánh. |
| `motorcycle` | Xe máy / mô tô hai bánh có động cơ; mô tô với sidecar gắn liền vẫn tính một phương tiện. | Xe số, scooter, mô tô; box sidecar gắn liền thuộc box xe. | Người ngồi trên xe là `person` riêng; xe đạp không động cơ ngoài phạm vi. |
| `person` | Người thật nhận diện được trong cảnh, không phụ thuộc tư thế hoặc việc đang ở trên phương tiện. | Người đi bộ, lái xe máy, người nhìn rõ qua kính. | Tượng, ma-nơ-canh, hình in và hình phản chiếu không phải cá thể người thật cần gán. |
| `ignore_region` | Vùng chứa vật thuộc phạm vi nhưng quá nhỏ hoặc cụm cá thể không thể tách đáng tin cậy. | Vật cao dưới 20 px; cụm người / xe dày đặc không phân biệt được từng cá thể. | Không dùng thay cho lớp chưa biết, vật giả hoặc một vật lớn chỉ vì gán khó. |

`bus` và `tuk_tuk` là đề xuất bổ sung so với v0, phải có trong taxonomy và cấu hình CVAT của nhóm trước khi dùng. Lớp trả lời mô hình cần phát hiện loại vật gì; thuộc tính mô tả điều kiện quan sát và trạng thái cần kiểm lại.

Xe đạp, xích lô đạp, phương tiện ngoài danh sách, hàng hóa rời và vật trang trí nằm ngoài phạm vi đề xuất. Người thật trên phương tiện ngoài phạm vi vẫn được gán riêng nếu đáp ứng điều kiện. Ca chưa xác định được trong / ngoài phạm vi chuyển R-12, không tự bọc ignore để kết thúc.

### 2.2. Thuộc tính

Mỗi box đối tượng phải có đủ ba thuộc tính dưới đây. `ignore_region` không cần các thuộc tính trong cấu hình do script tạo.

**`occluded_level`:** ước lượng phần diện tích hình chiếu của vật trong khung ảnh bị vật khác che, không cộng phần mất ngoài mép ảnh. Có thể hiểu `p = diện tích bị che / (diện tích thấy + diện tích bị che)` trong khung ảnh; hình dạng bị che là ước lượng phục vụ thuộc tính, không phải phần được vẽ thêm vào box.

| Mức | Khoảng `p` | Điều kiện |
|---|---|---|
| `0` | `p = 0%` | Không bị vật khác che. |
| `1` | `0% < p <= 30%` | Che ít; đúng 30% vẫn thuộc mức 1. |
| `2` | `30% < p <= 70%` | Che nhiều; đúng 70% vẫn thuộc mức 2. |
| `3` | `70% < p < 100%` | Còn phần nhìn thấy cao ít nhất 20 px, nhận diện chắc chắn cá thể và lớp. |

Không có ngưỡng tự động loại bỏ ở 85%: nếu chưa chắc nhận diện được thì cần review; vật bị che hoàn toàn không tạo box. Tỉ lệ là ước lượng trên ảnh, không phải số đo đã được script xác nhận.

| Thuộc tính | Giá trị | Cách chọn |
|---|---|---|
| `truncated` | `yes` / `no` | `yes` khi mép khung ảnh cắt mất một phần vật; chỉ chạm cạnh ảnh mà vật vẫn trọn vẹn chưa đủ. |
| `can_review` | `true` / `false` | `true` khi có box tạm nhưng chưa chắc về lớp, biên hoặc thuộc tính; kèm ảnh, vùng / ID box và câu hỏi. |

Che khuất và cắt mép có thể xảy ra đồng thời. Ảnh mờ không tự làm tăng mức che khuất. Cờ occluded mặc định của CVAT không thay cho thuộc tính `occluded_level`.

## 3. Quy tắc vẽ box

Mã quy tắc dưới đây dùng cho bản đề xuất; chỉ áp dụng bộ mã chung đã khóa khi bắt đầu gán nhãn độc lập.

### R-01 — Chọn phạm vi phần nhìn thấy

Vẽ một hình chữ nhật sát các cực trái, phải, trên, dưới nhìn thấy của cùng một vật. Nếu vật bị một cột mảnh chia thành hai phần thấy được, vẫn bao các phần của cùng cá thể bằng một box. Không kéo box tới đầu / chân / đuôi suy đoán phía sau vật che. Bao bộ phận cố định như bánh, gương; không bao bóng đổ, ánh đèn trên mặt đường hoặc khói. Box phải nằm trong ảnh và có chiều rộng / cao dương.

### R-02 — Xử lý cắt mép

Box dừng đúng mép ảnh; gán `truncated=yes` nếu vật bị mất một phần ngoài khung. Gán cá thể riêng khi lớp rõ và chiều cao phần thấy ít nhất 20 px; phần thấy thấp hơn ngưỡng áp dụng R-06. Không nhầm bị cắt mép với bị vật khác che.

### R-03 — Tách người và phương tiện

Mỗi người thật đủ điều kiện có một box `person`; mỗi phương tiện đủ điều kiện có box riêng. Một người đi một xe máy thường tương ứng hai box; hai người trên một xe máy tương ứng ba box nếu phân biệt được các cá thể. Box xe không mở rộng để bao đầu người, box người không bao toàn xe. Người nhìn rõ qua kính vẫn áp dụng cùng tiêu chí, nhưng không suy đoán người lái chỉ vì thấy một chiếc xe. Quần áo / mũ / vật đeo sát người nằm trong box người; ô xòe, hàng rời và vật dài cầm tay không làm mở rộng biên người. Mô tô gắn sidecar là một box xe bao cả kết cấu gắn liền, người ngồi trong sidecar là cá thể riêng.

### R-04 — Phương tiện được chở

Vẽ box cho xe chở theo kết cấu của nó và từng phương tiện thật được chở nếu nhận diện được, phần thấy cao ít nhất 20 px. Không gộp tất cả thành một đối tượng truck. Việc các box chồng nhau hoặc box nhỏ nằm trong box lớn có thể xảy ra do hình học; không kéo biên xe chở theo phần xe được chở nhô ra ngoài kết cấu xe chở. Vật chỉ giống xe nhưng là mô hình / thân vỏ trang trí cần kiểm theo R-05 và R-12.

### R-05 — Loại vật giả và phản chiếu

Không vẽ box cho tượng, ma-nơ-canh, đồ chơi, hình người / xe trên biển quảng cáo hoặc vật chỉ xuất hiện trong phản chiếu. Không tạo ignore cho chính các vật bị loại chắc chắn này. Phân biệt nhìn xuyên kính tới vật thật với hình phản chiếu trên kính; nếu không phân biệt được, ghi ca cần review.

### R-06 — Vật nhỏ và cụm không tách được

Đo chiều cao box ôm phần thấy bằng pixel ảnh gốc. Dưới 20 px: dùng vùng ignore vừa đủ cho vật / cụm vật thuộc phạm vi. Từ 20 px trở lên và tách được cá thể: gán riêng. Với đám đông không thể tách ranh giới từng người / xe đáng tin cậy, chỉ bao phần không tách được bằng ignore, giữ cá thể rõ ở tiền cảnh thành box riêng. Không dùng số lượng vật đông hoặc ảnh nhòe như lý do duy nhất để gom ignore.

### R-07 — Xe chủ

Không gán phần xe gắn camera khi xác định chắc là xe chủ. Xe khác ở sát mép ảnh vẫn gán theo R-02. Vị trí ở mép dưới không tự chứng minh là xe chủ; không chắc thì dùng R-12.

### R-08 — Giữ đúng taxonomy đã khóa

Đề xuất thêm `bus`, `tuk_tuk` trước lượt gán vì đó là loại vật cần mô hình phân biệt. Sau khi nhóm khóa v1.0, không tự thêm lớp hoặc ép vật chưa có định nghĩa sang lớp gần nhất. Đủ bằng chứng ngoài phạm vi thì bỏ qua; thiếu quy tắc / chưa xác định được phạm vi thì ghi vấn đề.

### R-09 — Pickup, van và bus

Pickup có cabin và thùng riêng vẫn là truck khi lắp nắp thùng. Van có thân dạng hộp liền khối; bus có kết cấu xe buýt / xe khách chuyên dụng. Không đặt ranh giới theo số ghế tưởng tượng hoặc xe trông lớn do đứng gần camera. Thiếu bằng chứng cấu trúc thì đánh dấu review và nêu dấu hiệu còn thiếu.

### R-10 — Hàng hóa nhô ra và phụ kiện

Biên xe theo thân, mái, khung và thùng cố định. Không kéo rộng box theo bao tải hoặc vật liệu chở nhô ra; hàng nằm trong hình chữ nhật của xe có thể nằm trong box nhưng không được dùng làm điểm xác định biên. Bộ phận không rõ là kết cấu xe hay hàng rời cần review.

### R-11 — Nhòe chuyển động

Nếu còn nhận biết được từng vật và thân vật, vẽ theo phần thân đó, không theo toàn bộ vệt nhòe. Không gộp hai vật vì vệt mờ nối nhau. Nếu biên hoặc số vật không rõ, ghi cụ thể câu hỏi theo R-12 thay vì tạo kết quả chắc chắn giả.

### R-12 — Ghi vấn đề và kiểm lại

Có box tạm thì bật `can_review=true` và ghi ảnh, vùng / ID box, điều chưa rõ. Chưa vẽ được box thì ghi `image,annotator,region,note` với mã `157` để nhóm tập hợp sau khi khóa nhãn độc lập. Không hỏi người cùng gán ảnh về ca đó trong Pha 3. Khi calibration, ghi người thực sự chốt, quy tắc áp dụng, dấu vết sửa và người kiểm lại. Vai trò review cuối của 157 trong TEAM.md không đồng nghĩa mọi ảnh đã được 157 duyệt.

## 4. Ca khó tham chiếu

Đây là đề xuất áp dụng quy tắc lên các tình huống được tài liệu lab mô tả. Không có tọa độ box hay xác nhận 157 đã xem / duyệt những ảnh này.

| Ảnh | Tình huống / câu hỏi từ lab | Rule ID | Xử lý đề xuất | Lý do và điều cần kiểm |
|---|---|---|---|---|
| `img_002.jpg` | Xe chở các ô tô khác. | `R-04` | Xe chở và các xe thật đủ điều kiện có box riêng. | Kiểm cả xe được chở, không chỉ box truck lớn. |
| `img_004.jpg`, `img_035.jpg` | Tuk-tuk chưa có trong v0. | `R-08` | Dùng lớp tuk_tuk nếu nhóm chấp nhận và đã đồng bộ taxonomy. | Không dùng ignore thay cho thiếu định nghĩa lớp. |
| `img_009.jpg`, `img_037.jpg` | Bus và van dễ bị dùng chung lớp. | `R-09` | Phân biệt theo kết cấu; thiếu dấu hiệu thì review. | Không quyết định theo số ghế suy đoán. |
| `img_037.jpg` | Pickup gắn nắp thùng cao. | `R-09` | Truck nếu xác nhận được kết cấu pickup. | Cần chỉ ra dấu hiệu cabin / thùng riêng; chưa rõ không tự chốt. |
| `img_017.jpg`, `img_027.jpg` | Tượng hình người / xe. | `R-05` | Loại tượng; vẫn kiểm vật thật khác trong ảnh. | Không coi toàn ảnh là trống chỉ vì có tượng. |
| `img_033.jpg`, `img_038.jpg` | Ma-nơ-canh và đồ chơi. | `R-05` | Không gán vật giả và không bọc ignore cho chúng. | Đây là phản ví dụ của lớp người / xe thật. |
| `img_018.jpg`, `img_020.jpg` | Hình in và phản chiếu. | `R-05` | Không đếm hình biểu diễn thành cá thể. | Vật thật thấy xuyên kính cần được phân biệt riêng. |
| `img_006.jpg`, `img_029.jpg`, `img_039.jpg` | Xe đạp / xích lô không động cơ. | `R-03`, `R-08` | Loại phương tiện ngoài phạm vi, vẫn gán người thật đủ điều kiện. | Phạm vi xe không loại người trên xe. |
| `img_035.jpg` | Hàng chở nhô ra khỏi xe. | `R-10` | Biên phương tiện không mở rộng theo hàng rời. | Kiểm hai người có cùng phạm vi thân xe. |
| `img_024.jpg` | Xe được chở hay thân vỏ trang trí? | `R-04`, `R-05`, `R-12` | Nếu xác nhận chỉ là vật trưng bày thì loại; chưa rõ thì review. | Không khẳng định xe thật từ hình dạng giống xe. |
| `img_014.jpg`, `img_015.jpg` | Nhòe làm biên và số cá thể khó đọc. | `R-11` | Giữ cá thể tách biệt khi nhận diện được, không ôm toàn vệt nhòe. | Trường hợp không chắc phải để lại câu hỏi. |

Trước khi xác nhận bản cá nhân, 157 cần tự khảo sát đủ 40 ảnh và thử R-03 trên hai ảnh có người đi xe máy; tên ảnh và kết quả thực tế được ghi vào ghi chú cá nhân. Các ca khác phát hiện khi xem ảnh cần bổ sung theo cùng cấu trúc bảng.

## 5. QC và phiên bản

### 5.1. Ngưỡng cố định

Giữ nguyên tám khóa trong `rules` của taxonomy. Đây là ngưỡng yêu cầu, không phải kết quả đo của thành viên 157.

| Khóa | Giá trị | Ý nghĩa |
|---|---|---|
| `min_box_height_px` | `20` | Ngưỡng chiều cao phần thấy để gán box đối tượng. |
| `large_px` | `50` | Ranh giới box lớn / nhỏ. |
| `iou_thr_large` | `0.85` | Ngưỡng IoU cho box lớn. |
| `iou_thr_small` | `0.70` | Ngưỡng IoU cho box nhỏ. |
| `match_iou` | `0.50` | Ngưỡng ghép cặp box trước khi tính IAA. |
| `iaa_iou` | `0.85` | Ngưỡng IoU trung bình trên cặp ghép. |
| `iaa_class` | `0.95` | Ngưỡng đồng thuận lớp trên cặp ghép. |
| `error_rate_img_max` | `0.02` | Ngưỡng tỉ lệ ảnh có lỗi khi xét Release. |

### 5.2. Trình tự kiểm đề xuất

1. Đối chiếu các bản cá nhân, chốt guideline nhóm và taxonomy trước khi gán; không coi bản AI soạn là bằng chứng đã họp hay đã gán độc lập.
2. Gán đúng 27 ảnh được giao cho 157; xuất COCO 1.0 không kèm ảnh thành `submission/labels/157.json`, giữ cả ảnh không có đối tượng. Chạy `python scripts/validate_labels.py submission/labels/157.json` để kiểm cấu trúc.
3. Chỉ đo IAA khi cả nhóm đủ 80 lượt theo phân công, mỗi ảnh hai người. IoU trung bình và đồng thuận lớp chỉ tính trên cặp box ghép được; không có cặp thì N/A. Kiểm riêng bỏ sót / thừa và khả năng hai người cùng sai.
4. Calibration phải có cơ sở quy tắc và dấu vết sửa. Thay đổi cách chọn vật, lớp, biên hoặc thuộc tính là semantic và cần rà cả 40 ảnh.
5. QC duyệt toàn lô, ghi lỗi theo ảnh và theo annotation có mẫu số; bỏ sót ghi riêng. Một ảnh lỗi trong 40 ảnh là 2,5%, vượt 2%. Hold nếu thiếu bằng chứng hoặc còn vấn đề; Rework nếu vượt ngưỡng / lỗi lặp theo lab; Release khi bằng chứng đầy đủ và reviewer thực sự xác nhận.

Theo hướng kiểm của bản này, reviewer cần hỏi: có vật thật chưa được xét không, vùng ignore có che cá thể rõ không, box có chứa phần không thuộc vật không, trường hợp qua kính có bị nhầm phản chiếu không, và mỗi quyết định có truy được mã quy tắc không.

### 5.3. Lịch sử và trạng thái

| Phiên bản | Nội dung | Căn cứ / trạng thái |
|---|---|---|
| `v0` | Guideline khởi đầu, định nghĩa lớp / thuộc tính chưa đủ rõ. | Tài liệu gốc của lab. |
| `v0.1-proposal-157` | Soạn đủ năm phần theo góc nhìn kiểm tra tính nhất quán, đề xuất tám nhãn và 12 quy tắc. | Yêu cầu bổ sung bản 157 ngày 2026-11-17; AI hỗ trợ soạn, chưa có xác nhận quan sát độc lập hoặc phê duyệt của 157. |

Bản này cần được 157 tự đối chiếu và sửa theo ảnh trước khi xác nhận. Khi tổng hợp nhóm, lưu riêng guideline v1.0, ghi lựa chọn và lý do đối với các điểm khác giữa bản 106 / 127 / 157; không sửa bản cá nhân đã nộp để tạo đồng thuận hồi tố.
