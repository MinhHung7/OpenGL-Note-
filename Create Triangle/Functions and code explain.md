# Guide to create triangles on OpenGL Window 
## Graphics Pineline
![](https://github.com/MinhHung7/OpenGL/blob/main/Create%20Triangle/Screenshot%202023-07-06%20110143.png)
## Giải thích về Shape Assembly và Geometry Shader
**Shape Assembly:**
Shape Assembly (tạm dịch: Lắp ráp hình dạng): Shape Assembly là một công nghệ trong đồ họa máy tính dùng để xây dựng hình dạng phức tạp bằng cách kết hợp các hình dạng đơn giản. Các hình dạng đơn giản được gọi là "shape primitives" (hình dạng nguyên thủy), chẳng hạn như các khối hình hộp, hình cầu, hình trụ, vv. Bằng cách lắp ráp các hình dạng này lại với nhau, ta có thể tạo ra hình dạng phức tạp hơn.<br>
**Geometry Shader:**
Geometry Shader (tạm dịch: Đa hình hóa): Geometry Shader là một giai đoạn trong quá trình xử lý đồ họa máy tính, nằm trong pipeline của GPU (Đơn vị xử lý đồ họa). Geometry Shader nhận đầu vào là các hình dạng hình học (geometry) từ giai đoạn trước đó và có khả năng biến đổi chúng để tạo ra các hình dạng mới. Điều này cho phép chúng ta thực hiện các phép biến đổi không gian, như thu nhỏ, phóng to, xoay, đẩy, và còn nhiều hiệu ứng hình dạng phức tạp khác.
## Buiding code
```cpp
// Định nghĩa mã nguồn cho vertexShaderSource (xác định tọa độ đỉnh)
const char* vertexShaderSource = "#version 330 core\n"  // Phiên bản 3.3
"layout (location = 0) in vec3 aPos;\n"
"void main()\n"
"{\n"
"	gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
"}\0";
```
VertexShaderSource là mã nguồn của vertex shader. Trong shader này, các điểm đầu vào được khai báo thông qua layout (location = 0) in vec3 aPos, nghĩa là vertex shader sẽ nhận vào một vector ba chiều aPos với vị trí đầu vào là 0. Trong hàm main() của vertex shader, gl_Position được thiết lập để lưu trữ vị trí của các đỉnh, trong trường hợp này là (aPos.x, aPos.y, aPos.z, 1.0).
```cpp
// Định nghĩa mã nguồn cho fragmentShaderSource
const char* fragmentShaderSource = "#version 330 core\n"
"out vec4 FragColor;\n"
"void main()\n"
"{\n"
"	FragColor = vec4(0.8f, 0.3f, 0.02f, 1.0f);\n"
"}\n\0";
```
FragmentShaderSource là mã nguồn của fragment shader. Trong shader này, FragColor được khai báo là một biến out để định nghĩa màu sắc đầu ra của fragment shader. Trong hàm main() của fragment shader, FragColor được thiết lập là một vector bốn chiều vec4(0.8f, 0.3f, 0.02f, 1.0f) đại diện cho màu sắc của fragment, với giá trị RGB là (0.8f, 0.3f, 0.02f) và giá trị alpha là 1.0f.
```cpp
GLfloat vertices[] =
{
  -0.5f, -0.5f * float (sqrt (3)) / 3, 0.0f,
  0.5f, -0.5f * float (sqrt (3)) / 3, 0.0f,
  0.0f, 0.5f * float (sqrt (3)) * 2 / 3, 0.0f,
};
```
Đặt tọa độ các đỉnh trong không gian 3 chiều, do đang vẽ trên không gian 2D nên z = 0.0f
```cpp
GLuint vertexShader = glCreateShader (GL_VERTEX_SHADER);
glShaderSource (vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader (vertexShader);
```
Đoạn mã này được sử dụng để tạo, nạp mã nguồn và biên dịch một shader vertex trong OpenGL.

<br>glCreateShader(GL_VERTEX_SHADER) tạo một đối tượng shader mới và trả về một giá trị đại diện cho shader đó. Trong trường hợp này, shader được tạo là một shader vertex, được xác định bởi hằng số GL_VERTEX_SHADER.

<br>glShaderSource(vertexShader, 1, &vertexShaderSource, NULL) nạp mã nguồn của shader vertex vào đối tượng shader đã được tạo. Tham số đầu tiên là đối tượng shader, tham số thứ hai là số lượng chuỗi mã nguồn, tham số thứ ba là mảng chứa các con trỏ đến các chuỗi mã nguồn và tham số thứ tư là độ dài của mỗi chuỗi. Trong trường hợp này, bạn chỉ truyền một chuỗi mã nguồn vertexShaderSource và chiều dài của nó được xác định bằng cách truyền 1 và NULL cho tham số cuối cùng.

<br>glCompileShader(vertexShader) biên dịch shader vertex đã được tạo và nạp mã nguồn vào đối tượng shader đó. Sau khi shader được biên dịch, OpenGL sẽ kiểm tra mã nguồn và tạo các tệp nhị phân tương ứng cho GPU để sử dụng trong quá trình xử lý đồ họa.
```cpp
GLuint fragmentShader = glCreateShader (GL_FRAGMENT_SHADER);
glShaderSource (fragmentShader, 1, &fragmentShaderSource, NULL);
glCompileShader (fragmentShader);
```
```cpp
GLuint shaderProgram = glCreateProgram ();

glAttachShader (shaderProgram, vertexShader);
glAttachShader (shaderProgram, fragmentShader);
glLinkProgram (shaderProgram);

glDeleteShader (vertexShader);
glDeleteShader (fragmentShader);
```
glCreateProgram() tạo một đối tượng chương trình shader mới và trả về một giá trị đại diện cho chương trình shader đó.

<br>Chương trình shader trong OpenGL là sự kết hợp của các shader khác nhau, bao gồm ít nhất một shader vertex và một shader fragment. Chương trình shader được sử dụng để quản lý và liên kết các shader thành một đơn vị hoàn chỉnh để sử dụng trong quá trình xử lý đồ họa.

<br>glAttachShader(shaderProgram, vertexShader) và glAttachShader(shaderProgram, fragmentShader) được sử dụng để liên kết shader vào chương trình shader đã được tạo. Tham số đầu tiên là đối tượng chương trình shader, tham số thứ hai là đối tượng shader (vertex shader hoặc fragment shader) mà bạn muốn liên kết vào chương trình shader. Trong trường hợp này, bạn liên kết shader vertex (vertexShader) và shader fragment (fragmentShader) vào chương trình shader (shaderProgram).

<br>glLinkProgram(shaderProgram) được sử dụng để liên kết chương trình shader sau khi đã liên kết các shader thành công. Việc liên kết chương trình shader là quá trình tạo kết nối giữa các shader và tạo các liên kết tới các biến thuộc tính (attributes) và biến đồng nhất (uniforms). Sau khi chương trình shader được liên kết thành công, nó sẽ sẵn sàng để sử dụng trong quá trình xử lý đồ họa.

<br>glDeleteShader(vertexShader) và glDeleteShader(fragmentShader) được sử dụng để xóa các đối tượng shader sau khi chúng đã được liên kết vào chương trình shader. Sau khi liên kết thành công, chúng ta không cần đối tượng shader riêng lẻ nữa, do đó có thể xóa chúng để giải phóng tài nguyên.
```cpp
GLuint VAO, VBO;

glGenVertexArrays (1, &VAO);
glGenBuffers (1, &VBO);

glBindVertexArray (VAO);

glBindBuffer (GL_ARRAY_BUFFER, VBO);
glBufferData (GL_ARRAY_BUFFER, sizeof (vertices), &vertices, GL_STATIC_DRAW);

glVertexAttribPointer (0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof (float), (void*)0);
glEnableVertexAttribArray (0);

glBindBuffer (GL_ARRAY_BUFFER, 0);
glBindVertexArray (0);
```
Đoạn mã này được sử dụng để tạo, liên kết và cấu hình VAO (Vertex Array Object) và VBO (Vertex Buffer Object) để lưu trữ và truyền dữ liệu đỉnh trong OpenGL.

glGenVertexArrays(1, &VAO) tạo một VAO mới và gán một định danh duy nhất cho nó. Trong trường hợp này, bạn tạo một VAO duy nhất và lưu trữ định danh của nó trong biến VAO.

glGenBuffers(1, &VBO) tạo một VBO mới và gán một định danh duy nhất cho nó. Trong trường hợp này, bạn tạo một VBO duy nhất và lưu trữ định danh của nó trong biến VBO.

glBindVertexArray(VAO) liên kết VAO hiện tại với định danh của VAO đã được tạo. Từ nay trở đi, tất cả các hoạt động trên đỉnh (vertex) sẽ áp dụng cho VAO này.

glBindBuffer(GL_ARRAY_BUFFER, VBO) liên kết VBO hiện tại với định danh của VBO đã được tạo. Từ nay trở đi, tất cả các hoạt động liên quan đến dữ liệu đỉnh (vertex data) sẽ áp dụng cho VBO này.

glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW) sao chép dữ liệu đỉnh từ mảng vertices vào VBO hiện tại. Tham số thứ hai là kích thước của dữ liệu đỉnh, tham số thứ ba là con trỏ đến dữ liệu đỉnh, và tham số thứ tư xác định cách mà dữ liệu sẽ được sử dụng trong quá trình vẽ (GL_STATIC_DRAW trong trường hợp này cho biết dữ liệu đỉnh là tĩnh và không thay đổi sau khi sao chép).

glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0) xác định cấu trúc dữ liệu đỉnh và thiết lập thuộc tính của đỉnh. Tham số đầu tiên là vị trí của thuộc tính trong shader (0 trong trường hợp này), tham số thứ hai là số thành phần của thuộc tính (3 trong trường hợp này xác định tọa độ x, y, z), tham số thứ ba xác định kiểu dữ liệu của thuộc tính (GL_FLOAT trong trường hợp này), tham số thứ tư xác định nếu các giá trị của thuộc tính nên được chuẩn hóa hay không, tham số thứ năm là bước (stride) giữa các thuộc tính trong bộ nhớ, và tham số thứ sáu là vị trí bắt đầu của thuộc tính trong bộ nhớ.

glEnableVertexAttribArray(0) kích hoạt thuộc tính đang được xác định bằng glVertexAttribPointer.

glBindBuffer(GL_ARRAY_BUFFER, 0) gỡ bỏ liên kết VBO hiện tại. Bây giờ không có VBO nào được liên kết với GL_ARRAY_BUFFER.

glBindVertexArray(0) gỡ bỏ liên kết VAO hiện tại. Bây giờ không có VAO nào được liên kết.

Sau khi đoạn mã này được thực thi, bạn đã tạo và cấu hình thành công VAO và VBO để lưu trữ và truyền dữ liệu đỉnh. Chúng sẽ sẵn sàng để sử dụng
```cpp
while (!glfwWindowShouldClose (window))
{
  glClearColor (0.07f, 0.13f, 0.17f, 1.0f);
  glClear (GL_COLOR_BUFFER_BIT);
  glUseProgram (shaderProgram);
  glBindVertexArray (VAO);
  glDrawArrays (GL_TRIANGLES, 0, 3);
  glfwSwapBuffers (window);

  glfwPollEvents ();
}
```
glfwWindowShouldClose(window) kiểm tra xem cửa sổ đã được yêu cầu đóng chưa. Nếu giá trị trả về là true, vòng lặp sẽ dừng lại và chương trình kết thúc.

glClearColor(0.07f, 0.13f, 0.17f, 1.0f) đặt màu nền của framebuffer (bộ đệm màu) bằng màu RGB (0.07, 0.13, 0.17) và giá trị alpha là 1.0. Màu này sẽ được sử dụng để xóa màu nền trong quá trình xóa framebuffer trong lệnh glClear.

glClear(GL_COLOR_BUFFER_BIT) xóa các buffer màu trong framebuffer để chuẩn bị vẽ frame mới. Trong trường hợp này, chỉ buffer màu được xóa, sử dụng giá trị màu nền đã được đặt bởi glClearColor.

glUseProgram(shaderProgram) sử dụng chương trình shader (shaderProgram) đã được liên kết trước đó để thực hiện các phép tính đồ họa tiếp theo.

glBindVertexArray(VAO) liên kết VAO (VAO) đã được tạo và cấu hình trước đó. VAO này chứa thông tin về đỉnh (vertex) và các thuộc tính của đỉnh.

glDrawArrays(GL_TRIANGLES, 0, 3) vẽ các hình tam giác bằng cách sử dụng VAO và shader đã liên kết. Trong trường hợp này, vẽ một tam giác bằng cách sử dụng 3 đỉnh từ VAO.

glfwSwapBuffers(window) hoán đổi vị trí các buffer trong framebuffer, hiển thị frame vừa được vẽ lên cửa sổ.

glfwPollEvents() xử lý các sự kiện đã xảy ra trong cửa sổ, ví dụ như sự kiện bàn phím, chuột, cửa sổ được kéo thả,
```cpp
glDeleteVertexArrays (1, &VAO);
glDeleteBuffers (1, &VBO);
glDeleteProgram (shaderProgram);
```

