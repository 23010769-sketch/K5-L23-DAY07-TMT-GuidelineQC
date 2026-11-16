# Guideline chung — TMT — v1.0

> Bản tổng hợp được soạn theo yêu cầu của thành viên 127 từ ba tài liệu cá nhân 106, 127 và 157, có hỗ trợ của Codex. Tên file theo quy cách bài nộp; trạng thái hiện tại là **đã soạn, chờ nhóm đối chiếu ảnh và xác nhận trước khi khóa v1.0**. Chưa ghi nhận cuộc họp thông qua hoặc xác nhận ba bản đã được viết độc lập. Bảng lựa chọn ở Mục 5 là đề xuất hợp nhất, không phải biên bản cuộc họp đã diễn ra.

- Nhóm: `TMT`; thành viên: `106`, `127`, `157`.
- Phiên bản đặc tả và taxonomy: `v1.0`.
- Ngày soạn: `2026-11-17`.
- Điều phối theo TEAM.md: `106`; review cuối: `157`. Chưa mặc định các vai trò này đã thực hiện duyệt tài liệu.
- Nguồn cá nhân: [106 — v0.1-draft-106](individual/guideline_106.md), [127 — v0.2-proposal-127](individual/guideline_127.md), [157 — v0.1-proposal-157](individual/guideline_157.md).
- Căn cứ bài: [hướng dẫn lab](../../K5-L23-Day07-Guideline-Taxonomy-QC.md), [quy tắc](../../RULES.md), [ngưỡng QC](../../docs/QC_CHEATSHEET.md).
- Cấu hình đi kèm: [taxonomy.json](../taxonomy.json), [labels_generated.json](../../cvat/labels_generated.json).

## 1. Mục tiêu và phạm vi

Bộ nhãn dùng cho thực hành phát hiện người và phương tiện giao thông đường bộ trên ảnh 2D. Mỗi cá thể thuộc phạm vi có một lớp và một bounding box ôm sát phần nhìn thấy. Vùng không gán từng cá thể được xử lý riêng theo R-06.

Ưu tiên tránh bỏ sót người / phương tiện thật, gán nhầm vật giả hoặc phản chiếu, nhầm lớp và vẽ sai biên. Box không biểu diễn khoảng cách, vận tốc, phần vật bị che hoàn toàn hoặc phần nằm ngoài ảnh. Bộ 40 ảnh là dữ liệu thực hành, chưa có bằng chứng cho sử dụng thực tế.

- Phạm vi khảo sát guideline: cả 40 ảnh `img_001.jpg` đến `img_040.jpg`.
- Lượt gán độc lập: dùng [assignment.csv](../qc/assignment.csv), seed 42; 106 nhận 26 ảnh, 127 nhận 27 ảnh, 157 nhận 27 ảnh. Mỗi ảnh hai người gán, tổng 80 lượt, không phải ba người cùng gán mọi ảnh.
- Ngoài phạm vi phương tiện: xe đạp / xích lô không động cơ, phương tiện không thuộc bảy lớp đối tượng, vật trang trí và hàng hóa rời. Người thật trên phương tiện ngoài phạm vi vẫn được xét riêng.
- Tượng, ma-nơ-canh, hình in, đồ chơi và phản chiếu là phản ví dụ; không tạo vùng ignore chỉ cho các vật đó.

## 2. Taxonomy và thuộc tính

### 2.1. Danh sách lớp

Taxonomy v1.0 có **bảy lớp đối tượng và một lớp vùng**. Tên lớp phải trùng chính xác giữa guideline, taxonomy, Project CVAT và COCO.

| Lớp | Định nghĩa | Ví dụ / dấu hiệu | Phản ví dụ / ranh giới |
|---|---|---|---|
| `car` | Ô tô con dạng sedan, hatchback, SUV hoặc crossover; không có thùng chở hàng tách biệt với cabin. | Xe con chở người, SUV; phân biệt bằng kết cấu nhìn thấy. | Pickup là truck; thân van là van; đồ chơi / tượng xe không gán. |
| `truck` | Xe chở hàng có cabin và thùng hoặc kết cấu chở hàng tách biệt, gồm pickup và xe chuyên dụng chở xe. | Xe chở ô tô trong mô tả `img_002.jpg`; pickup có nắp thùng ở `img_037.jpg` cần đối chiếu kết cấu. | Van liền khối; SUV; tượng pickup tại `img_027.jpg`. |
| `van` | Xe có thân dạng hộp liền khối giữa cabin và khoang chở hàng hoặc hành khách, không có thùng hàng riêng như pickup. | Panel van hoặc minibus dùng thân van. | Xe buýt chuyên dụng là bus; pickup lắp nắp thùng vẫn là truck. |
| `bus` | Xe buýt hoặc xe khách có thân và khoang hành khách chuyên dụng kéo dài, phân biệt với minibus dùng thân van. | Các ca xe buýt được lab nêu ở `img_009.jpg`, `img_037.jpg`. | Không dựa vào số ghế suy đoán hoặc kích thước tương đối trong ảnh để phân lớp. |
| `tuk_tuk` | Xe cơ giới ba bánh dạng tuk-tuk, xe lam hoặc xe ba gác máy có khoang chở người hoặc sàn chở hàng. | Ca tuk-tuk tại `img_004.jpg`, `img_035.jpg` theo lab. | Xích lô đạp không động cơ; mô tô có sidecar; mô hình trưng bày. |
| `motorcycle` | Xe máy hoặc mô tô hai bánh có động cơ, gồm scooter; sidecar gắn liền được tính cùng một phương tiện. | Xe số, tay ga, mô tô có sidecar cố định. | Tuk-tuk không thuộc lớp này; người ngồi trên xe gán riêng; xe đạp không động cơ ngoài phạm vi. |
| `person` | Người thật nhận diện được trong cảnh, kể cả người trên phương tiện hoặc nhìn rõ qua kính. | Người đi bộ, người lái / ngồi sau xe máy; người thật đủ rõ sau kính. | Tượng, ma-nơ-canh, người trong hình in, phản chiếu; không suy đoán người sau kính tối. |
| `ignore_region` | Vùng chứa đối tượng trong phạm vi có phần nhìn thấy cao dưới 20 px hoặc cụm không thể tách từng cá thể đáng tin cậy. | Vật quá nhỏ, phần đám đông không tách được theo R-06. | Không dùng thay cho việc chưa chọn được lớp, không gom vật rõ, không dùng để giấu lỗi. |

Trình tự xét vật: vật thật hay biểu diễn → trong / ngoài phạm vi → lớp theo kết cấu → kích thước và khả năng tách cá thể → box và thuộc tính. Nếu không đủ cơ sở phân lớp, áp dụng R-12; không ép vào lớp gần nhất. Không suy đoán số chỗ, động cơ bị khuất hoặc nhãn hiệu để quyết định.

### 2.2. Thuộc tính

Mọi box thuộc bảy lớp đối tượng có đủ ba thuộc tính dưới đây. `ignore_region` không cần thuộc tính trong cấu hình CVAT do script sinh; không yêu cầu người gán tự thêm mặc định cho vùng ignore.

**`occluded_level`:** ước lượng phần diện tích hình chiếu bị vật khác che, so với diện tích hình chiếu ước lượng của chính vật trong khung ảnh. Không cộng phần bị cắt ngoài ảnh, không kéo box ra phần bị che để biểu diễn mức che, không coi kính trong hoặc nhòe là che khuất chỉ vì chúng làm khó quan sát.

| Giá trị | Tỉ lệ bị che `p` | Tiêu chí |
|---|---|---|
| `0` | `p = 0%` | Không thấy dấu hiệu bị vật khác che. |
| `1` | `0% < p <= 30%` | Bị che ít, còn thấy phần lớn hình dạng. |
| `2` | `30% < p <= 70%` | Bị che nhiều nhưng vẫn xác định được cá thể và lớp. |
| `3` | `70% < p < 100%` | Còn phần nhìn thấy cao ít nhất 20 px và vẫn nhận diện chắc chắn. |

Đúng 30% thuộc mức 1, đúng 70% thuộc mức 2. Không tự bỏ vật chỉ vì ước lượng che trên 85%; tiêu chí là nhận diện được và đủ kích thước. Vật che hoàn toàn không có box suy đoán. Không chắc mức thì bật `can_review` và nêu lý do.

| Thuộc tính | Giá trị | Cách áp dụng |
|---|---|---|
| `truncated` | `yes` / `no` | `yes` khi một phần vật thật sự nằm ngoài khung; chỉ chạm mép mà vẫn trọn vẹn chưa đủ để chọn yes. Có thể đồng thời bị che khuất. |
| `can_review` | `true` / `false` | `true` khi có box tạm nhưng còn nghi vấn lớp, biên hoặc thuộc tính; kèm ghi chú ảnh, ID box / vùng và câu hỏi. Chỉ tắt khi đã giải quyết nghi vấn. |

Cờ occluded mặc định của CVAT, phím Q, khác với `occluded_level` mà script của bài đọc. Giữ đúng tập giá trị taxonomy, không tạo thuộc tính tự do thay thế.

## 3. Quy tắc vẽ box

Mã R-01 đến R-12 dưới đây là mã chuẩn của bản chung. Khi dùng phiếu calibration / QC, dẫn theo mã bản chung và ghi phiên bản; không lấy mã cùng số từ bản cá nhân 106 nếu nội dung khác.

### R-01 — Box theo phần nhìn thấy

Rectangle song song hai trục ảnh, ôm sát điểm ngoài cùng nhìn thấy của cùng một cá thể. Nếu vật bị vật mỏng chia thành nhiều mảnh nhưng xác định được là cùng vật, vẽ một box bao các phần nhìn thấy; nền / vật cản giữa chúng có thể nằm trong hình chữ nhật. Không đoán biên đầu, chân hoặc đuôi bị che hoàn toàn. Không bao bóng đổ, khói, phản sáng hoặc vệt sáng rời. Không thêm ngưỡng sai số 2 px vào tiêu chí QC cố định của bài.

### R-02 — Cắt mép ảnh

Gán phần còn thấy nếu xác định được lớp và chiều cao ít nhất 20 px, đặt `truncated=yes`, dừng box tại biên ảnh. Phần còn thấy dưới 20 px theo R-06. Tọa độ hợp lệ: `0 <= x_min < x_max <= width`, `0 <= y_min < y_max <= height`; COCO lưu `[x, y, width, height]`.

### R-03 — Người và phương tiện riêng biệt

Một người trên một xe máy đủ điều kiện tương ứng hai box: person và motorcycle. Hai người trên một xe tương ứng ba box khi tách được từng cá thể. Không kéo box xe ôm đầu / thân người hoặc kéo box người ôm cả xe. Người qua kính được gán khi nhận diện chắc chắn là người thật và đủ kích thước; không loại chỉ vì cabin kín và không gán chỉ vì suy đoán xe cần tài xế. Phân biệt nhìn xuyên kính với phản chiếu theo R-05. Sidecar gắn cố định thuộc cùng một box motorcycle, người trong sidecar vẫn là cá thể riêng.

### R-04 — Xe chở xe

Gán xe chở theo kết cấu của xe chở và từng phương tiện được chở đủ điều kiện thành các box riêng. Không gộp xe con thành một box truck hoặc bỏ xe con chỉ vì đang là hàng chở. Không kéo biên xe chở theo xe con nhô ra ngoài kết cấu xe chở. Box có thể lồng / chồng do hình học hình chữ nhật. Nếu vật trên xe chỉ là tượng, mô hình hoặc vỏ trang trí thì áp dụng R-05; chưa rõ bản chất thì R-12.

### R-05 — Vật giả, hình in và phản chiếu

Không gán đối tượng hoặc vùng ignore cho tượng, ma-nơ-canh, đồ chơi, hình người / xe in trên biển / thân xe, tranh hoặc vật chỉ xuất hiện trong phản chiếu. Vẫn kiểm vật thật khác trong ảnh, không tự coi cả ảnh là trống vì có tượng. Nhìn xuyên kính không phải phản chiếu; chưa phân biệt được thì ghi review.

### R-06 — Vật nhỏ và vùng ignore

- Chiều cao xét theo phần nhìn thấy và độ phân giải ảnh gốc. Dưới 20 px: dùng vùng ignore vừa đủ cho vật / cụm vật thuộc phạm vi; đúng 20 px: đủ ngưỡng box đối tượng.
- Với đám đông / cụm xe không tách được cá thể đáng tin cậy sau khi phóng to, vùng ignore chỉ bao phần không tách được. Vật rõ, đủ lớn phải được gán riêng.
- Không tự gom theo số lượng ba hay bốn vật. Không dùng ignore làm lớp cho vật chưa biết loại, không dùng cho vật giả hoặc phương tiện chắc chắn ngoài phạm vi.
- Tránh vùng ignore phủ cá thể rõ ở tiền cảnh. Khi không chia vùng được đáng tin cậy hoặc nghi có ảnh hưởng tới cá thể đang gán, ghi review; không mở rộng ignore để né lỗi.

### R-07 — Xe chủ / ego-vehicle

Bỏ phần xe gắn camera chỉ khi có đủ dấu hiệu là xe chủ, chẳng hạn phần nắp capo / bảng điều khiển của xe chụp. Xe khác bị cắt ở mép dưới vẫn theo R-02. Không chắc là xe chủ thì ghi review, không bỏ chỉ vì vị trí trong ảnh.

### R-08 — Phạm vi và bộ lớp đã khóa

Dùng đúng tám tên trong taxonomy v1.0. Bus và tuk-tuk là lớp đối tượng, không đưa vào ignore chỉ vì v0 thiếu lớp. Xe đạp / xích lô không động cơ ngoài phạm vi phương tiện, nhưng người thật trên đó theo R-03. Sau khi nhóm khóa v1.0, người gán không tự thêm lớp, đổi nghĩa lớp hoặc sửa taxonomy; ca mới chuyển R-12.

### R-09 — Pickup, van, bus và xe ba bánh

Pickup lắp nắp thùng vẫn là truck nếu xác định được cabin và thùng tách biệt. Van có thân liền khối; bus có kết cấu xe buýt / xe khách chuyên dụng; không suy ra số ghế để chọn lớp. Tuk-tuk / xe lam / ba gác máy thuộc tuk_tuk, mô tô có sidecar thuộc motorcycle. Không đủ dấu hiệu kết cấu thì gán tạm với can_review hoặc ghi vấn đề chưa vẽ được box.

### R-10 — Hàng hóa, phụ kiện và vật mang theo

Box phương tiện gồm bánh, gương, khung, mái và thùng gắn cố định. Không kéo theo hàng rời nhô ra như bao tải, kiện hàng, vật liệu. Box person gồm cơ thể nhìn thấy, quần áo, mũ đội và vật đeo sát người; không kéo theo ô xòe, vật dài cầm tay hoặc kiện hàng rời. Vật cố định với phương tiện khác hàng đặt trên phương tiện; không phân biệt được thì ghi review.

### R-11 — Nhòe chuyển động

Vẽ theo phần thân còn nhận biết, không ôm hết vệt nhòe. Không gộp các cá thể chỉ vì vệt nhòe nối nhau. Không tăng occluded_level chỉ vì ảnh mờ. Không xác định được biên, lớp hoặc số cá thể thì ghi review; không mặc định toàn bộ vùng mờ là ignore.

### R-12 — Ca chưa chắc và truy vết

Phân biệt ca đủ bằng chứng để loại theo quy tắc với ca chưa chắc cần hỏi. Có box tạm thì bật can_review và ghi ảnh, ID box / vùng, nghi vấn cùng mã quy tắc. Chưa vẽ được box thì ghi `image,annotator,region,note`; nhóm tập hợp vào `submission/qc/image_issues.csv` sau khi khóa các bản độc lập. Trong lượt gán không hỏi người cùng gán ảnh hoặc xem nhãn của họ. Giữ bản độc lập trước sửa. Phiếu calibration phải ghi người thực sự chốt và người thực sự kiểm lại; vai trò trong TEAM.md không tự chứng minh đã duyệt.

## 4. Ca khó và cách áp dụng

Tên ảnh và tình huống tham chiếu từ tài liệu lab; cột xử lý là cách áp dụng bản đặc tả để người gán tự kiểm trên ảnh. Bảng không phải nhãn COCO hoặc bằng chứng đã review.

| Ảnh | Tình huống | Rule ID | Cách xử lý theo bản chung | Lý do |
|---|---|---|---|---|
| `img_002.jpg` | Xe tải chở ô tô. | `R-04` | Gán xe chở và từng xe thật đủ điều kiện riêng. | Tách cá thể xe chở với xe được chở. |
| `img_004.jpg`, `img_035.jpg` | Tuk-tuk. | `R-08`, `R-09` | Dùng tuk_tuk khi xác nhận đúng định nghĩa. | Đã có lớp riêng trong cấu hình v1.0. |
| `img_009.jpg`, `img_037.jpg` | Xe buýt, ca dễ nhầm van. | `R-09` | Dựa vào kết cấu, dùng bus khi phù hợp; không rõ thì review. | Không suy đoán số ghế hoặc ép về van. |
| `img_037.jpg` | Pickup có nắp thùng cao. | `R-09` | Truck nếu xác nhận kết cấu pickup; thiếu dấu hiệu thì review. | Nắp thùng không thay đổi kết cấu nền của pickup. |
| `img_017.jpg`, `img_027.jpg` | Tượng người / pickup. | `R-05` | Không gán lên tượng; vẫn kiểm phần cảnh còn lại. | Loại biểu diễn của vật. |
| `img_033.jpg`, `img_038.jpg` | Ma-nơ-canh, đồ chơi. | `R-05` | Không gán vật giả, không bọc ignore cho chính vật giả. | Không phải cá thể thật thuộc mục tiêu phát hiện. |
| `img_018.jpg` | Chân dung trên biển quảng cáo. | `R-05` | Không gán person cho hình in. | Người trong biểu diễn 2D không phải người trong cảnh. |
| `img_020.jpg` | Phản chiếu qua gương. | `R-05` | Không gán vật chỉ xuất hiện trong phản chiếu. | Tránh gán thêm cá thể từ phản chiếu. |
| `img_006.jpg`, `img_029.jpg`, `img_039.jpg` | Xe đạp / xích lô không động cơ. | `R-03`, `R-08` | Bỏ phương tiện ngoài phạm vi, xét người thật riêng. | Phạm vi lớp person không phụ thuộc phương tiện. |
| `img_035.jpg` | Hàng hóa vượt đường bao xe. | `R-10` | Box theo kết cấu xe, không mở theo hàng rời. | Hai người dùng cùng phạm vi đối tượng. |
| `img_024.jpg` | Xe thật hay thân vỏ trưng bày trên xe. | `R-04`, `R-05`, `R-12` | Xác nhận chỉ là vỏ trang trí thì loại; chưa rõ thì review. | Không kết luận xe thật chỉ từ đường bao giống xe. |
| `img_014.jpg`, `img_015.jpg` | Nhòe chuyển động. | `R-11` | Biên theo phần thân nhận biết, không ôm vệt nhòe. | Mờ không đồng nghĩa che khuất hoặc một cá thể chung. |

Trước khi khóa v1.0, mỗi thành viên áp dụng R-03 trên hai ảnh có người đi xe máy và so cách giải thích quy tắc trong buổi thống nhất. Chỉ ghi tên ảnh, số box và kết quả thử sau khi thực sự kiểm tra; bảng trên không thay thế bước này.

## 5. QC, hợp nhất và phiên bản

### 5.1. Tám ngưỡng cố định

| Khóa | Giá trị | Ý nghĩa |
|---|---|---|
| `min_box_height_px` | `20` | Ngưỡng chiều cao phần nhìn thấy cho box đối tượng. |
| `large_px` | `50` | Ranh giới box lớn; 20 đến dưới 50 px là box nhỏ. |
| `iou_thr_large` | `0.85` | Ngưỡng IoU cho box lớn. |
| `iou_thr_small` | `0.70` | Ngưỡng IoU cho box nhỏ. |
| `match_iou` | `0.50` | Ngưỡng ghép box 1–1 trước tính IAA. |
| `iaa_iou` | `0.85` | IoU trung bình tối thiểu trên các cặp ghép. |
| `iaa_class` | `0.95` | Tỉ lệ đồng thuận lớp tối thiểu trên các cặp ghép. |
| `error_rate_img_max` | `0.02` | Tỉ lệ ảnh lỗi tối đa khi xét bàn giao. |

Không sửa mục rules để làm số liệu đạt. Khi so hai box, script phân nhóm lớn / nhỏ theo chiều cao trung bình của cặp; các ngưỡng không phải dung sai biên box theo pixel.

### 5.2. Bảng đối chiếu để duyệt bản hợp nhất

Các lựa chọn dưới đây do quá trình soạn bản chung đề xuất. Chúng ghi khác biệt trong tài liệu, không khẳng định các thành viên đã tranh luận hoặc bỏ phiếu.

| Chủ đề | Nội dung bản 106 | Nội dung bản 127 | Nội dung đề xuất 157 | Lựa chọn trong bản chung và lý do |
|---|---|---|---|---|
| Bus / tuk-tuk | Đưa ignore hoặc chờ bổ sung lớp. | Đề xuất hai lớp riêng. | Hai lớp riêng. | Thêm bus, tuk_tuk theo R-08; mô hình cần phân biệt hai dạng phương tiện. |
| Mức che 0 | Dưới 5%. | Bằng 0%. | Bằng 0%. | Chọn 0%, phần bị che dương đến 30% là mức 1; tránh dung sai 5% chưa thống nhất. |
| Mức che 3 | Trên 70% đến 85%; nêu loại khi không nhận diện nổi trên 85%. | Trên 70% đến dưới 100%, vẫn nhận diện được. | Theo khả năng nhận diện và chiều cao nhìn thấy. | Chọn quy tắc 127 / 157; không tạo khoảng trên 85% chưa định nghĩa. |
| Người qua kính | Thường loại trong cabin kín. | Gán nếu nhận diện chắc chắn và đủ kích thước. | Áp dụng cùng tiêu chí người thật. | Chọn R-03; kính trong khác phản chiếu, không dùng kiểu cabin làm lý do loại duy nhất. |
| Sidecar | Chưa chốt có ôm sidecar hay không. | Cùng box motorcycle. | Cùng phương tiện gắn liền. | Một box xe, người riêng; không nhầm sidecar với tuk_tuk. |
| Vùng ignore | Có cả loại chưa rõ, cụm hơn 3–4 vật. | Theo vật nhỏ và khả năng tách cá thể. | Không dùng để thay lựa chọn lớp. | R-06; vật rõ vẫn gán riêng, số lượng đông không đủ để bỏ qua. |
| Dung sai biên | Nêu sai số tối đa 2 px. | Không thêm ngưỡng pixel vào rules. | Giữ ngưỡng lab, kiểm biên theo ảnh. | Ôm sát phần thấy theo R-01; không coi 2 px là cổng QC tự động mới. |
| Thuộc tính ignore | Nêu giữ mặc định. | Không cần ba thuộc tính. | Theo cấu hình script. | Ignore không có thuộc tính trong CVAT; khớp make_cvat_labels.py. |
| Xe chở xe / hàng hóa | Xe được chở gán riêng, còn điểm cần chốt. | Tách xe, không kéo biên theo hàng rời. | Tách cá thể và kiểm đường bao kết cấu. | R-04 và R-10, phân biệt xe thật với vỏ trang trí. |

Ánh xạ mã của bản 106 sang bản chung: `R-01 → R-01`; `R-02 → R-03`; `R-03 → R-05`; `R-04 → R-04`; `R-05 → R-06`; `R-06 → R-10`; `R-07 → R-02 / R-07`; `R-08 → R-08 / R-09`. Bản 127 và đề xuất 157 dùng cùng chủ đề của bộ R-01 đến R-12. Nội dung bản chung là căn cứ sau khi khóa, không sửa các bản cá nhân để xóa khác biệt ban đầu.

### 5.3. Kiểm trong các pha tiếp theo

1. Sau khi các thành viên xác nhận nội dung, điều phối ghi mốc khóa v1.0 trong TEAM.md. Dùng taxonomy và cấu hình CVAT đi kèm, không thay bộ lớp trong lượt gán độc lập.
2. Mỗi người gán đúng ảnh được giao và xuất COCO 1.0, tắt Save images. Lưu `submission/labels/<mã>.json`, chạy validate_labels.py cho bản của mình, gửi riêng; ảnh 0 đối tượng vẫn nằm trong COCO. Chỉ tập hợp nhãn khi mọi người đã khóa bản độc lập.
3. Đủ 80/80 lượt gán mới chạy qc_compare.py. IoU trung bình và đồng thuận lớp chỉ trên cặp ghép, không có cặp thì N/A. Box chưa ghép / bỏ sót và bất đồng ignore xem riêng. Đối chiếu ca tham chiếu coach để tìm cả tình huống đồng thuận nhưng cùng sai.
4. Calibration có quyết định, mã rule, phiên bản, người chốt, vết sửa và người kiểm lại. Tạo v1.1 cùng guideline_change.json; đổi nghĩa lớp / chọn vật / box / thuộc tính là semantic, cần phạm vi cả lô và kiểm lại đủ 40 ảnh. Không tạo sẵn bằng chứng đã calibration ở giai đoạn này.
5. QC duyệt toàn lô bằng qc_sample.py --round 1, điền phiếu thật rồi chạy qc_report.py. Tỉ lệ ảnh lỗi = ảnh có ít nhất một lỗi / ảnh đã duyệt. Tỉ lệ đối tượng lỗi = annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt; vật bỏ sót ghi riêng. Luôn ghi mẫu số.
6. Hold nếu thiếu bằng chứng, vấn đề guideline / dữ liệu chưa giải quyết, còn can_review, lệch phiên bản hoặc chỉ duyệt mẫu. Rework nếu ảnh lỗi trên 2% hoặc một loại lỗi có ít nhất 3 lần và chiếm ít nhất 50% tổng lỗi. Release cần đủ bằng chứng và người review xác nhận đạt; lô 40 ảnh chỉ một ảnh lỗi đã là 2,5%. Sửa nhãn thì mở vòng QC mới, giữ vòng cũ.

### 5.4. Lịch sử phiên bản và trạng thái

| Phiên bản | Thay đổi | Căn cứ / trạng thái |
|---|---|---|
| `v0` | Bộ khởi đầu: năm lớp đối tượng, ignore và thuộc tính sơ lược. | Tài liệu giảng viên. |
| Các bản cá nhân 106 / 127 / 157 | Bổ sung định nghĩa, ca khó và đề xuất xử lý. | Nguồn được liên kết ở đầu tài liệu; không tự suy ra đã khảo sát / nộp độc lập. |
| `v1.0` | Hợp nhất thành tám lớp; thống nhất ngưỡng thuộc tính, bộ 12 rule và cách xử lý khác biệt; giữ nguyên tám rules QC. | Soạn ngày 2026-11-17 theo yêu cầu 127; chờ nhóm đối chiếu ảnh và xác nhận trước khi khóa. |

- [ ] Cả ba thành viên đã tự đối chiếu nội dung với ảnh và ghi phần hỗ trợ AI theo thực tế.
- [ ] Đã xử lý các khác biệt ở bảng 5.2 và thử quy tắc người đi xe máy trên hai ảnh.
- [ ] Nhóm đã xác nhận nội dung và ghi mốc khóa v1.0; chưa đánh dấu nếu chưa thực hiện.
- [ ] Project CVAT đã nhận đúng cấu hình tám lớp và ba thuộc tính cho từng lớp đối tượng.

Việc soạn bản v1.0 và sinh cấu hình không chứng minh đã gán nhãn, calibration hoặc duyệt QC; các kết quả đó được ghi khi thực hiện các pha tương ứng.
