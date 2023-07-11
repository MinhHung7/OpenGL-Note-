# Trong file "default.vert"
```cpp
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColor;
layout (location = 2) in vec2 aTex;

out vec3 color;

out vec2 texCoord;

uniform float scale;

uniform mat4 model;
uniform mat4 view;
uniform mat4 proj;


void main()
{
   gl_Position = proj * view * model * vec4(aPos, 1.0);
   color = aColor;
   texCoord = aTex;
}
```
uniform mat4 model là một ma trận 4x4 (mat4) được sử dụng để biến đổi các điểm trong không gian đối tượng (local space) thành không gian thế giới (world space). Nó thường biểu diễn vị trí, quay, và tỷ lệ của đối tượng.

uniform mat4 view là một ma trận 4x4 (mat4) được sử dụng để biến đổi các điểm trong không gian thế giới (world space) thành không gian ngắm (view space) hoặc không gian camera. Nó thường biểu diễn vị trí và hướng nhìn của camera.

uniform mat4 proj là một ma trận 4x4 (mat4) được sử dụng để thực hiện phép chiếu (projection) của các điểm trong không gian ngắm (view space) vào không gian màn hình (clip space) để hiển thị lên màn hình. Nó thường biểu diễn các thông số liên quan đến góc nhìn, ví dụ như góc nhìn theo chiều ngang (fovy), tỷ lệ khung hình (aspect ratio), và khoảng cách gần và xa (near và far) trong phép chiếu.
# Trong file "Main.cpp"
```cpp
#include<glm/glm.hpp>
#include<glm/gtc/matrix_transform.hpp>
#include<glm/gtc/type_ptr.hpp>
```
#include<glm/glm.hpp> bao gồm các lớp và hàm cơ bản của glm như vec2, vec3, vec4, mat2, mat3, mat4, và các phép toán tương ứng với chúng.

#include<glm/gtc/matrix_transform.hpp> bao gồm các hàm để tạo và biến đổi ma trận trong glm, bao gồm cả các hàm biến đổi vị trí, xoay, tỷ lệ và chiếu.

#include<glm/gtc/type_ptr.hpp> cung cấp các hàm hỗ trợ để truy cập dữ liệu trong glm từ các kiểu dữ liệu chuẩn trong OpenGL như GLfloat*, GLint*,...
```cpp
GLfloat vertices[] =
{ //     COORDINATES     /        COLORS      /   TexCoord  //
	-0.5f, 0.0f,  0.5f,     0.83f, 0.70f, 0.44f,	0.0f, 0.0f,
	-0.5f, 0.0f, -0.5f,     0.83f, 0.70f, 0.44f,	5.0f, 0.0f,
	 0.5f, 0.0f, -0.5f,     0.83f, 0.70f, 0.44f,	0.0f, 0.0f,
	 0.5f, 0.0f,  0.5f,     0.83f, 0.70f, 0.44f,	5.0f, 0.0f,
	 0.0f, 0.8f,  0.0f,     0.92f, 0.86f, 0.76f,	2.5f, 5.0f
};

// Indices for vertices order
GLuint indices[] =
{
	0, 1, 2,
	0, 2, 3,
	0, 1, 4,
	1, 2, 4,
	2, 3, 4,
	3, 0, 4
};

```
vertices: Mảng này chứa thông tin về các đỉnh của hình dạng ba chiều. Mỗi đỉnh được định nghĩa bằng tọa độ (x, y, z), màu sắc (r, g, b), và tọa độ texture (s, t). Dòng đầu tiên (-0.5f, 0.0f, 0.5f, 0.83f, 0.70f, 0.44f, 0.0f, 0.0f) định nghĩa đỉnh đầu tiên với tọa độ (-0.5, 0.0, 0.5), màu sắc (0.83, 0.70, 0.44) và tọa độ texture (0.0, 0.0). Tương tự, các đỉnh khác cũng được định nghĩa tương tự.

indices: Mảng này chứa chỉ mục (index) của các đỉnh trong mảng vertices để xác định thứ tự vẽ các tam giác. Ví dụ, các số 0, 1, 2 trong dòng đầu tiên (0, 1, 2) xác định rằng ta cần lấy các đỉnh thứ 0, 1 và 2 từ mảng vertices để tạo một tam giác. Tương tự, các số trong các dòng khác xác định các đỉnh cần được lấy để tạo các tam giác khác.

Tổng cộng, trong đoạn mã trên, ta có 5 đỉnh và 6 tam giác được tạo từ các đỉnh đó.
```cpp
  float rotation = 0.0f;
  double prevTime = glfwGetTime ();
  
  glEnable (GL_DEPTH_TEST);
```
double prevTime = glfwGetTime();: Câu lệnh này lấy thời gian hiện tại từ GLFW và gán nó cho biến prevTime. Hàm glfwGetTime() trả về số giây kể từ khi GLFW được khởi động. Biến prevTime thường được sử dụng để lưu trữ thời điểm thời gian trước đó, thường được sử dụng trong vòng lặp để tính toán thời gian chênh lệch giữa các khung hình (frame) và thực hiện các hoạt động theo thời gian.

glEnable(GL_DEPTH_TEST);: Câu lệnh này kích hoạt chế độ kiểm tra độ sâu (depth testing) trong OpenGL. Khi chế độ này được bật, OpenGL sẽ tự động so sánh giá trị độ sâu (depth value) của các điểm trong không gian 3D với giá trị độ sâu hiện tại trong bộ đệm độ sâu (depth buffer). Điều này giúp xác định những điểm nào nằm phía trước và nằm phía sau của các vật thể, từ đó xác định xem điểm đó có nằm trong tầm nhìn (visible) hay không. Các điểm nằm phía sau sẽ không được hiển thị trên màn hình. Chế độ kiểm tra độ sâu thường được sử dụng để đảm bảo các đối tượng trong không gian 3D được hiển thị đúng theo thứ tự từ xa đến gần (depth ordering).

**Trong while (!glfwWindowShouldClose (window))**
```cpp
glClear (GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

double crntTime = glfwGetTime ();
if (crntTime - prevTime >= 1 / 60)
{
  rotation += 0.5f;
  prevTime = crntTime;
}
```
```cpp
glm::mat4 model = glm::mat4 (1.0f);
glm::mat4 view = glm::mat4 (1.0f);
glm::mat4 proj = glm::mat4 (1.0f);
```
glm::mat4 model = glm::mat4(1.0f);: Ma trận model là ma trận biến đổi cho mô hình đối tượng 3D. Đối tượng glm::mat4(1.0f) tạo ra một ma trận đơn vị, có tất cả các phần tử trên đường chéo chính (đi từ góc trên trái đến góc dưới phải) đều bằng 1 và các phần tử còn lại bằng 0. Ma trận này thường được sử dụng để biến đổi vị trí, tỷ lệ và quay của đối tượng.

glm::mat4 view = glm::mat4(1.0f);: Ma trận view là ma trận biến đổi cho ma trận ngắm (view matrix) trong hệ thống camera. Ma trận glm::mat4(1.0f) tạo ra một ma trận đơn vị, tương tự như trên, và thường được sử dụng để biểu diễn vị trí và hướng nhìn của camera trong không gian 3D.

glm::mat4 proj = glm::mat4(1.0f);: Ma trận proj là ma trận chiếu (projection matrix) để thực hiện phép chiếu của không gian ngắm (view space) vào không gian màn hình (clip space). Ma trận glm::mat4(1.0f) cũng tạo ra một ma trận đơn vị, và thường được sử dụng để biểu diễn các thông số liên quan đến góc nhìn, tỷ lệ khung hình và khoảng cách gần và xa trong phép chiếu.
```cpp
model = glm::rotate (model, glm::radians (rotation), glm::vec3 (0.0f, 1.0f, 0.0f));
view = glm::translate (view, glm::vec3 (0.0f, -0.5f, -2.0f));
proj = glm::perspective (glm::radians (45.0f), (float)(width / height), 0.1f, 100.0f);
```
model = glm::rotate(model, glm::radians(rotation), glm::vec3(0.0f, 1.0f, 0.0f));: Dòng này sử dụng hàm glm::rotate để thực hiện phép quay trên ma trận model. Phép quay được xác định bằng cách sử dụng góc quay (glm::radians(rotation)) và trục quay (glm::vec3(0.0f, 1.0f, 0.0f)). Trong trường hợp này, ma trận model sẽ được quay theo trục y (hoặc trục lên xuống). Giá trị rotation là biến thay đổi theo thời gian để tạo hiệu ứng quay.

view = glm::translate(view, glm::vec3(0.0f, -0.5f, -2.0f));: Dòng này sử dụng hàm glm::translate để thực hiện phép dịch chuyển trên ma trận view. Phép dịch chuyển được xác định bằng cách sử dụng vectơ dịch chuyển (glm::vec3(0.0f, -0.5f, -2.0f)). Trong trường hợp này, ma trận view sẽ được dịch chuyển lên trên và lùi ra phía sau để tạo hiệu ứng di chuyển và căn chỉnh camera.

proj = glm::perspective(glm::radians(45.0f), (float)(width / height), 0.1f, 100.0f);: Dòng này sử dụng hàm glm::perspective để tạo ma trận chiếu (projection matrix). Hàm này nhận vào các tham số như góc mở (field of view), tỷ lệ khung hình, khoảng cách gần và xa. Trong trường hợp này, góc mở được đặt là 45 độ (glm::radians(45.0f)), tỷ lệ khung hình được tính từ kích thước cửa sổ width và height, khoảng cách gần và xa được đặt là 0.1 và 100.0. Ma trận proj sẽ thể hiện phép chiếu 3D thành không gian màn hình.
```cpp
int modelLoc = glad_glGetUniformLocation (shaderProgram.ID, "model");
glUniformMatrix4fv (modelLoc, 1, GL_FALSE, glm::value_ptr (model));
int viewLoc = glad_glGetUniformLocation (shaderProgram.ID, "view");
glUniformMatrix4fv (viewLoc, 1, GL_FALSE, glm::value_ptr (view));
int projLoc = glad_glGetUniformLocation (shaderProgram.ID, "proj");
glUniformMatrix4fv (projLoc, 1, GL_FALSE, glm::value_ptr (proj));
```
