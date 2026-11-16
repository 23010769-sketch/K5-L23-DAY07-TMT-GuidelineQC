# Guideline gán nhãn — v0.2-proposal — 127

> Bản đề xuất cá nhân được soạn với hỗ trợ của Codex theo yêu cầu của thành viên 127. Các quy tắc dưới đây đã có nội dung để đối chiếu và thảo luận; chưa phải quyết định thống nhất của nhóm. Ví dụ ảnh lấy từ mô tả trong tài liệu lab, chưa xác nhận 127 đã tự khảo sát đủ 40 ảnh hoặc thử quy tắc trên ảnh.

- Mã thành viên: `127` — nhóm `TMT`.
- Phiên bản: `v0.2-proposal-127`.
- Ngày cập nhật: `2026-11-17`.
- Loại nhãn: bounding box 2D, CVAT, xuất COCO 1.0.
- Tài liệu căn cứ: [hướng dẫn lab](../../../K5-L23-Day07-Guideline-Taxonomy-QC.md), [guideline v0](../../../guideline/guideline_v0_starter.md), [khung guideline](../../../guideline/TEMPLATE_guideline.md), [ngưỡng QC](../../../docs/QC_CHEATSHEET.md).
- Phạm vi khảo sát Pha 1: cả 40 ảnh. Phân công gán độc lập Pha 3 của 127: 27 ảnh theo [danh sách phân công](../../qc/assignments/127.txt).

## 1. Mục tiêu

Bộ nhãn phục vụ bài thực hành huấn luyện và đánh giá mô hình phát hiện người và phương tiện giao thông đường bộ trên ảnh 2D. Mỗi đối tượng trong phạm vi có một lớp và một box ôm sát phần nhìn thấy trong ảnh.

Ba loại sai sót cần hạn chế:

- **Bỏ sót:** thiếu người hoặc phương tiện thật làm giảm khả năng phát hiện và làm sai số đếm đối tượng.
- **Gán thừa / sai lớp:** coi tượng, hình in hoặc phản chiếu là vật thật; hoặc dùng tên lớp gần giống thay cho định nghĩa đã chốt.
- **Sai biên box / thuộc tính:** ôm cả hàng hóa rời, bóng đổ, người ngồi trên xe vào box phương tiện hoặc nhầm che khuất với cắt mép ảnh.

Giới hạn: box mô tả phạm vi 2D; không suy ra khoảng cách, vận tốc, tình trạng động cơ hoặc bộ phận không nhìn thấy. Bộ 40 ảnh dùng cho thực hành quy trình, chưa đủ bằng chứng cho sử dụng thực tế.

## 2. Lớp và thuộc tính

### 2.1. Lớp đối tượng

Đề xuất giữ năm lớp đối tượng của v0, bổ sung `bus`, `tuk_tuk` và giữ `ignore_region` làm vùng bỏ qua. Mỗi vật chỉ thuộc một lớp đối tượng; `ignore_region` không phải lớp vật thể.

| Lớp | Định nghĩa và ranh giới áp dụng | Ví dụ / dấu hiệu nhận biết | Phản ví dụ / dễ nhầm |
|---|---|---|---|
| `car` | Ô tô chở người dạng sedan, hatchback, SUV hoặc crossover; không có thùng chở hàng tách biệt với cabin. | Ô tô con hoặc SUV có khoang hành khách là phần thân chính. | Pickup là `truck`; xe có thân dạng van là `van`; mô hình ô tô không gán. |
| `truck` | Phương tiện chở hàng có cabin và thùng / kết cấu chở hàng tách biệt; bao gồm pickup, xe tải và xe chuyên dụng chở xe. | Xe chở ô tô được nêu ở `img_002.jpg`; pickup có nắp thùng là ca cần làm rõ ở `img_037.jpg`. | Van liền khối; SUV; tượng pickup ở `img_027.jpg`. |
| `van` | Xe chở hàng hoặc chở người có thân dạng hộp, cabin và khoang sau nằm trong một thân xe liền khối, không có thùng hàng riêng như pickup / truck. | Xe giao hàng dạng panel van hoặc minibus có thân van. | Pickup lắp nắp thùng vẫn là `truck`; xe buýt chuyên dụng là `bus`; không đoán số ghế từ ảnh. |
| `motorcycle` | Xe máy / mô tô hai bánh có động cơ, gồm scooter; loại có sidecar vẫn xem là một cụm phương tiện gắn liền. | Xe số, xe tay ga, mô tô; sidecar gắn cố định thuộc cùng box xe. | Xe đạp không động cơ; người ngồi trên xe phải có box `person` riêng; xe ba bánh thuộc định nghĩa `tuk_tuk`. |
| `person` | Người thật nhìn thấy và nhận diện được trong cảnh, bất kể đứng, đi bộ, ngồi hoặc điều khiển phương tiện. | Người đi bộ, người lái / ngồi sau xe máy; người nhìn rõ qua kính cũng áp dụng cùng tiêu chí. | Tượng, ma-nơ-canh, người trong tranh / ảnh in, phản chiếu. |
| `bus` — đề xuất thêm | Phương tiện có kết cấu xe buýt / xe khách chuyên dụng, khoang hành khách kéo dài; phân biệt bằng hình thái thân xe với van, không dùng số chỗ suy đoán. | Các ca xe buýt mà lab nêu ở `img_009.jpg`, `img_037.jpg`. | Minibus có kết cấu thân van; xe tải thùng kín. Nếu hình thái không đủ rõ thì cần review. |
| `tuk_tuk` — đề xuất thêm | Phương tiện cơ giới ba bánh dạng tuk-tuk / xe lam / xe ba gác máy, có khoang chở người hoặc sàn chở hàng. | Ca tuk-tuk ở `img_004.jpg`, `img_035.jpg` theo mô tả lab. | Xích lô đạp không động cơ; mô tô có sidecar; mô hình trang trí không phải phương tiện thật. |
| `ignore_region` | Vùng chứa vật quá nhỏ hoặc cụm đối tượng không thể tách từng cá thể đáng tin cậy theo R-06. | Cụm người / xe xa, dày đặc; vật thuộc phạm vi có box nhìn thấy cao dưới 20 px. | Vật giả hoặc vật chắc chắn ngoài phạm vi không tự động thành vùng ignore; vật rõ, đủ lớn phải gán riêng. |

**Thứ tự phân biệt:** kiểm tra vật thật hay biểu diễn của vật → kiểm tra phạm vi → xác định loại hình phương tiện → kiểm tra kích thước và khả năng tách cá thể → vẽ box và chọn thuộc tính. Không dùng `ignore_region` như cách thay thế cho việc chưa chọn được lớp.

**Ngoài phạm vi đề xuất:** xe đạp / xích lô không động cơ, phương tiện không thuộc các lớp đã định nghĩa, vật trang trí và hàng hóa rời. Bỏ qua phương tiện ngoài phạm vi nhưng vẫn gán người thật trên đó nếu đủ điều kiện. Khi chưa phân biệt được trong / ngoài phạm vi, thực hiện R-12.

**Quan hệ với taxonomy khi soạn bản cá nhân:** taxonomy đầu vào là `v0`, chưa có `bus`, `tuk_tuk`. Hai lớp này là đề xuất của bản 127 để tổng hợp; không tự dùng tên lớp chưa có trong Project. Cấu hình mới và trạng thái xác nhận của nhóm được quản lý trong [guideline chung v1.0](../guideline_v1.0_agreed.md), tách khỏi đề xuất cá nhân ban đầu.

### 2.2. Thuộc tính và ngưỡng

Mỗi box đối tượng có đủ `occluded_level`, `truncated`, `can_review`. Vùng `ignore_region` không cần ba thuộc tính trong cấu hình CVAT do script sinh.

**`occluded_level`:** ước lượng tỉ lệ diện tích hình chiếu của vật bị một vật khác che. Chỉ xét phần vật nằm trong khung ảnh, không cộng phần mất ngoài mép ảnh. Đây là đánh giá che khuất; không kéo box ra phần bị che để biểu diễn tỉ lệ này.

| Giá trị | Tỉ lệ bị che `p` | Cách áp dụng |
|---|---|---|
| `0` | `p = 0%` | Không thấy dấu hiệu vật khác che vật đang gán. |
| `1` | `0% < p <= 30%` | Bị che ít, vẫn nhìn thấy phần lớn hình dạng. |
| `2` | `30% < p <= 70%` | Bị che nhiều nhưng vẫn xác định được cá thể và lớp. |
| `3` | `70% < p < 100%` | Chỉ còn một phần nhỏ nhưng vẫn nhận diện chắc chắn và phần nhìn thấy đáp ứng ngưỡng chiều cao. |

Các khoảng không chồng nhau: đúng 30% thuộc mức `1`, đúng 70% thuộc mức `2`. Phần trăm là ước lượng trên ảnh, không phải phép đo tự động; nếu không đủ cơ sở chọn mức hoặc lớp, bật `can_review` và ghi lý do. Vật bị che hoàn toàn không được tạo box suy đoán.

| Thuộc tính | Giá trị | Quy tắc |
|---|---|---|
| `truncated` | `yes` | Một phần vật thực sự nằm ngoài khung ảnh; box dừng tại mép ảnh. Chỉ chạm mép mà vật vẫn trọn vẹn chưa đủ để kết luận bị cắt. |
| `truncated` | `no` | Vật không bị cắt bởi mép ảnh; vẫn có thể bị vật khác che. |
| `can_review` | `true` | Có box tạm nhưng chưa chắc lớp, biên hoặc thuộc tính; cần ghi ảnh, vùng / ID box và câu hỏi. |
| `can_review` | `false` | Đã áp dụng được quy tắc mà không còn điểm chưa chắc. |

Một vật có thể đồng thời `occluded_level > 0` và `truncated=yes`. Cờ occluded mặc định của CVAT, phím `Q`, không thay thế thuộc tính `occluded_level` mà script đọc.

## 3. Quy tắc vẽ box

Các mã `R-01` đến `R-12` thuộc bản đề xuất 127; khi hợp nhất, nhóm cần thống nhất mã và cập nhật mọi chỗ dẫn chiếu.

### R-01 — Biên box và phần nhìn thấy

- Dùng Rectangle song song với hai trục ảnh, ôm sát các điểm ngoài cùng nhìn thấy của một cá thể.
- Khi vật bị che, box bao toàn bộ các phần còn nhìn thấy của cùng vật; khoảng trống giữa các phần có thể nằm trong hình chữ nhật. Không mở rộng tới vị trí đầu / chân / đuôi xe chỉ suy đoán.
- Không bao bóng đổ, phản sáng trên mặt đường, khói hoặc nền rời. Phần cố định của phương tiện như bánh, gương và khung xe nằm trong phạm vi box.
- Tọa độ phải nằm trong ảnh, chiều rộng và chiều cao dương. Khi xuất COCO, bbox có dạng `[x, y, width, height]`.

### R-02 — Vật bị cắt mép ảnh

Gán phần còn nhìn thấy nếu xác định được lớp và box cao ít nhất 20 px; đặt `truncated=yes`, không kéo box vượt biên. Nếu phần còn thấy thấp hơn 20 px thì áp dụng R-06. Không coi cắt mép ảnh là che khuất bởi vật khác.

### R-03 — Người và phương tiện là các cá thể riêng

- Một người trên một xe máy: một box `person` và một box `motorcycle` nếu cả hai đều đủ điều kiện. Hai người trên một xe máy: hai box `person` và một box `motorcycle` khi tách được từng người.
- Box xe theo phần phương tiện, không mở rộng chỉ để ôm đầu / thân người ngồi trên đó. Box người theo phần cơ thể nhìn thấy, không ôm cả xe.
- Box người bao quần áo, mũ đội và vật đeo sát người; không mở rộng theo ô xòe, vật dài cầm tay hoặc kiện hàng rời.
- Người đi xe đạp / xích lô vẫn được gán `person`; phương tiện không động cơ nằm ngoài phạm vi đề xuất.
- Người qua cửa kính áp dụng cùng điều kiện nhận diện, kích thước và che khuất. Không gán người chỉ dựa trên suy đoán rằng xe đang có tài xế.
- Mô tô có sidecar gắn liền: một box `motorcycle` bao phần xe và sidecar nhìn thấy; người trong sidecar là cá thể riêng.

### R-04 — Xe chở xe

Gán một box cho xe chở theo kết cấu phương tiện chở, đồng thời gán riêng từng xe được chở nếu chúng là xe thật, nhận diện được và cao ít nhất 20 px. Không gộp các xe thành một đối tượng. Box xe chở có thể chứa các box xe con do hình học hình chữ nhật; không kéo biên xe chở theo phần xe con nhô ra ngoài kết cấu xe chở. Tượng / mô hình / vỏ xe trang trí áp dụng R-05 và R-12.

### R-05 — Vật giả, hình in và phản chiếu

Không gán box đối tượng hoặc vùng ignore cho tượng người / xe, ma-nơ-canh, đồ chơi, ảnh trên biển quảng cáo, tranh hoặc vật chỉ xuất hiện qua phản chiếu. Quy tắc này không loại người / phương tiện thật nhìn xuyên qua kính. Nếu không phân biệt được phản chiếu với vật thật sau kính, ghi vấn đề cần review thay vì tự kết luận.

### R-06 — Vật nhỏ và đám đông

- Xét chiều cao box ôm phần nhìn thấy ở độ phân giải ảnh gốc: dưới 20 px thì không gán lớp đối tượng, dùng vùng `ignore_region` vừa đủ bao vật / cụm vật thuộc phạm vi.
- Box cao đúng 20 px đủ ngưỡng; không dùng độ phóng đại trên màn hình làm kích thước pixel gốc.
- Vật từ 20 px trở lên và tách được từng cá thể phải gán riêng, kể cả trong cảnh đông.
- Với phần đám đông / cụm xe không thể tách cá thể đáng tin cậy sau khi phóng to, dùng vùng ignore ôm phần không tách được. Không gom cả vùng chỉ vì có nhiều vật.
- Không đặt vùng ignore rộng để che cá thể rõ ở tiền cảnh, giấu lỗi bỏ sót hoặc thay thế lựa chọn lớp. Nếu không thể chia vùng rõ ràng, ghi review theo R-12.

### R-07 — Xe chủ / ego-vehicle

Không gán phần xe gắn camera nếu nhận diện chắc chắn là xe chủ, ví dụ nắp capo / bảng điều khiển của xe chụp. Phương tiện khác bị cắt mép vẫn theo R-02. Nếu chưa xác định được là xe chủ hay xe khác, cần review, không bỏ qua chỉ vì vật ở mép dưới ảnh.

### R-08 — Ngoài taxonomy và đề xuất lớp mới

Đề xuất tách `bus`, `tuk_tuk` vì đây là loại phương tiện mà mô hình cần dự đoán. Các cờ che khuất / cắt mép / cần review là thuộc tính. Khi tổng hợp Pha 2, nhóm phải quyết định nhận hoặc loại các lớp mới và ghi phạm vi rõ trong cả guideline lẫn taxonomy. Trong lượt gán độc lập, chỉ dùng bộ lớp v1.0 đã khóa; gặp loại chưa được định nghĩa thì ghi vấn đề, không tự thêm nhãn hoặc ép vào lớp gần nhất.

### R-09 — Pickup, van và xe buýt

Pickup vẫn là `truck` khi lắp nắp thùng nếu còn đủ dấu hiệu xác định kết cấu cabin và thùng hàng tách biệt. Van có thân liền khối; bus có kết cấu xe buýt / xe khách chuyên dụng. Không dùng kích thước tương đối trên ảnh hoặc số ghế suy đoán làm tiêu chí duy nhất. Khi dấu hiệu kết cấu không đủ rõ, gán tạm và bật `can_review` nếu đã có lớp phù hợp trong taxonomy; chưa đủ cơ sở vẽ thì ghi vấn đề cấp ảnh.

### R-10 — Hàng hóa và phụ kiện

Box phương tiện gồm kết cấu xe và bộ phận gắn cố định như mái / thùng xe. Không kéo biên theo bao tải, kiện hàng, vật liệu hoặc hàng rời nhô ra. Hàng nằm trong đường bao xe có thể nằm trong hình chữ nhật; không vẽ box riêng cho hàng ngoài phạm vi. Nếu không phân biệt được hàng rời với bộ phận cố định, ghi review.

### R-11 — Nhòe chuyển động

Nếu còn xác định được từng vật, vẽ box theo ranh giới phần thân có thể nhận biết, không ôm hết vệt nhòe. Không gộp hai vật chỉ vì vệt nhòe nối nhau. Nhòe không đồng nghĩa với che khuất: không tăng `occluded_level` chỉ do ảnh mờ. Khi không xác định được biên / số cá thể / lớp, thực hiện R-12.

### R-12 — Ca chưa chắc và người chốt

- Phân biệt **loại theo quy tắc** (đủ bằng chứng là vật giả / ngoài phạm vi) với **chưa chắc** (cần thêm quyết định về lớp, biên hoặc thuộc tính).
- Có box tạm: bật `can_review=true`, lưu câu hỏi cùng tên ảnh và ID box / vùng ảnh.
- Chưa vẽ được box: ghi `image,annotator,region,note`, với `annotator=127`; nhóm tập hợp vào `submission/qc/image_issues.csv` sau khi khóa các bản độc lập.
- Trong Pha 3 không hỏi người cùng gán ảnh để chọn nhãn. Giữ nguyên bản độc lập trước calibration.
- Người điều phối 106 tổ chức chốt quy tắc ở Pha 2 và Pha 5; phiếu quyết định phải ghi đúng mã người thực sự chốt. Người review cuối theo TEAM.md là 157; ai kiểm lại từng ca phải được ghi theo thực tế, không mặc định đã duyệt.

## 4. Ca khó tham chiếu từ tài liệu lab

Các tình huống và tên ảnh dưới đây được tài liệu lab mô tả. Cột quyết định là **đề xuất quy tắc** để 127 đối chiếu ảnh trước khi xác nhận; không phải nhãn đã vẽ hoặc nhật ký quan sát cá nhân đã hoàn thành.

| Ảnh | Điểm v0 chưa nói rõ, theo tài liệu lab | Rule ID | Cách xử lý đề xuất | Lý do |
|---|---|---|---|---|
| `img_002.jpg` | Xe tải chở ô tô: chỉ gán xe chở hay cả xe được chở? | `R-04` | Gán xe chở và từng ô tô đủ điều kiện thành các đối tượng riêng. | Từng xe vẫn là một cá thể; không dùng một box chung thay cho tất cả. |
| `img_004.jpg`, `img_035.jpg` | Tuk-tuk không có lớp riêng trong v0. | `R-08` | Đề xuất lớp `tuk_tuk`; nhóm cần chốt và thêm vào taxonomy trước gán. | Hình thái khác xe máy hai bánh và ô tô con. |
| `img_009.jpg`, `img_037.jpg` | Phân biệt bus với van / truck. | `R-09` | Đề xuất lớp `bus`; dựa trên kết cấu xe, ca không rõ chuyển review. | Tránh ép mọi xe chở khách vào van hoặc đoán số chỗ. |
| `img_037.jpg` | Pickup lắp nắp thùng cao dễ nhầm van. | `R-09` | Chọn `truck` nếu xác nhận kết cấu pickup; không rõ dấu hiệu thì review. | Nắp thùng không tự biến kết cấu pickup thành van liền khối. |
| `img_017.jpg`, `img_027.jpg` | Tượng có hình dạng người / pickup. | `R-05` | Không gán lên tượng; vẫn kiểm các vật thật khác có trong ảnh. | Hình dạng giống lớp chưa đủ để là cá thể thật. |
| `img_033.jpg`, `img_038.jpg` | Ma-nơ-canh và đồ chơi. | `R-05` | Không gán vật giả, không dùng vùng ignore cho chính vật giả. | Chúng là phản ví dụ của person / phương tiện thật. |
| `img_018.jpg` | Chân dung trên biển quảng cáo. | `R-05` | Không gán `person` cho hình in. | Vật trong biểu diễn 2D không phải người thật trong cảnh. |
| `img_020.jpg` | Vật xuất hiện qua gương chiếu hậu. | `R-05` | Không gán vật chỉ xuất hiện dưới dạng phản chiếu. | Tránh đếm thêm một cá thể từ hình phản chiếu. |
| `img_006.jpg`, `img_029.jpg`, `img_039.jpg` | Xe đạp / xích lô không động cơ nằm ngoài các lớp đề xuất. | `R-03`, `R-08` | Không gán phương tiện ngoài phạm vi; gán riêng người thật đủ điều kiện. | Phạm vi phương tiện không quyết định phạm vi người. |
| `img_035.jpg` | Khối hàng vượt ra ngoài xe ảnh hưởng biên box. | `R-10` | Ôm kết cấu phương tiện, không mở rộng theo hàng rời nhô ra. | Hai người cần dùng cùng định nghĩa phạm vi xe. |
| `img_024.jpg` | Phân biệt xe được chở với thân vỏ / vật trưng bày đặt trên xe. | `R-04`, `R-05`, `R-12` | Nếu xác nhận chỉ là vỏ trang trí thì loại; chưa đủ bằng chứng thì cần review. | Không kết luận xe thật chỉ từ đường bao giống xe. |
| `img_014.jpg`, `img_015.jpg` | Nhòe chuyển động làm ranh giới vật khó xác định. | `R-11` | Theo phần thân còn nhận biết; không ôm hết vệt mờ; review nếu biên không chắc. | Mờ ảnh và che khuất là hai vấn đề khác nhau. |

**Phép thử nhất quán trước khi gửi bản cá nhân:** tự chọn hai ảnh có người đi xe máy trong bộ dữ liệu, áp dụng R-03 trên từng ảnh và ghi tên ảnh, số cá thể nhìn thấy, số box đề xuất cùng lý do vào ghi chú cá nhân. Chỉ ghi đã thử sau khi thực sự làm. Khi khảo sát các ảnh còn lại, bổ sung mọi ca chưa được bảng tham chiếu bao phủ.

## 5. QC và phiên bản

### 5.1. Ngưỡng cố định của bài

Giữ nguyên tám giá trị trong `rules` của taxonomy. Bảng này là tiêu chí kiểm, không phải kết quả đã đạt.

| Khóa | Giá trị | Ý nghĩa |
|---|---|---|
| `min_box_height_px` | `20` | Box đối tượng đủ ngưỡng khi cao ít nhất 20 px. |
| `large_px` | `50` | Box cao ít nhất 50 px thuộc nhóm lớn; 20 đến dưới 50 px thuộc nhóm nhỏ. |
| `iou_thr_large` | `0.85` | Ngưỡng IoU cho box lớn. |
| `iou_thr_small` | `0.70` | Ngưỡng IoU cho box nhỏ. |
| `match_iou` | `0.50` | Ngưỡng ghép box 1–1 trước khi đo IAA. |
| `iaa_iou` | `0.85` | IoU trung bình tối thiểu trên các cặp box ghép. |
| `iaa_class` | `0.95` | Tỉ lệ đồng thuận lớp tối thiểu trên các cặp box ghép. |
| `error_rate_img_max` | `0.02` | Tỉ lệ ảnh có lỗi tối đa khi xét Release, cùng các điều kiện bằng chứng. |

### 5.2. Kiểm trong quy trình

- **Độc lập:** nhóm ba người dùng phân công mỗi ảnh hai người gán, tổng 80 lượt. 127 làm đúng 27 ảnh được giao trong Pha 3; Pha 1 vẫn khảo sát cả 40 ảnh.
- **Kiểm cấu trúc:** xuất COCO 1.0, tắt Save images, lưu `submission/labels/127.json` rồi chạy `python scripts/validate_labels.py submission/labels/127.json`. Ảnh không có đối tượng vẫn phải nằm trong COCO.
- **IAA:** cần đủ 100% lượt gán trước khi chạy `qc_compare.py`. IoU trung bình và đồng thuận lớp chỉ tính trên cặp ghép; không có cặp thì N/A. Ca bỏ sót / thừa và bất đồng ignore phải xem riêng; hai người giống nhau vẫn có thể cùng sai.
- **Calibration:** mỗi ca có quyết định, mã quy tắc, người chốt, vết sửa và người kiểm lại. Nếu đổi cách chọn vật, lớp, biên box hoặc thuộc tính thì là thay đổi semantic, phải ghi phạm vi cả lô và kiểm lại đủ 40 ảnh.
- **QC:** duyệt toàn bộ 40 ảnh bằng vòng có số thứ tự. Tỉ lệ lỗi theo ảnh = số ảnh có ít nhất một lỗi / số ảnh đã duyệt; tỉ lệ lỗi theo đối tượng = số annotation đối tượng hiện có bị lỗi / số annotation đối tượng đã duyệt. Vật bỏ sót ghi riêng; luôn ghi tử số và mẫu số.
- **Quyết định:** Hold nếu thiếu bằng chứng / còn vấn đề chưa giải quyết; Rework nếu ảnh lỗi vượt 2% hoặc có một loại lỗi xuất hiện ít nhất 3 lần và chiếm ít nhất 50% tổng lỗi; Release khi đủ bằng chứng, duyệt toàn lô và đạt các điều kiện. Với 40 ảnh, một ảnh lỗi đã là 2,5%, nên muốn đạt ngưỡng ảnh lỗi phải là 0/40.

### 5.3. Lịch sử phiên bản

| Phiên bản | Thay đổi | Căn cứ |
|---|---|---|
| `v0` | Bản khởi đầu của giảng viên với định nghĩa và thuộc tính sơ lược. | `guideline/guideline_v0_starter.md`. |
| `v0.1-draft-127` | Tạo khung cá nhân và chép các ngưỡng cố định; các phần nội dung còn trống. | Chuẩn bị phần cá nhân ngày 2026-11-16. |
| `v0.2-proposal-127` | Viết nội dung năm phần, đề xuất bus / tuk-tuk, ngưỡng che khuất, 12 quy tắc và bảng ca khó tham chiếu; phân biệt đề xuất với quan sát đã kiểm chứng. | Tài liệu lab và yêu cầu soạn guideline ngày 2026-11-17; chưa chốt nhóm. |

### 5.4. Đối chiếu và tổng hợp thành guideline nhóm

1. 127 tự đối chiếu đề xuất với cả 40 ảnh, sửa điểm không phù hợp và ghi ba ca khó thực sự đã xem vào ghi chú cá nhân.
2. Sau khi 106, 127 và 157 đã gửi riêng bản cá nhân, đối chiếu từng chủ đề: phạm vi lớp, bus / tuk-tuk, ngưỡng che khuất, người đi xe máy, xe chở xe, vật giả, ignore và biên hàng hóa.
3. Với điểm khác nhau, ghi quyết định cuối và lý do; không tự coi bản của một người là quy tắc đã thống nhất. Lưu guideline nhóm tại `submission/guidelines/guideline_v1.0_agreed.md` với lịch sử `v0 → v1.0`.
4. Cập nhật `submission/taxonomy.json` khớp bộ lớp / thuộc tính được nhóm chấp nhận, tăng version thành `v1.0`, giữ nguyên `rules`, chạy `python scripts/make_cvat_labels.py` rồi khóa hai tài liệu cho lượt gán độc lập.
5. Giữ nguyên bản cá nhân đã nộp để truy vết khác biệt. Bản sau calibration lưu riêng tại `guideline_v1.1_calibrated.md`.

**Trạng thái đối chiếu:** chưa có bằng chứng trong ghi chú rằng 127 đã tự xem đủ 40 ảnh. Bản 157 và bản tổng hợp v1.0 đã được soạn bổ sung theo yêu cầu ngày 2026-11-17; trạng thái xác nhận / khóa bản chung theo TEAM.md, không suy ra từ việc các file đã có. Các quy tắc và bảng ca khó của bản cá nhân này được giữ để đối chiếu với bản chung.
