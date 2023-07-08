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
![](https://github.com/MinhHung7/OpenGL-Note-/blob/main/Index_Buffer/Assets/Index_Buffer.png)
## Implement
```cpp
GLfloat vertices[] =
{
  -0.5f, -0.5f * (float)(sqrt (3)) / 3, 0.0f,
  0.5f, -0.5f * (float)(sqrt (3)) / 3, 0.0f,
  0.0f, 0.5f * (float)(sqrt (3)) * 2 / 3, 0.0f,
  -0.5f / 2, 0.5f * (float)(sqrt (3)) / 6, 0.0f,
  0.5f / 2, 0.5f * (float)(sqrt (3)) / 6, 0.0f,
  0.0f, -0.5f * (float)(sqrt (3)) / 3, 0.0f
};

GLuint indices[] = {
    0, 3, 5,
    3, 2, 4,
    5, 4, 1
};
```
Tạo một mảng lưu tọa độ các đỉnh và một mảng lưu chỉ số các đỉnh
```cpp
GLuint VAO, VBO, EBO;
```
Tạo cấu hình EBO (element buffer object)
```cpp
glGenBuffers (1, &EBO);
```
Tạo một EBO mới và gán một định danh duy nhất cho nó. Trong trường hợp này, bạn tạo một EBO duy nhất và lưu trữ định danh của nó trong biến EBO.
```cpp
glBindBuffer (GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData (GL_ELEMENT_ARRAY_BUFFER, sizeof (indices), indices, GL_STATIC_DRAW);
```
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);: Dòng này liên kết một đối tượng buffer (EBO) với kết hợp vẽ của OpenGL. GL_ELEMENT_ARRAY_BUFFER là một kiểu buffer đặc biệt chỉ được sử dụng cho index buffer. Bằng cách liên kết EBO với GL_ELEMENT_ARRAY_BUFFER, ta cho phép OpenGL biết rằng EBO sẽ được sử dụng làm index buffer trong quá trình vẽ.

glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);: Dòng này sao chép dữ liệu của mảng indices vào đối tượng buffer đã được liên kết (EBO) bằng cách sử dụng hàm glBufferData. Tham số đầu tiên GL_ELEMENT_ARRAY_BUFFER xác định rằng dữ liệu được sao chép sẽ được lưu trữ trong index buffer. Tham số thứ hai sizeof(indices) chỉ định kích thước của dữ liệu index, trong trường hợp này là kích thước của mảng indices. Tham số thứ ba indices là con trỏ đến mảng dữ liệu index. Tham số cuối cùng GL_STATIC_DRAW chỉ ra rằng dữ liệu sẽ không thay đổi sau khi đã được sao chép và sẽ được sử dụng để vẽ một lần (không thay đổi).
```cpp
glBindBuffer (GL_ELEMENT_ARRAY_BUFFER, 0);
```
Gỡ bỏ liên kết EBO hiện tại, lúc này ko có EBO nào được liên kết với GL_ELEMENT_ARRAY_BUFFER
```cpp
while (!glfwWindowShouldClose (window))
{
  glClearColor (0.07f, 0.13f, 0.17f, 1.0f);
  glClear (GL_COLOR_BUFFER_BIT);
  glUseProgram (shaderProgram);
  glBindVertexArray (VAO);
  glDrawElements (GL_TRIANGLES,9, GL_UNSIGNED_INT, 0);
  glfwSwapBuffers (window);

  glfwPollEvents ();
}
```
GL_TRIANGLES: Tham số đầu tiên xác định kiểu hình dạng sẽ được vẽ. Trong trường hợp này, GL_TRIANGLES chỉ ra rằng các đỉnh sẽ được kết hợp thành các tam giác. Mỗi ba chỉ mục trong index buffer sẽ tạo thành một tam giác.

9: Tham số thứ hai là số lượng chỉ mục (indices) sẽ được sử dụng để vẽ. Trong ví dụ này, có tổng cộng 9 chỉ mục sẽ được sử dụng để tạo ra các tam giác.

GL_UNSIGNED_INT: Tham số thứ ba xác định kiểu dữ liệu của các chỉ mục trong index buffer. Trong trường hợp này, dữ liệu chỉ mục được xác định bằng kiểu dữ liệu unsigned int.

0: Tham số cuối cùng chỉ ra vị trí bắt đầu trong index buffer để bắt đầu vẽ. Trong trường hợp này, vị trí bắt đầu là vị trí đầu tiên của index buffer.

Dòng mã glDrawElements(GL_TRIANGLES, 9, GL_UNSIGNED_INT, 0); được sử dụng để vẽ các tam giác bằng cách sử dụng các chỉ mục từ index buffer. Các chỉ mục sẽ được sử dụng để xác định thứ tự các đỉnh trong mảng đỉnh và tạo thành các tam giác. Trong ví dụ này, tổng cộng 9 chỉ mục được sử dụng để tạo ra các tam giác từ mảng đỉnh đã được định nghĩa trước đó.
```cpp
glDeleteBuffers (1, &EBO);
```
