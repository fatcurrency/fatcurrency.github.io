# 在 VSCode 中利用 Qt 5.9.8、CMake 和 VS2017 构建简单案例

# VSCode + Qt 5.9.8 + CMake + VS2017 构建简单案例

本文梳理在 VSCode 环境下，结合 Qt 5.9.8、CMake 和 Visual Studio 2017 搭建简单 Qt 案例的完整流程。

## 一、环境准备与检查

确保以下环境配置完成：

1. Visual Studio 2017：需安装 C++ 开发工具和 Windows SDK

2. Qt 5.9.8：需选择对应 MSVC2017 版本（32/64 位按需选择）

3. VSCode 插件：

    - C/C++（Microsoft 官方）

    - CMake（twxs）

    - CMake Tools（Microsoft）

4. 系统环境变量：

    - `Qt5_DIR`：指向 Qt 5.9.8 的 cmake 目录（示例：`D:\Qt\5.9.8\msvc2017_64\lib\cmake\Qt5`）

## 二、项目结构与文件编写

### 1. 项目目录结构

```Plain Text

QtSimpleDemo/
├── CMakeLists.txt  # CMake 配置文件
├── main.cpp        # 主程序代码
└── .vscode/        # VSCode 配置目录（自动生成/手动配置）
```

### 2. CMakeLists.txt 配置

```CMake

# 指定 CMake 最低版本（匹配 VS2017 和 Qt 5.9.8）
cmake_minimum_required(VERSION 3.9)

# 项目名称和语言
project(QtSimpleDemo LANGUAGES CXX)

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 查找 Qt 5 组件（核心 + GUI + 窗口组件）
find_package(Qt5 REQUIRED COMPONENTS Core Gui Widgets)

# 自动处理 Qt 的 MOC/RCC/UIC 工具（关键：Qt 信号槽必须加这个）
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# 指定源文件
set(SOURCES
    main.cpp
)

# 生成可执行文件
add_executable(${PROJECT_NAME} ${SOURCES})

# 链接 Qt 库
target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt5::Core
    Qt5::Gui
    Qt5::Widgets
)

# Windows 下设置可执行文件输出目录（可选，方便查找）
if(WIN32)
    set_target_properties(${PROJECT_NAME} PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin
    )
endif()
```

### 3. main.cpp 代码（简单窗口案例）

```C++

#include <QApplication>
#include <QWidget>
#include <QLabel>
#include <QVBoxLayout>

int main(int argc, char *argv[])
{
    // Qt 应用程序核心对象
    QApplication app(argc, argv);

    // 创建主窗口
    QWidget window;
    window.setWindowTitle("Qt 5.9.8 + CMake + VS2017 Demo");
    window.resize(400, 200); // 设置窗口大小

    // 创建标签
    QLabel *label = new QLabel("Hello Qt 5.9.8! 🎉", &window);
    label->setAlignment(Qt::AlignCenter); // 文字居中
    label->setStyleSheet("font-size: 20px; color: #2196F3;"); // 样式

    // 布局管理
    QVBoxLayout *layout = new QVBoxLayout(&window);
    layout->addWidget(label);
    window.setLayout(layout);

    // 显示窗口
    window.show();

    // 运行应用程序事件循环
    return app.exec();
}
```

## 三、VSCode 中编译运行

1. 打开 VSCode，导入 `QtSimpleDemo` 项目目录；

2. 按下 `Ctrl+Shift+P`，输入 `CMake: Select a Kit`，选择 `Visual Studio 2017 Release - amd64`（按需选 32 位 `x86`）；

3. 再次按下 `Ctrl+Shift+P`，输入 `CMake: Configure`，等待配置完成；

4. 按下 `Ctrl+Shift+B`，选择 `CMake: Build`，等待编译完成；

5. 编译成功后，可执行文件生成在 `build/bin/Debug`（或 Release）目录下；

6. 打开 Qt 5.9.8 命令行工具（如 `Qt 5.9.8 (MSVC 2017 64-bit)`）；

7. 切换到可执行文件目录（示例：`cd D:\QtSimpleDemo\build\bin\Debug`）；

8. 执行 `windeployqt QtSimpleDemo.exe`，自动复制依赖的 Qt 库；

9. 回到 VSCode，右键点击 `build/bin/Debug/QtSimpleDemo.exe`，选择 `Open in Terminal`，执行 `QtSimpleDemo.exe` 运行程序。

![结果可视化](mynote/image/hello-Qt5.9.8-in-vscode.png)

### 总结

1. 环境匹配是基础：需保证 Qt 版本（MSVC2017）、VS2017 工具链、CMake 版本兼容；

2. CMake 配置核心：正确查找 Qt 组件并启用 `AUTOMOC/AUTOUIC/AUTORCC`；

3. 运行关键步骤：Windows 下需通过 `windeployqt` 部署 Qt 依赖库，否则程序无法运行。
> 编辑于2026年2月3日