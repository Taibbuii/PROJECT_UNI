# Nhập môn xử lý ảnh số - Lab_05
## Phân vùng ảnh
* Sinh viên thực hiện: Bùi Hữu Tài MSSV: 207CT58588
* Môn học: Nhập môn Xử lý ảnh số
* Giảng viên: Đỗ Hữu Quân
## Giơi thiệu
Bài lab tập trung vào các kỹ thuật gán nhãn ảnh, phân vùng ảnh theo vùng, và thay đổi ảnh, bao gồm:
- Gán nhãn để phân biệt các đối tượng trong ảnh.
- Dò tìm cạnh, hình dạng, và xác định góc của đối tượng.
- Tìm điểm tương đồng giữa hai ảnh bằng các kỹ thuật như Harris Corner Detector và Hough Transform.
- Ứng dụng trong nhận diện đối tượng, phân tích ảnh y khoa, và xử lý ảnh vệ tinh.
## Công nghệ sử dụng
- Python: Ngôn ngữ lập trình chính để xử lý ảnh.
- Pillow (PIL): Hỗ trợ đọc, chuyển đổi và lưu ảnh.
- NumPy: Xử lý ảnh dưới dạng mảng số học.
- OpenCV: Thư viện xử lý ảnh mạnh mẽ, hỗ trợ phát hiện đặc trưng và xử lý ảnh.
- SciPy: Hỗ trợ các phép toán hình thái học và xử lý ảnh.
- Matplotlib: Hiển thị ảnh và trực quan hóa kết quả.
## Các phương pháp xử lý ảnh
### 1. Gán nhãn ảnh
- Mục đích: Phân vùng và gắn nhãn các đối tượng trong ảnh dựa trên các đặc điểm như cường độ hoặc kết nối pixel.
- Phương pháp: Sử dụng thuật toán Connected Component Labeling để gán nhãn cho các vùng liên kết.
- Ví dụ: Một ảnh nhị phân có các vùng trắng (đối tượng) trên nền đen. Mỗi vùng trắng được gán một nhãn duy nhất (ví dụ: 1, 2, 3).
- Code:
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('image1.png', 0)
_, binary_img = cv2.threshold(img, 127, 255, cv2.THRESH_BINARY)
num_labels, labels = cv2.connectedComponents(binary_img)

plt.imshow(labels, cmap='jet')
plt.title('Labeled Image')
plt.show()
```
### 2. Phân vùng ảnh theo Region
- Mục đích: Nhóm các pixel có đặc điểm giống nhau thành vùng.
- Phương pháp: Sử dụng Region Growing hoặc Watershed để phân vùng dựa trên đặc điểm cường độ hoặc gradient.
- Ví dụ: Phân vùng ảnh y khoa để tách mô bệnh lý khỏi mô lành.
- Code:
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('image2.png', 0)
markers = np.zeros_like(img)
markers[img < 100] = 1
markers[img > 200] = 2

markers = cv2.watershed(cv2.cvtColor(img, cv2.COLOR_GRAY2BGR), markers)

plt.imshow(markers, cmap='jet')
plt.title('Watershed Segmentation')
plt.show()
```
### 3. Dò tìm cạnh
#### 3.1. Dò tìm cạnh theo chiều dọc
- Mục đích: Phát hiện các cạnh dọc trong ảnh.
- Phương pháp: Sử dụng kernel tùy chỉnh để phát hiện sự thay đổi cường độ theo chiều dọc.
- Code:
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('image3.png', 0)
kernel = np.array([[-1, 0, 1],
                   [-1, 0, 1],
                   [-1, 0, 1]])
vertical_edges = cv2.filter2D(img, -1, kernel)

plt.imshow(vertical_edges, cmap='gray')
plt.title('Vertical Edge Detection')
plt.show()
```
#### 3.2. Dò tìm cạnh với Sobel Filter
- Mục đích: Phát hiện cạnh bằng Sobel Filter, sử dụng kernel để tính gradient theo hướng ngang và dọc.
- Công thức toán học:
+ Gradient theo x: $ G_x = \begin{bmatrix} -1 & 0 & 1 \ -2 & 0 & 2 \ -1 & 0 & 1 \end{bmatrix} * I $
+ Gradient theo y: $ G_y = \begin{bmatrix} -1 & -2 & -1 \ 0 & 0 & 0 \ 1 & 2 & 1 \end{bmatrix} * I $
+ Độ lớn gradient: $ G = \sqrt{G_x^2 + G_y^2} $
- Code:
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('image4.png', 0)
sobel_x = cv2.Sobel(img, cv2.CV_64F, 1, 0, ksize=3)
sobel_y = cv2.Sobel(img, cv2.CV_64F, 0, 1, ksize=3)
sobel_combined = np.sqrt(sobel_x**2 + sobel_y**2)

plt.imshow(sobel_combined, cmap='gray')
plt.title('Sobel Edge Detection')
plt.show()
```
### 4. Xác định góc của đối tượng
- Mục đích: Xác định hướng của đối tượng dựa trên gradient.
- Phương pháp: Tính góc của gradient bằng $ \theta = \arctan(G_y / G_x) $.
- Code:
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('image5.png', 0)
sobel_x = cv2.Sobel(img, cv2.CV_64F, 1, 0, ksize=3)
sobel_y = cv2.Sobel(img, cv2.CV_64F, 0, 1, ksize=3)
angle = np.arctan2(sobel_y, sobel_x) * 180 / np.pi

plt.imshow(angle, cmap='hsv')
plt.title('Object Orientation')
plt.show()
```
### 5. Dò tìm hình dạng với Hough Transform
#### 5.1. Dò tìm đường thẳng
- Mục đích: Phát hiện các đường thẳng trong ảnh.
- Phương pháp: Sử dụng Hough Transform để chuyển đổi không gian ảnh sang không gian Hough.
- Code:
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('image6.png', 0)
edges = cv2.Canny(img, 100, 200)
lines = cv2.HoughLines(edges, 1, np.pi / 180, 150)
for rho, theta in lines[0]:
    a = np.cos(theta)
    b = np.sin(theta)
    x0 = a * rho
    y0 = b * rho
    x1 = int(x0 + 1000 * (-b))
    y1 = int(y0 + 1000 * (a))
    x2 = int(x0 - 1000 * (-b))
    y2 = int(y0 - 1000 * (a))
    cv2.line(img, (x1, y1), (x2, y2), (0, 255, 0), 2)

plt.imshow(img, cmap='gray')
plt.title('Hough Line Detection')
plt.show()
```
#### 5.2. Dò tìm đường tròn
- Mục đích: Phát hiện các đường tròn trong ảnh.
- Code:
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('image7.png', 0)
circles = cv2.HoughCircles(img, cv2.HOUGH_GRADIENT, 1, 20,
                           param1=50, param2=30, minRadius=0, maxRadius=0)
if circles is not None:
    circles = np.uint16(np.around(circles))
    for i in circles[0, :]:
        cv2.circle(img, (i[0], i[1]), i[2], (0, 255, 0), 2)

plt.imshow(img, cmap='gray')
plt.title('Hough Circle Detection')
plt.show()
```
### 6. Image Matching
- Mục đích: Tìm điểm tương đồng giữa hai ảnh.
- Phương pháp:
+ Sử dụng Harris Corner Detector để tìm các điểm đặc trưng.
+ Tính mô tả đặc trưng cục bộ (SIFT hoặc ORB).
+ So khớp các điểm đặc trưng giữa hai ảnh.
- Code:
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img1 = cv2.imread('image8.png', 0)
img2 = cv2.imread('image9.png', 0)
orb = cv2.ORB_create()
kp1, des1 = orb.detectAndCompute(img1, None)
kp2, des2 = orb.detectAndCompute(img2, None)
bf = cv2.BFMatcher(cv2.NORM_HAMMING, crossCheck=True)
matches = bf.match(des1, des2)
matches = sorted(matches, key=lambda x: x.distance)
img_matches = cv2.drawMatches(img1, kp1, img2, kp2, matches[:10], None, flags=2)

plt.imshow(img_matches)
plt.title('Image Matching')
plt.show()
```
## Cấu trúc file
```
├── main.ipynb        # Code chính
├── image1.png        # Ảnh cho gán nhãn
├── image2.png        # Ảnh cho phân vùng vùng
├── image3.png        # Ảnh cho dò cạnh dọc
├── image4.png        # Ảnh cho Sobel
├── image5.png        # Ảnh cho xác định góc
├── image6.png        # Ảnh cho Hough Line
├── image7.png        # Ảnh cho Hough Circle
├── image8.png        # Ảnh 1 cho image matching
├── image9.png        # Ảnh 2 cho image matching
├── README.md         # Hướng dẫn
```