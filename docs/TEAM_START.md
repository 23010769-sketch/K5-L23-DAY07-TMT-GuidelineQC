# TMT — Clone repo và bắt đầu gán nhãn

Repo nhóm: `https://github.com/23010769-sketch/K5-L23-DAY07-TMT-GuidelineQC`. Đây là repo riêng tư: tài khoản GitHub của thành viên cần được cấp quyền truy cập trước khi clone. URL không tự cấp quyền cho tài khoản khác.

## 1. Clone và tạo môi trường

Chạy một lần trên máy của từng thành viên. Repo có sẵn cả 40 ảnh trong `data/images/` cùng ghi công `data/ATTRIBUTION.csv`.

Windows PowerShell:

```powershell
git clone https://github.com/23010769-sketch/K5-L23-DAY07-TMT-GuidelineQC.git
cd K5-L23-DAY07-TMT-GuidelineQC
python -m venv .venv
$env:PYTHONUTF8 = '1'
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe scripts/split_assign.py --members 106,157,127 --seed 42 --copy-images
```

macOS / Linux:

```bash
git clone https://github.com/23010769-sketch/K5-L23-DAY07-TMT-GuidelineQC.git
cd K5-L23-DAY07-TMT-GuidelineQC
python3 -m venv .venv
.venv/bin/python -m pip install -r requirements.txt
.venv/bin/python scripts/split_assign.py --members 106,157,127 --seed 42 --copy-images
```

Thư mục `work/` và `.venv/` không được đưa lên GitHub. Lệnh chia ảnh tạo lại `work/` đúng theo phân công đã commit, không tạo Task trên CVAT. Giữ nguyên bộ mã và seed 42, không tự đổi phân công khi đã bắt đầu gán.

| Thành viên | Danh sách ảnh | Thư mục tải lên CVAT | Số frame Task | File COCO sau export |
|---|---|---|---|---|
| `106` | [106.txt](../submission/qc/assignments/106.txt) | `work/106/images/` | 26 | `submission/labels/106.json` |
| `127` | [127.txt](../submission/qc/assignments/127.txt) | `work/127/images/` | 27 | `submission/labels/127.json` |
| `157` | [157.txt](../submission/qc/assignments/157.txt) | `work/157/images/` | 27 | `submission/labels/157.json` |

Tổng 40 ảnh, mỗi ảnh hai người gán độc lập, tương ứng 80 lượt. Pha khảo sát guideline vẫn cần xem cả 40 ảnh; khi gán chỉ làm ảnh trong danh sách của mình.

## 2. Đọc và xác nhận guideline trước khi gán

- Bản chung: [guideline TMT v1.0](../submission/guidelines/guideline_v1.0_agreed.md), đặc biệt bảng khác biệt ở Mục 5.2.
- Trạng thái nhóm và mốc khóa: [TEAM.md](../TEAM.md).
- Bộ lớp / thuộc tính: [taxonomy.json](../submission/taxonomy.json), phiên bản `v1.0`.

Bản chung đã được soạn nhưng chưa ghi nhận nhóm xác nhận / khóa. Các thành viên đối chiếu ảnh và thống nhất nội dung trước khi gán độc lập; người điều phối cập nhật mốc khóa theo thực tế. Việc clone repo không đồng nghĩa với đã xác nhận guideline.

## 3. Tạo Project và Task trên CVAT

Người điều phối chuẩn bị một Project với nội dung [cvat/labels_generated.json](../cvat/labels_generated.json) ở phần cấu hình Labels Raw. Bộ nhãn gồm `car`, `truck`, `van`, `bus`, `tuk_tuk`, `motorcycle`, `person`, `ignore_region`.

Mỗi người tạo Task riêng `task-<mã>` trong Project, tải đúng ảnh trong `work/<mã>/images/` và đối chiếu số frame theo bảng. CVAT của lớp và quyền truy cập do lớp / người điều phối cung cấp; repo không chứa tài khoản đăng nhập.

Gán Rectangle theo guideline đã khóa; mỗi box đối tượng có `occluded_level`, `truncated`, `can_review`. Box chưa chắc thì bật `can_review`; chưa vẽ được box thì ghi ảnh, vùng và lý do riêng để nộp sau khi khóa bản độc lập. Cờ Q mặc định của CVAT không thay cho thuộc tính `occluded_level`.

Hướng dẫn thao tác đầy đủ: [CVAT_SETUP.md](CVAT_SETUP.md).

## 4. Xuất nhãn, tự kiểm và nộp riêng

Xuất **COCO 1.0**, **tắt Save images**. Giải nén, lấy `annotations/instances_*.json` và lưu thành file theo mã trong bảng. Giữ cả ảnh không có đối tượng trong Task / COCO.

Ví dụ cho 127, thay `127` bằng mã của mình nếu là thành viên khác:

```powershell
$env:PYTHONUTF8 = '1'
.\.venv\Scripts\python.exe scripts/validate_labels.py submission/labels/127.json
```

Sau khi kiểm hợp lệ, gửi riêng file COCO và vấn đề cần review cho điều phối / coach theo kênh lớp. **Chưa push nhãn độc lập, ảnh có box hoặc ghi chú vấn đề từng ảnh lên repo chung khi các thành viên khác chưa nộp xong.** Khi cả ba đã nộp riêng và khóa bản độc lập, điều phối mới tập hợp các file nhãn rồi commit để đo IAA. Không mở nhãn của người khác để hỏi trong lượt gán.

Những phần nhãn còn làm cục bộ không bị gitignore, nên kiểm `git status` và chọn đúng file khi commit; không dùng `git add .` cho nhãn chưa được phép tập hợp.

## 5. Kiểm môi trường nếu cần

```powershell
$env:PYTHONUTF8 = '1'
.\.venv\Scripts\python.exe -m unittest discover -s tests -v
```

Lần kiểm trên máy chuẩn bị bài: 19/20 đạt; test symlink gặp `WinError 1314` do quyền Windows. Đây là lỗi môi trường kiểm thử đã ghi nhận, không phải kết quả QC nhãn. Khi chạy trên máy khác, ghi đúng kết quả thực tế, không mặc định 20/20 đạt.

Các bước sau khi đủ nhãn: [GUIDE.md](../GUIDE.md). Không tự sửa ngưỡng `rules` trong taxonomy hoặc ghi số liệu / kết quả duyệt chưa thực hiện.
