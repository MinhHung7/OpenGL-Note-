# Guide to create triangles on OpenGL Window 
## Graphics Pineline
![](https://github.com/MinhHung7/OpenGL/blob/main/Create%20Triangle/Screenshot%202023-07-06%20110143.png)
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
