# Purpose: Guide to create OpenGL window for C++ in Visual Studio 2022
## Libraries: 
  ```cpp
    #include<iostream>
    #include<glad/glad.h>
    #include<GLFW/glfw3.h>
```
## Chú ý:
Trong cửa sổ của openGL nếu bạn sử dụng hàm glViewport(0, 0, 800, 600) để đặt kích thước cửa sổ đồ họa là 800 pixel chiều rộng và 600 pixel chiều cao, thì giá trị 1.0f trong hệ tọa độ NDC sẽ tương ứng với kích thước của cửa sổ đồ họa.
## Main Function
```cpp
glfwInit()
// Khởi tạo GLFW và môi trường để OpenGL làm việc
```
```cpp
// glfwWindowHint() là hàm dùng để thiết lập cấu hình và tùy chọn cho cửa sổ trước khi tạo ra nó
glfwWindowHint (GLFW_CONTEXT_VERSION_MAJOR, 3);
glfwWindowHint (GLFW_CONTEXT_VERSION_MINOR, 3);
// Xác định phiên bản chính và phiên bản con của OpenGL mà bạn muốn sử dụng
// Trong trường hợp này nó là 3.3
glfwWindowHint (GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
// GLFW_OPENGL_PROFILE giúp các định loại hồ sơ (profile) bạn muốn sử dụng. Trong trường hợp này nó là
// GLFW_OPENGL_CORE_PROFILE: sử dụng các tính năng "core" trong openGL và loại bỏ các tính năng cũ
```
```cpp
GLFWwindow* window = glfwCreateWindow (800, 800, "MinhHung", NULL, NULL);
// Tạo cửa sổ với chiều rộng 800px, dài 800px
// Tiêu đề: MinhHung
//NULL: Con trỏ đến cửa sổ chính nếu bạn muốn tạo một cửa sổ OpenGL chia sẻ với một cửa sổ khác. Trong trường hợp này, không có cửa sổ chia sẻ, vì vậy được thiết lập là NULL.
//NULL: Con trỏ đến màn hình chính hoặc khung cửa sổ nơi cửa sổ GLFW sẽ được đặt. Trong trường hợp này, không có màn hình hoặc khung cửa sổ chính, vì vậy được thiết lập là NULL.
```
```cpp
if (window == NULL)
{
  glfwTerminate ();
  return -1;
}
```
```cpp
glfwMakeContextCurrent (window);
// Đưa cửa sổ window làm cửa sổ chính
// Các thao tác tiếp theo sẽ thực hiện trên cửa sổ đó
gladLoadGL ();
// Tải các hàm OpenGL vào bộ nhớ và sẵn sàng để sử dụng
```
```cpp
glViewport (0, 0, 800, 800);
//glViewport(): Hàm này đặt kích thước của khu vực hiển thị trong cửa sổ OpenGL.
//Trong trường hợp này, kích thước của khu vực hiển thị được đặt là 800x800 pixel, bắt đầu từ tọa độ (0, 0).
glClearColor (0.07f, 0.13f, 0.17f, 1.0f);
// Tạo màu nền cho cửa sổ
glClear (GL_COLOR_BUFFER_BIT);
//glClear(): Hàm này xóa nội dung của bộ đệm màu (color buffer) trong cửa sổ OpenGL bằng màu xóa nền đã đặt trước đó.
glfwSwapBuffers (window);
//glfwSwapBuffers: Hàm này hoán đổi các bộ đệm (buffer) hiển thị và bộ đệm vẽ (draw buffer) của cửa sổ GLFW.
//Điều này giúp hiển thị nội dung đã được vẽ trên cửa sổ.
```
```cpp
// Duy trì cửa sổ cho đến khi người dùng yêu cầu đóng cửa sổ
while (!glfwWindowShouldClose (window))  // glfwWindowShouldClose(window) trả về true nếu yêu cầu đóng cửa sổ
{
  glfwPollEvents ();
  //Hàm này kiểm tra và xử lý các sự kiện như di chuyển chuột, nhấn phím, thay đổi kích thước cửa sổ, và các sự kiện khác.
}
```
```cpp
glfwDestroyWindow (window);
// Hủy bỏ cửa sổ, giải phóng tài nguyên
glfwTerminate ();
// Giải phóng tài nguyên và đảm bảo của sổ được hủy chính xác
```

























