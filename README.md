# 🚗 Đếm Số Lượng Xe Giao Thông Bằng YOLO và ByteTrack

## Giới thiệu

Dự án xây dựng hệ thống tự động nhận diện, theo dõi và đếm số lượng phương tiện giao thông từ dữ liệu video. Hệ thống tập trung phân loại và đếm 4 nhóm đối tượng chính:

* **Car** (Ô tô con)
* **Bus** (Xe buýt)
* **Truck** (Xe tải)
* **Motorcycle** (Xe máy)

Mô hình sử dụng thuật toán nhận diện **YOLO11x** kết hợp cùng thuật toán theo dõi **ByteTrack** thông qua thư viện Supervision để bám sát quỹ đạo di chuyển của từng phương tiện và đếm khi chúng vượt qua vạch quy định.

Mục tiêu của dự án là tìm hiểu và thực hành quy trình xây dựng một bài toán **Object Tracking & Counting** cơ bản trong lĩnh vực Thị giác máy tính (Computer Vision).

## Mục tiêu

* Tìm hiểu quy trình xử lý dữ liệu video và trích xuất từng khung hình (frame).
* Áp dụng mô hình YOLO11x để nhận diện đối tượng (Object Detection).
* Sử dụng thuật toán ByteTrack để gán ID và theo dõi phương tiện (Object Tracking).
* Thiết lập logic đếm đối tượng khi đi qua vạch đếm (Line Zone Counting).
* Vẽ Bounding Box, hiển thị nhãn, quỹ đạo di chuyển và kết quả thống kê lên video đầu ra.

## Dữ liệu đầu vào

Dự án sử dụng dữ liệu là các đoạn video giao thông định dạng `.mp4`.

## Công nghệ sử dụng

* Python
* Ultralytics (YOLOv11)
* Supervision (`supervision[assets]==0.24.0`)
* OpenCV (`cv2`)
* NumPy
* Matplotlib
* Google Colab (GPU T4)

## Pipeline

**Plaintext**

```
Input Video
    │
    ▼
YOLO Object Detection (yolo11x)
    │
    ▼
Class Filtering (Car, Bus, Truck, Motorcycle)
    │
    ▼
ByteTrack (Object Tracking & ID Assignment)
    │
    ▼
Line Zone (Crossing Detection & Counting)
    │
    ▼
Annotation (BBox, Labels, Traces, Text)
    │
    ▼
Output Video (result.mp4)
```

## Cấu trúc dự án

*Vì dự án được phát triển trên môi trường Google Colab, cấu trúc tệp tập trung vào notebook chính:*

**Plaintext**

```
vehicle-counting/
│
├── obj_counting.ipynb      # Notebook chứa toàn bộ mã nguồn xử lý
├── data/
│   └── test_video.mp4      # Video giao thông đầu vào (cần upload)
│
├── result.mp4              # Video kết quả đầu ra sau khi xử lý
└── README.md
```

## Thuật toán và Logic Xử lý

* **Nhận diện (Detection):** Mô hình `yolo11x.pt` được sử dụng để phát hiện các đối tượng trong từng khung hình. Lớp đối tượng được lọc qua tập `SELECTED_CLASS_IDS`.
* **Theo dõi (Tracking):** ByteTrack được cấu hình với `track_activation_threshold=0.25` và `minimum_matching_threshold=0.8` để duy trì ID liên tục cho phương tiện.
* **Đếm (Counting):** Cấu hình một vạch kẻ ngang với tọa độ (`LINE_START`, `LINE_END`). Khi tâm (`cy`) của Bounding Box cắt qua tọa độ `line_y` theo chiều vào (in) hoặc ra (out), hệ thống sẽ cập nhật số lượng đếm vào `class_counts`.

## Cài đặt và Chạy chương trình

Môi trường khuyên dùng: **Google Colab** để tận dụng GPU.

**Bước 1:** Cài đặt các thư viện cần thiết:

**Bash**

```
pip install ultralytics==8.3.19
pip install supervision[assets]==0.24.0
```

**Bước 2:** Chạy tuần tự các block code trong file `obj_counting.ipynb`.

**Bước 3:** Ở block tải video đầu vào, hệ thống sẽ yêu cầu bạn upload file video từ máy tính:

**Python**

```
from google.colab import files
uploaded = files.upload()
```

**Bước 4:** Block xử lý chính sẽ duyệt qua từng frame, áp dụng nhận diện, theo dõi và lưu kết quả vào file `result.mp4`.

**Bước 5:** Tải video kết quả về máy:

**Python**

```
from google.colab import files
files.download('result.mp4')
```

## Kết quả

Video đầu ra (`result.mp4`) sẽ bao gồm:

* **Bounding Box** bao quanh từng phương tiện.
* **Label** hiển thị ID theo dõi, Tên loại xe và Độ tin cậy (Confidence score).
* **Trace** (vệt quỹ đạo) dài 50 khung hình phía sau mỗi xe di chuyển.
* **Bảng thống kê** ở góc trên bên phải hiển thị tổng số xe và số lượng chi tiết từng loại (Car, Bus, Truck, Motorcycle).
* **Vạch kẻ đỏ** biểu thị ranh giới đếm xe.

## Hướng phát triển

* Tính toán vận tốc di chuyển ước lượng của từng phương tiện.
* Phân tích mật độ giao thông và vẽ biểu đồ nhiệt (Heatmap).
* Cảnh báo phương tiện đi sai làn đường hoặc đi ngược chiều.
* Tích hợp giao diện WebUI (Gradio hoặc Streamlit) để người dùng dễ dàng tải video lên và xem trực tiếp.

## Tài liệu tham khảo

* Ultralytics YOLO Documentation
* Roboflow Supervision Documentation

## Tác giả

**Trần Hải Nam**

Sinh viên ngành Robot và Trí tuệ nhân tạo

Trường Đại học Giao thông vận tải Hà Nội
