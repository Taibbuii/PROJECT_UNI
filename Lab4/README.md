# Nhập môn xử lý ảnh số - Lab_04
## Phân vùng ảnh
* Sinh viên thực hiện: Bùi Hữu Tài MSSV: 207CT58588
* Môn học: Nhập môn Xử lý ảnh số
* Giảng viên: Đỗ Hữu Quân
## Giơi thiệu
Bài lab tập trung vào phân vùng ảnh, chia ảnh thành các vùng có đặc điểm chung để:
- Tách đối tượng khỏi nền.
- Làm nổi bật vùng quan tâm.
- Ứng dụng trong ảnh y khoa, ảnh vệ tinh, nhận diện đối tượng.
## Công nghệ sử dụng
- Python: Ngôn ngữ chính
- Pillow (PIL): Đọc, chuyển đổi, và lưu ảnh
- NumPy: Xử lý ảnh dưới dạng mảng số học
- OpenCV: Xử lý ảnh
- ImageIO: Đọc file ảnh với định dạng hiện đại
- SciPy: Hỗ trợ toán hình thái học
- Matplotlib: Hiển thị ảnh trực quan
## Các phương pháp phân vùng
### 1. Phân vùng theo Histogram
- Mục đích: Chia ảnh thành vùng sáng/tối dựa trên histogram.
#### 1.1. Phương pháp Otsu
- Tự động tìm ngưỡng tối ưu để tách ảnh thành hai lớp (foreground/background) bằng cách tối ưu hóa phương sai giữa các lớp.
- Công thức toán học:
$ \sigma^2_b(T) = w_0 w_1 (\mu_0 - \mu_1)^2 $
* Trong đó:
- \( w_0, w_1 \): Tỷ lệ pixel của hai lớp (foreground và background)  
- \( \mu_0, \mu_1 \): Giá trị trung bình cường độ của hai lớp
- Ví dụ:
+ Ảnh có vùng sáng (cường độ 200) và tối (cường độ 50). Otsu tìm $ T \approx 125 $, tách vùng sáng (>125) và tối (≤125).
- Code:
```python
import cv2
img = cv2.imread('dalat.jpg', 0)
_, otsu_img = cv2.threshold(img, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
```
#### 1.2. Adaptive Thresholding
- Chia ảnh thành các vùng nhỏ, tính ngưỡng riêng cho từng vùng, phù hợp với ảnh có ánh sáng không đồng đều.
- Công thức toán học:
$ Ngưỡng cục bộ: T(x, y) = \text{mean}(N(x, y)) - \text{offset} $
* Trong đó:
- N(x, y) $: Vùng lân cận pixel (x, y).
- \text{offset} $: Hằng số (thường 10).
- Ví dụ:
+ Ảnh có vùng sáng (cường độ 200) và vùng tối (cường độ 50) do bóng. Ngưỡng cục bộ tại vùng tối có thể là 60, tại vùng sáng là 180, tách chính xác hơn Otsu.
- Code:
```python
import cv2
img = cv2.imread('dalat.jpg', 0)
_, otsu_img = cv2.threshold(img, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
```