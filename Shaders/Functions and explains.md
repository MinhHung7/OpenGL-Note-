# Shaders
## Problem : Draw it
![](https://github.com/MinhHung7/OpenGL-Note-/blob/main/Shaders/Shader.png)
## Start
![](https://github.com/MinhHung7/OpenGL-Note-/blob/main/Shaders/Vertex%20Attribute%20Pointer.png)

**Trong file "default.vert"**
```cpp
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColor;

out vec3 color;

uniform float scale;

void main()
{
   gl_Position = vec4(aPos.x + aPos.x * scale, aPos.y + aPos.y * scale, aPos.z + aPos.z * scale, 1.0);
   color = aColor;
}
```
Trong GLSL, từ khóa const được sử dụng để khai báo một hằng số (constant). Một hằng số không thể thay đổi giá trị sau khi đã được khai báo và được gán một giá trị cụ thể ngay từ đầu.

Trong trường hợp của bạn, nếu bạn sử dụng const float scale; thay vì uniform float scale;, giá trị của biến scale sẽ không thể thay đổi trong suốt quá trình chạy ứng dụng. Điều này có nghĩa rằng bạn sẽ không thể điều khiển hoặc thay đổi giá trị scale từ bên ngoài shader.

Với uniform float scale;, bạn có thể gửi giá trị scale từ ứng dụng vào shader và thay đổi nó khi cần thiết. Điều này cho phép bạn tương tác và điều chỉnh giá trị scale trong quá trình chạy ứng dụng, mà không cần phải sửa đổi mã shader.

Vì vậy, nếu bạn muốn có khả năng điều khiển và thay đổi giá trị scale từ bên ngoài shader, sử dụng uniform float scale; là phù hợp. Nếu giá trị scale là một hằng số không thay đổi, bạn có thể sử dụng const float scale = <giá trị cụ thể>; để khai báo một hằng số và sử dụng nó trong shader.<br>
**Trong file "default.frag**
```cpp
#version 330 core
out vec4 FragColor;

in vec3 color;

void main()
{
   FragColor = vec4(color, 1.0f);
}
```
**Trong file ShaderClass.cpp**
```cpp
void Shader::compiErrors (unsigned int shader, const char* type)
{
	GLint hasCompiled;
	char infoLog[1024];
	if (type != "PROGRAM")
	{
		glGetShaderiv (shader, GL_COMPILE_STATUS, &hasCompiled);
		if (hasCompiled == GL_FALSE)
		{
			glGetShaderInfoLog (shader, 1024, NULL, infoLog);
			std::cout << "SHADER_COMPILATION_ERROR for: " << type << "\n";
		}
	}
	else
	{
		glGetProgramiv (shader, GL_COMPILE_STATUS, &hasCompiled);
		if (hasCompiled == GL_FALSE)
		{
			glGetShaderInfoLog (shader, 1024, NULL, infoLog);
			std::cout << "SHADER_LINKING_ERROR for: " << type << "\n";
		}
	}
}
```
Mục đích báo lỗi<br>
**Trong file VAO.h**
```cpp
void VAO::LinkAttrib (VBO& VBO, GLuint layout, GLuint numComponents, GLenum type, GLsizeiptr stride, void* offset)
{
	VBO.Bind ();
	glVertexAttribPointer (layout, numComponents, type, GL_FALSE, stride, offset);
	glEnableVertexAttribArray (layout);
	VBO.Unbind ();
}
```
- VBO là một tham chiếu đến một đối tượng VBO (Vertex Buffer Object). VBO chứa dữ liệu đỉnh (vị trí, màu sắc, các thuộc tính khác) của mô hình 3D.
- layout là chỉ số của thuộc tính đầu vào trong VAO mà chúng ta muốn liên kết với VBO.
- numComponents là số lượng thành phần dữ liệu trong mỗi đỉnh của thuộc tính (ví dụ: 3 cho vị trí XYZ, 2 cho tọa độ UV).
- type là kiểu dữ liệu của mỗi thành phần trong thuộc tính (ví dụ: GL_FLOAT, GL_INT).
- stride là khoảng cách giữa các đỉnh trong VBO. Đây là thông tin về bước nhảy giữa các dữ liệu của các đỉnh liên tiếp.
- offset là vị trí bắt đầu của dữ liệu trong VBO cho thuộc tính cần liên kết.
**Trong file Main.cpp**<br>
```cpp
GLfloat vertices[] =
{           // CORDINAES                                     COLOR
	-0.5f, -0.5f * float (sqrt (3)) / 3,      0.0f,    0.8f, 0.3f,  0.02f, // Lower left corner
	0.5f,  -0.5f * float (sqrt (3)) / 3,      0.0f,    0.8f, 0.3f,  0.02f,// Lower right corner
	0.0f,   0.5f * float (sqrt (3)) * 2 / 3,  0.0f,    1.0f, 0.6f,  0.32f,// Upper corner
	-0.25f, 0.5f * float (sqrt (3)) / 6,      0.0f,    0.9f, 0.45f, 0.17f, // Inner left
	0.25f,  0.5f * float (sqrt (3)) / 6,      0.0f,    0.9f, 0.45f, 0.17f,// Inner right
	0.0f,  -0.5f * float (sqrt (3)) / 3,      0.0f,    0.8f, 0.3f, 0.02f,// Inner down
};
```
Xác định tọa độ và màu sắc
```cpp
// Links VBO to VAO
VAO1.LinkAttrib (VBO1, 0, 3, GL_FLOAT, 6 * sizeof (float), (void*)0);
VAO1.LinkAttrib (VBO1, 1, 3, GL_FLOAT, 6*sizeof(float), (void*)(3*sizeof(float)));
```
Dòng mã VAO1.LinkAttrib(VBO1, 0, 3, GL_FLOAT, 6 * sizeof(float), (void*)0) và VAO1.LinkAttrib(VBO1, 1, 3, GL_FLOAT, 6*sizeof(float), (void*)(3*sizeof(float))) được sử dụng để liên kết các thuộc tính của VAO với VBO.

Trong đó:

VAO1 là một đối tượng VAO (Vertex Array Object) được khởi tạo trước đó.

VBO1 là một đối tượng VBO (Vertex Buffer Object) chứa dữ liệu đỉnh.

Tham số thứ nhất (0 và 1) là chỉ số của thuộc tính đầu vào trong VAO mà chúng ta muốn liên kết với VBO. Trong trường hợp này, chỉ số 0 và 1 tương ứng với các thuộc tính vị trí và màu sắc của đỉnh.

Tham số thứ hai (3) là số lượng thành phần dữ liệu trong mỗi đỉnh của thuộc tính. Trong trường hợp này, chúng ta có 3 thành phần cho cả vị trí và màu sắc (x, y, z hoặc r, g, b).

Tham số thứ ba (GL_FLOAT) là kiểu dữ liệu của mỗi thành phần trong thuộc tính. Trong trường hợp này, chúng ta sử dụng kiểu dữ liệu GL_FLOAT để đại diện cho các giá trị dấu phẩy động.

Tham số thứ tư (6 * sizeof(float) và 6*sizeof(float)) là khoảng cách giữa các đỉnh trong VBO. Đây là thông tin về bước nhảy giữa các dữ liệu của các đỉnh liên tiếp. Vì mỗi đỉnh có 6 thành phần (3 cho vị trí và 3 cho màu sắc), nên khoảng cách là 6 * sizeof(float).

Tham số thứ năm ((void*)0 và (void*)(3sizeof(float))) là vị trí bắt đầu của dữ liệu trong VBO cho thuộc tính tương ứng. Trong trường hợp đầu tiên, vị trí bắt đầu của thuộc tính vị trí là 0, và trong trường hợp thứ hai, vị trí bắt đầu của thuộc tính màu sắc là 3sizeof(float), tức là sau 3 thành phần của thuộc tính vị trí.

Tổng cộng, các dòng mã này xác định cách dữ liệu trong VBO sẽ được liên kết và truyền vào thuộc tính tương ứng của shader trong VAO. Điều này cho phép OpenGL biết cách trích xuất và sử dụng dữ liệu từ VBO để hiển thị các đỉnh của đối tượng.
```cpp
GLuint uniID = glGetUniformLocation (shaderProgram.ID, "scale");

// Main while loop
while (!glfwWindowShouldClose (window))
{
   // Specify the color of the background
   glClearColor (0.07f, 0.13f, 0.17f, 1.0f);
   // Clean the back buffer and assign the new color to it
   glClear (GL_COLOR_BUFFER_BIT);
   // Tell OpenGL which Shader Program we want to use
   shaderProgram.Activate ();
   glUniform1f (uniID, 0.5f);
   // Bind the VAO so OpenGL knows to use it
   VAO1.Bind ();
   // Draw primitives, number of indices, datatype of indices, index of indices
   glDrawElements (GL_TRIANGLES, 9, GL_UNSIGNED_INT, 0);
   // Swap the back buffer with the front buffer
   glfwSwapBuffers (window);
   // Take care of all GLFW events
   glfwPollEvents ();
}
```

Dòng mã GLuint uniID = glGetUniformLocation(shaderProgram.ID, "scale"); được sử dụng để lấy vị trí (location) của một biến uniform trong shader program.

shaderProgram là một đối tượng Shader được khởi tạo trước đó, chứa các shader vertex và fragment.
.ID là thành viên của đối tượng Shader, đại diện cho ID của shader program đã được liên kết và sử dụng trong OpenGL.
glGetUniformLocation là một hàm trong OpenGL để lấy vị trí của một biến uniform trong shader program. Nó nhận vào ID của shader program và tên của biến uniform cần lấy vị trí.

Trong trường hợp này, biến uniform có tên "scale" trong shader program được tìm kiếm, và vị trí (location) của nó được lưu trữ trong biến uniID có kiểu dữ liệu GLuint (unsigned int).

Việc lấy vị trí của biến uniform là để sau này có thể gửi giá trị cho biến uniform từ ứng dụng. Bằng cách lấy vị trí này, chúng ta có thể gọi các hàm như glUniform1f để gửi giá trị cho biến uniform scale trong shader program.
