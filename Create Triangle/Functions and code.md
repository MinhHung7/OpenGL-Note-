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

