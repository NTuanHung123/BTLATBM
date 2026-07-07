# Multi-Cloud Secure Replication

## Giả lập upload/download tài liệu lên nhiều cloud

Đây là project bài tập lớn học phần **Nhập môn An toàn bảo mật thông tin**.

Hệ thống mô phỏng quá trình upload và download tài liệu an toàn trên nhiều cloud. Tài liệu được mã hóa trước khi lưu trữ, sau đó sao chép lên ba cloud giả lập. Hệ thống có khả năng kiểm tra tính toàn vẹn, phát hiện dữ liệu bị sửa hoặc mất và tự động tải từ cloud dự phòng.

---

## Chức năng chính

- Upload tài liệu qua giao diện web.
- Mã hóa tài liệu bằng AES-256-GCM trước khi lưu.
- Sao chép bản mã lên 3 cloud giả lập.
- Lưu metadata riêng cho từng cloud.
- Kiểm tra tính toàn vẹn bằng SHA-256 và Authentication Tag.
- Phát hiện cloud bị sửa dữ liệu.
- Phát hiện cloud bị mất file.
- Tự động chuyển sang cloud dự phòng khi download.
- Chống Replay Attack bằng Request ID và Timestamp.
- Ghi log các hoạt động của hệ thống.
- Kiểm thử tự động bằng Pytest.

---

## Công nghệ sử dụng

- Python
- Flask
- Cryptography
- AES-256-GCM
- SHA-256
- Pytest
- HTML
- CSS

---

## Cấu trúc project

```text
BTL ATBM/
├── static/
│   └── style.css
├── templates/
│   └── index.html
├── tests/
│   └── test.py
├── app.py
├── secure_replication.py
├── requirements.txt
└── README.md
```

Khi chương trình chạy lần đầu, hệ thống tự động tạo thêm:

```text
storage/
├── cloud_a/
├── cloud_b/
└── cloud_c/

instance/
├── documents.json
├── used_requests.json
└── master.key

logs/
└── system.log
```

Người dùng không cần tự tạo các thư mục trên.

---

# Hướng dẫn cài đặt và chạy chương trình

## 1. Yêu cầu

Máy tính cần cài đặt:

- Python 3
- Visual Studio Code hoặc trình soạn thảo tương tự

Kiểm tra Python bằng lệnh:

```bash
python --version
```

---

## 2. Tải source code

Có thể tải project bằng một trong hai cách.

### Cách 1: Clone bằng Git

```bash
git clone <đường-dẫn-repository>
```

Sau đó di chuyển vào thư mục project:

```bash
cd "BTL ATBM"
```

### Cách 2: Download ZIP

Chọn:

```text
Code → Download ZIP
```

Sau đó giải nén và mở thư mục project bằng Visual Studio Code.

---

## 3. Tạo môi trường ảo

Mở Terminal tại thư mục project và chạy:

```bash
python -m venv .venv
```

Kích hoạt môi trường ảo.

### PowerShell

```powershell
.\.venv\Scripts\Activate.ps1
```

### Command Prompt

```cmd
.venv\Scripts\activate
```

Khi kích hoạt thành công, đầu dòng Terminal sẽ xuất hiện:

```text
(.venv)
```

---

## 4. Cài đặt thư viện

Chạy lệnh:

```bash
python -m pip install -r requirements.txt
```

Các thư viện chính được cài đặt gồm:

```text
Flask
cryptography
pytest
```

---

## 5. Chạy chương trình

Chạy:

```bash
python app.py
```

Nếu thành công, Terminal hiển thị địa chỉ:

```text
http://127.0.0.1:5000
```

Mở trình duyệt và truy cập:

```text
http://127.0.0.1:5000
```

Để dừng chương trình, nhấn:

```text
Ctrl + C
```

---

# Hướng dẫn sử dụng

## 1. Upload tài liệu

Tại giao diện chính:

1. Nhấn **Chọn tệp**.
2. Chọn tài liệu cần upload.
3. Nhấn nút **Mã hóa và tải lên 3 cloud**.

Hệ thống sẽ:

```text
Đọc tài liệu
      ↓
Kiểm tra yêu cầu
      ↓
Mã hóa bằng AES-256-GCM
      ↓
Tính SHA-256
      ↓
Tạo metadata
      ↓
Lưu lên Cloud A, Cloud B và Cloud C
```

Kích thước file tối đa là **10 MB**.

Sau khi upload thành công, mỗi cloud lưu:

```text
file_id.bin
file_id.json
```

Trong đó:

- `.bin` chứa dữ liệu đã mã hóa.
- `.json` chứa metadata của bản sao.

---

## 2. Kiểm tra trạng thái các cloud

Mỗi bản sao có thể có một trong ba trạng thái:

| Trạng thái | Ý nghĩa |
|---|---|
| HEALTHY | Bản sao an toàn và hợp lệ |
| CORRUPTED | Bản sao đã bị sửa đổi |
| MISSING | Bản sao hoặc metadata bị mất |

Nhấn **Kiểm tra toàn vẹn** để hệ thống kiểm tra và so sánh dữ liệu giữa ba cloud.

Hệ thống sử dụng:

- SHA-256 để kiểm tra dữ liệu mã hóa.
- Authentication Tag để hỗ trợ xác thực dữ liệu.
- Metadata để kiểm tra thông tin của từng bản sao.

---

## 3. Download tài liệu

Người dùng có thể tải tài liệu từ một cloud được ưu tiên.

Hệ thống sẽ kiểm tra cloud đó trước:

```text
Cloud ưu tiên
      ↓
Kiểm tra trạng thái
      ↓
HEALTHY?
  /       \
Có        Không
 ↓           ↓
Giải mã    Bỏ qua
 ↓           ↓
Download   Chuyển sang cloud dự phòng
```

Nếu cloud được ưu tiên bị sửa hoặc mất dữ liệu, hệ thống sẽ tự động chuyển sang một cloud còn bản sao hợp lệ.

Ví dụ:

```text
Cloud A → CORRUPTED
Cloud B → HEALTHY
Cloud C → HEALTHY
```

Nếu ưu tiên tải từ Cloud A, hệ thống sẽ bỏ qua Cloud A và tự động tải từ Cloud B.

---

## 4. Mô phỏng cloud bị sửa dữ liệu

Nhấn chức năng **Mô phỏng sửa** tại một cloud.

Hệ thống sẽ thay đổi một bit trong file mã hóa của cloud đó.

Sau khi kiểm tra lại:

```text
SHA-256 hiện tại ≠ SHA-256 ban đầu
                ↓
           CORRUPTED
```

Bản sao bị sửa sẽ không được sử dụng để download.

---

## 5. Mô phỏng cloud bị mất dữ liệu

Nhấn chức năng **Mô phỏng mất** tại một cloud.

Hệ thống sẽ xóa bản sao mã hóa của cloud đó.

Sau khi kiểm tra lại, trạng thái cloud sẽ là:

```text
MISSING
```

Hệ thống sẽ bỏ qua cloud bị mất dữ liệu và sử dụng cloud dự phòng khi cần download.

---

## 6. Theo dõi log

Các hoạt động của hệ thống được lưu tại:

```text
logs/system.log
```

Một số sự kiện được ghi lại gồm:

- Upload tài liệu.
- Kiểm tra trạng thái cloud.
- So sánh dữ liệu giữa các cloud.
- Phát hiện cloud bị sửa.
- Phát hiện cloud bị mất file.
- Bỏ qua cloud lỗi.
- Download thành công.
- Phát hiện Replay Attack.

Khóa bí mật và nội dung tài liệu gốc không được ghi vào log.

---

# Chạy kiểm thử

Để chạy toàn bộ test, sử dụng lệnh:

```bash
python -m pytest -q
```

Project có các trường hợp kiểm thử chính:

1. Upload tài liệu lên 3 cloud.
2. Kiểm tra metadata riêng của từng cloud.
3. So sánh hash và tag giữa các cloud.
4. Phát hiện cloud bị sửa dữ liệu.
5. Phát hiện cloud bị mất file.
6. Download từ cloud dự phòng.
7. Phát hiện Replay Attack.
8. Từ chối yêu cầu đã hết hạn.
9. Không thể giải mã khi sử dụng sai khóa.
10. Kiểm tra trạng thái cloud được ghi vào log.

Khi kiểm thử thành công, kết quả hiển thị:

```text
9 passed
```

---

# Lưu ý quan trọng

## Không xóa khóa khi vẫn cần dữ liệu cũ

Khóa mã hóa được lưu tại:

```text
instance/master.key
```

Không xóa file này nếu vẫn muốn giải mã các tài liệu đã upload trước đó.

Nếu `master.key` bị xóa, hệ thống sẽ tạo khóa mới trong lần chạy tiếp theo và các tài liệu cũ sẽ không thể giải mã bằng khóa mới.

---

## Xóa môi trường ảo

Thư mục:

```text
.venv
```

chỉ chứa môi trường Python và các thư viện đã cài đặt.

Có thể xóa `.venv` sau khi dừng chương trình. Việc này không xóa dữ liệu tài liệu của hệ thống.

Khi muốn chạy lại, tạo môi trường mới:

```bash
python -m venv .venv
```

Sau đó cài lại thư viện:

```bash
python -m pip install -r requirements.txt
```

---

## Đặt lại toàn bộ dữ liệu hệ thống

Để đưa chương trình về trạng thái ban đầu, dừng chương trình bằng:

```text
Ctrl + C
```

Sau đó xóa các thư mục:

```text
storage/
instance/
logs/
```

Khi chạy lại:

```bash
python app.py
```

hệ thống sẽ tự động tạo lại thư mục lưu trữ và sinh một khóa AES-256 mới.

---

# Thực hiện

| STT | Họ và tên |
|---|---|
| 1 | Nguyễn Tuấn Hùng |

---

## Giảng viên hướng dẫn

**Thầy Trần Hải Anh**

---

## Học phần

**Nhập môn An toàn bảo mật thông tin**

**Trường Đại học Đại Nam – 2026**
**Nhập môn An toàn bảo mật thông tin**

**Trường Đại học Đại Nam – 2026**
