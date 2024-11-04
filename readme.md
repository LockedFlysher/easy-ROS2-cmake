# 项目简介 / Project Overview

这是一个用于开发ROS2项目的CMakeLists模板。通过使用此模板，您可以快速配置和构建ROS2包。

This is a CMakeLists template for developing ROS2 projects. With this template, you can quickly configure and build ROS2 packages.

## 使用步骤 / Usage Steps

### 1. 克隆项目 / Clone the Project

将项目代码克隆到本地：

Clone the project code to your local machine:

```bash
git clone <repository-url>
cd <repository-directory>
```

### 2. 修改CMakeLists.txt / Modify CMakeLists.txt

在使用此模板之前，您需要根据项目需求对CMakeLists.txt进行以下修改：

Before using this template, you need to modify the CMakeLists.txt according to your project requirements:

- **项目名称 / Project Name**: 修改 `project(ProjectName)` 为您的项目名称。

  Change `project(ProjectName)` to your project name.

- **依赖库 / Dependencies**: 在 `set(dependencies ...)` 中列出您的项目所需的第三方库。

  List the third-party libraries required by your project in `set(dependencies ...)`.

- **ROS包依赖 / ROS Package Dependencies**: 在 `set(parent_packages ...)` 中添加您的ROS包依赖。

  Add your ROS package dependencies in `set(parent_packages ...)`.

- **构建目标 / Build Targets**: 根据需要定义构建目标（可执行文件或库），并在 `set(ALL_TARGETS ...)` 中添加目标名称。

  Define build targets (executables or libraries) as needed, and add target names in `set(ALL_TARGETS ...)`.

- **链接库 / Link Libraries**: 在 `target_link_libraries` 中指定构建目标所需链接的库。

  Specify the libraries required for linking the build targets in `target_link_libraries`.

### 3. 使用colcon构建项目 / Build the Project with colcon

在ROS2工作空间中，使用`colcon build`命令构建项目：

In a ROS2 workspace, use the `colcon build` command to build the project:

```bash
colcon build
```

## 示例 / Example

以下是一个简单的使用示例：

Here is a simple usage example:

```cmake
# 设置项目名称 / Set the project name
project(MyROS2Project)

# 设置第三方库依赖 / Set third-party dependencies
set(dependencies
    rclcpp
    std_msgs
)

# 设置ROS包依赖 / Set ROS package dependencies
set(parent_packages
    my_custom_msgs
)

# 定义构建目标 / Define build targets
set(TARGET1 "my_node")
set(${TARGET1}_TYPE "EXECUTABLE")
set(${TARGET1}_SOURCES "src/my_node.cpp")

# 将所有目标名称添加到列表中 / Add all target names to the list
set(ALL_TARGETS
    ${TARGET1}
)

# 链接库(要在下面找一下) / Link libraries(find it down to the "Set dependencies if not an interface library")
target_link_libraries(${TARGET1}
    ${dependencies}
)
```

## 作者 / Author

[晴糖豆的B站](https://space.bilibili.com/438723840)