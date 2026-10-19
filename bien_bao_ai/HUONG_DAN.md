# Biển Báo AI – ứng dụng Android nhận diện biển báo giao thông

Ứng dụng chạy mô hình CNN của nhóm (code `traffic_sign.py`, 897.611 tham số, huấn luyện lại trên Colab) **ngay trên điện thoại**, không cần Internet.

## Chức năng
| Màn hình | Nội dung |
|---|---|
| Trang chủ | Chụp ảnh / chọn ảnh, tra cứu, ôn tập, lịch sử; báo trạng thái chạy ngoại tuyến |
| Khung ngắm | Kéo, phóng to để biển báo nằm gọn trong khung vuông → chỉ vùng trong khung được nhận diện |
| Kết quả | Tên biển, mã hiệu QCVN 41:2024/BGTVT, nhóm biển, ý nghĩa, độ tin cậy, 3 khả năng cao nhất, **thời gian xử lý (ms)**. Dưới ngưỡng 60% → báo “chưa chắc chắn” |
| Tra cứu | 43 biển, tìm theo tên/mã, lọc theo nhóm |
| Ôn tập | 10 câu trắc nghiệm ngẫu nhiên |
| Lịch sử | Các lần nhận diện + **thời gian chạy mô hình trung bình** (dùng để điền Bảng 14, 15 của báo cáo) |

Tiền xử lý giống hệt code huấn luyện `traffic_sign.py`: ảnh RGB → 30×30, giữ giá trị điểm ảnh 0–255.

---

## Bước 0 – Chuẩn bị file mô hình
Chép file `model.tflite` (tải về từ notebook `CNN_bien_bao_traffic_sign.ipynb`, ô 9) vào thư mục `assets/`.

## Cách 1 – Build APK trên GitHub (không cần cài gì trên máy) ✅ khuyên dùng
1. Tạo tài khoản tại github.com (nếu chưa có) → bấm **New repository** → đặt tên `bien-bao-ai` → **Create**.
2. Trong repo vừa tạo, bấm **uploading an existing file** → kéo thả **toàn bộ nội dung** thư mục `bien_bao_ai`
   (gồm cả thư mục ẩn `.github` – nếu trình duyệt không kéo được thư mục ẩn, xem lưu ý bên dưới) → **Commit changes**.
3. Vào tab **Actions** → chọn **Build APK** → **Run workflow**. Chờ khoảng 8–12 phút.
4. Khi có dấu ✅, bấm vào lần chạy → mục **Artifacts** → tải **BienBaoAI-apk** (file .zip chứa `app-release.apk`).
5. Chép APK sang điện thoại Android → mở để cài (cho phép “Cài ứng dụng không rõ nguồn gốc”).

> **Lưu ý thư mục `.github`:** nếu không tải lên được, trong repo bấm **Add file → Create new file**, gõ tên
> `.github/workflows/build-apk.yml`, dán nội dung file cùng tên trong gói này, rồi Commit.
>
> Nếu bước build báo lỗi (dấu ❌): mở lần chạy, bấm vào bước bị đỏ, chụp màn hình/copy phần lỗi gửi lại để sửa.

## Cách 2 – Build trên máy tính
1. Cài **Flutter 3.24.5** và **Android Studio** (có Android SDK), JDK 17.
2. Mở terminal trong thư mục `bien_bao_ai`:
   ```
   flutter create --platforms=android --project-name bien_bao_ai --org vn.khkt .
   bash ci/patch_android.sh        (Windows: chạy trong Git Bash)
   flutter pub get
   flutter build apk --release
   ```
3. APK nằm tại `build/app/outputs/flutter-apk/app-release.apk`. Cắm điện thoại và chạy `flutter run` để thử trực tiếp.

## Đo số liệu cho báo cáo (Bảng 14, 15)
- Mỗi điều kiện (đủ sáng, râm, chiều tối, nghiêng ~30°, xa 10–15 m, ảnh thư viện) chụp ~20 lần, ghi số lần đúng.
- Thời gian xử lý hiện ngay ở màn hình kết quả; màn hình **Lịch sử** tính sẵn thời gian trung bình
  (xóa lịch sử trước mỗi đợt đo để có số liệu riêng cho từng điều kiện/từng điện thoại).
- Bật **chế độ máy bay** khi thử để chứng minh chạy ngoại tuyến; chụp màn hình làm minh chứng.

## Lưu ý về dữ liệu biển báo (`assets/signs.json`)
- Mã hiệu ghi theo QCVN 41:2024/BGTVT. Các mã có dấu `*` (R.301*, DP.133*) cần nhóm đối chiếu lại
  với văn bản quy chuẩn và sửa trực tiếp trong file JSON.
- Lớp 30 “Đường có băng tuyết” là biển của Đức, không có trong hệ thống biển Việt Nam.
- Ảnh minh họa trong `assets/signs/` lấy từ bộ dữ liệu GTSRB.
