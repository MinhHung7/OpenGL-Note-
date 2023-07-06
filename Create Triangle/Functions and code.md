# Guide to create triangles on OpenGL Window 
## Graphics Pineline
![](https://github.com/MinhHung7/OpenGL/blob/main/Create%20Triangle/Screenshot%202023-07-06%20110143.png)
## Buiding code
```cpp
// ĐỊnh nghĩa mã nguồn cho vertexShaderSource (xác định tọa độ đỉnh)
const char* vertexShaderSource = "#version 330 core\n"
"layout (location = 0) in vec3 aPos;\n"
"void main()\n"
"{\n"
"	gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
"}\0";
```
