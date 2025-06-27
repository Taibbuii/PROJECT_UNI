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
- N(x, y): Vùng lân cận pixel (x, y).
- \text{offset}: Hằng số (thường 10).
- Ví dụ:
+ Ảnh có vùng sáng (cường độ 200) và vùng tối (cường độ 50) do bóng. Ngưỡng cục bộ tại vùng tối có thể là 60, tại vùng sáng là 180, tách chính xác hơn Otsu.
- Code:
```python
from PIL import Image
import numpy as np
from skimage.filters import threshold_local
import matplotlib.pyplot as plt

data = Image.open('dalat.jpg').convert('L')
a = np.array(data)
b = threshold_local(a, block_size=39, offset=10)
b = a > b
plt.imshow(b, cmap='gray')
plt.show()
```
### 2. Phân vùng theo Region
- Mục đích: Nhóm pixel có đặc điểm giống nhau thành vùng.
### 3. Biến đổi đối tượng trong ảnh
#### 3.1. Sử dụng binary_dilation
Mở rộng vùng sáng, thêm pixel ở biên.
- Công thức toán học:
+ $ A \oplus B = \{ z \ | \ (B)_z \cap A \neq \emptyset \} $
+ $ A $: Ảnh nhị phân.
+ $ B $: Phần tử cấu trúc (structuring element).
- Ví dụ:
+ Vùng sáng (hình tròn) được mở rộng thêm 50 pixel ở biên sau 50 lần lặp, làm tròn to hơn.
```python:
from PIL import Image
import numpy as np
import scipy.ndimage as nd
import matplotlib.pyplot as plt

data = Image.open('dil_img.gif').convert('L')
b = nd.binary_dilation(data, iterations=50)
c = Image.fromarray(b)
plt.imshow(c, cmap='gray')
plt.show()
```
#### 3.2. Sử dụng binary_opening
- Mục đích: Loại bỏ nhiễu nhỏ và làm mịn vùng đối tượng bằng cách kết hợp erosion và dilation.
- Nguyên lý: Thực hiện erosion trước, sau đó dilation để khôi phục hình dạng.
- Code chính:
```python:
from PIL import Image
import numpy as np
import scipy.ndimage as nd
import matplotlib.pyplot as plt

data = Image.open('dil_img.gif').convert('L')
b = nd.binary_opening(data, iterations=50)
c = Image.fromarray(b)
plt.imshow(c, cmap='gray')
plt.show()
```
#### 3.3. Sử dụng binary_erosion
- Mục đích: Thu hẹp vùng đối tượng bằng cách loại bỏ pixel ở biên.
- Nguyên lý: Loại bỏ các pixel ở rìa vùng sáng dựa trên phần tử cấu trúc.
- Code chính:
```python:
from PIL import Image
import numpy as np
import scipy.ndimage as nd
import matplotlib.pyplot as plt

data = Image.open('dil_img.gif').convert('L')
structure = [[0, 1, 0], [1, 1, 1], [0, 1, 0]]
b = nd.binary_erosion(data, structure, iterations=50)
c = Image.fromarray(b)
plt.imshow(c, cmap='gray')
plt.show()
```
#### 3.4. Sử dụng binary_closing
- Mục đích: Lấp đầy các lỗ nhỏ trong vùng đối tượng, làm mịn biên.
- Nguyên lý: Thực hiện dilation trước, sau đó erosion.
- Code chính:
```python:
from PIL import Image
import numpy as np
import scipy.ndimage as nd
import matplotlib.pyplot as plt

data = Image.open('dil_img.gif').convert('L')
b = nd.binary_closing(data, iterations=50)
c = Image.fromarray(b)
plt.imshow(c, cmap='gray')
plt.show()
```
## Cấu trúc file:
```
├── main.ipynb        # Code chính
├── fruit.jpg         # Ảnh cho histogram
├── dil_img.gif       # Ảnh cho morphological operations
├── README.md         # Hướng dẫn
```