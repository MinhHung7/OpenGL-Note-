# Index Buffer
Index buffer (hay còn gọi là element buffer) là một cấu trúc dữ liệu trong OpenGL (và các framework đồ họa khác) để lưu trữ chỉ mục của các đỉnh trong một mesh. Thay vì lưu trữ các đỉnh theo thứ tự liên tiếp trong bộ đệm đỉnh (vertex buffer), index buffer cho phép tái sử dụng các đỉnh đã tồn tại để xây dựng các đa giác phức tạp hơn.

Cách hoạt động của index buffer như sau: Thay vì lưu trữ toàn bộ dữ liệu của mỗi đỉnh trong mảng đỉnh, index buffer chỉ lưu trữ một danh sách các chỉ mục (index) tương ứng với các đỉnh trong mảng đỉnh. Mỗi chỉ mục tham chiếu đến vị trí của một đỉnh cụ thể trong mảng đỉnh.

Ví dụ, giả sử bạn có một mảng đỉnh với 6 đỉnh (A, B, C, D, E, F) và bạn muốn tạo ra một hình chữ nhật bằng cách kết hợp các đỉnh này. Thay vì lưu trữ 4 đỉnh của hình chữ nhật (ví dụ: A, B, C, D) trong mảng đỉnh, bạn có thể chỉ lưu trữ danh sách các chỉ mục (ví dụ: 0, 1, 2, 3) để chỉ định vị trí của các đỉnh này trong mảng đỉnh. Điều này cho phép bạn sử dụng các đỉnh đã tồn tại (ví dụ: A, B, C, D) nhiều lần để tạo ra các hình dạng phức tạp khác nhau.

Index buffer giúp tiết kiệm bộ nhớ và tăng hiệu suất vẽ, đặc biệt là khi bạn có các đỉnh trùng lặp trong một mesh. Ngoài ra, index buffer cũng hỗ trợ trong việc kết hợp các mesh lại với nhau để tạo thành các hình dạng phức tạp hơn.<br>
**Ví dụ :** <br>
![](https://github.com/MinhHung7/OpenGL-Note-/blob/main/Index_Buffer/Assets/Duplicate%20Vertices.png)
<br>Chúng ta thấy rằng để vẽ được hình trên ta cần lưu trữ đến 9 đỉnh
<br>Trong đó cặp đỉnh (1, 3), (2, 6), (5, 7) lại trùng nhau. Điều này sẽ gây ra việc lãng phí bộ nhớ
<br>Do vậy thực chất chúng ta chỉ cần 6 đỉnh
