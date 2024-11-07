# 设置CMake最低版本要求 / Set minimum CMake version
cmake_minimum_required(VERSION 3.21)

##################################################################################################
#################################### 用户配置区开始 / User Configuration Start #####################
##################################################################################################

# 项目信息 / Project Information
project(ProjectName)

######################################## 基础构建设置 / Basic Build Settings ######################
# 设置构建类型 / Set build type
set(CMAKE_BUILD_TYPE RelWithDebInfo)
# 导出编译命令 / Export compile commands
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

######################################## 宏定义设置 / Macro Definitions ###########################
# 示例宏定义（可根据需要启用） / Example macro definitions (enable as needed)
# add_definitions(-DCONFIG_FILE_PATH="${CMAKE_CURRENT_SOURCE_DIR}/config/config.yaml")
# add_definitions(-DPACKAGE_PATH="${CMAKE_CURRENT_SOURCE_DIR}")

######################################## 依赖项配置 / Dependencies Configuration ##################
# 设置第三方库依赖 / Set third-party dependencies
set(dependencies
    ExampleLib1  # 示例库1 / Example library 1
    ExampleLib2  # 示例库2 / Example library 2
    # 添加更多依赖库 / Add more dependencies
)

# 设置ROS包依赖（如果需要） / Set ROS package dependencies (if needed)
set(parent_packages
    # ROS包依赖列表 / ROS package dependencies list
)

######################################## 构建目标配置 / Build Targets Configuration ##############
# 定义构建目标列表 / Define build targets list
# 格式 / Format:
# set(TARGET_NAME "目标名称" / "Target Name")
# set(${TARGET_NAME}_TYPE "EXECUTABLE|LIBRARY|INTERFACE")
# set(${TARGET_NAME}_SOURCES "源文件列表或目录" / "Source files or directory")

# 示例：定义一个库目标 / Example: Define a library target
set(TARGET1 "ExampleTarget")
set(${TARGET1}_TYPE "LIBRARY")
aux_source_directory(src ${TARGET1}_SOURCES)

# 将所有目标名称添加到列表中 / Add all target names to the list
set(ALL_TARGETS
    ${TARGET1}
    # 在此添加更多目标 / Add more targets here
)

##################################################################################################
#################################### 用户配置区结束 / User Configuration End #######################
##################################################################################################

# 查找ament_cmake包（如果使用ROS2） / Find ament_cmake package (if using ROS2)
find_package(ament_cmake REQUIRED)

##################################################################################################
#################################### 自动处理区开始 / Automatic Processing Start ##################
##################################################################################################

######################################## 依赖处理 / Dependencies Handling ########################
# 查找所有依赖包 / Find all dependencies
foreach(dependency IN LISTS dependencies parent_packages)
    find_package(${dependency} REQUIRED)
endforeach()

# 设置包含目录 / Set include directories
include_directories(include)

# 自动处理依赖包的包含目录 / Automatically handle include directories for dependencies
foreach(dependency IN LISTS dependencies parent_packages)
    string(TOUPPER "${dependency}" DEP_UPPER)
    string(TOLOWER "${dependency}" DEP_LOWER)

    message(STATUS "Checking include directories for ${dependency}")
    if (DEFINED ${DEP_UPPER}_INCLUDE_DIRS OR DEFINED ${DEP_LOWER}_INCLUDE_DIRS)
        if (DEFINED ${DEP_UPPER}_INCLUDE_DIRS)
            include_directories(${${DEP_UPPER}_INCLUDE_DIRS})
            message(STATUS "Using ${DEP_UPPER}_INCLUDE_DIRS")
        else()
            include_directories(${${DEP_LOWER}_INCLUDE_DIRS})
            message(STATUS "Using ${DEP_LOWER}_INCLUDE_DIRS")
        endif()
    else()
        message(STATUS "No include directories found for ${dependency}")
    endif()
endforeach()

######################################## 目标构建 / Target Building #############################
# 处理每个目标 / Process each target
foreach(TARGET_NAME IN LISTS ALL_TARGETS)
    message(STATUS "Processing target: ${TARGET_NAME}")

    if(${${TARGET_NAME}_TYPE} STREQUAL "EXECUTABLE")
        # 添加可执行文件目标 / Add executable target
        add_executable(${TARGET_NAME} ${${TARGET_NAME}_SOURCES})

        # 设置安装规则 / Set install rules
        install(TARGETS ${TARGET_NAME}
                RUNTIME DESTINATION bin
        )

    elseif(${${TARGET_NAME}_TYPE} STREQUAL "LIBRARY")
        # 添加库目标 / Add library target
        add_library(${TARGET_NAME} SHARED ${${TARGET_NAME}_SOURCES})

        # 设置安装规则 / Set install rules
        install(TARGETS ${TARGET_NAME}
                EXPORT export_${PROJECT_NAME}
                LIBRARY DESTINATION lib
                ARCHIVE DESTINATION lib
        )
        ament_export_libraries(${TARGET_NAME})

    elseif(${${TARGET_NAME}_TYPE} STREQUAL "INTERFACE")
        # 接口库不需要源文件 / Interface library does not need source files
        add_library(${TARGET_NAME} INTERFACE)
        message(STATUS "Creating interface-only library: ${TARGET_NAME}")
    endif()

    # 如果不是接口库，设置依赖 / Set dependencies if not an interface library
    if(NOT ${${TARGET_NAME}_TYPE} STREQUAL "INTERFACE")
        # 用户定义： 系统库依赖 / User-defined: system library dependencies
        target_link_libraries(${TARGET_NAME}
            # 添加库依赖 / Add library dependencies
        )
        ament_target_dependencies(${TARGET_NAME} ${parent_packages})
    endif()
endforeach()

######################################## 通用安装配置 / General Installation Configuration ######
# 安装头文件 / Install header files
install(DIRECTORY include/
        DESTINATION include/
)
install(DIRECTORY launch/
DESTINATION share/${PROJECT_NAME}/)

######################################## ROS2包配置 / ROS2 Package Configuration ################
# 导出包依赖 / Export package dependencies
ament_export_dependencies(${parent_packages})
# 导出包含目录/ Export package include dirs
ament_export_include_directories(include)
# 生成包配置/ generate package configuration
ament_package()

##################################################################################################
#################################### 自动处理区结束 / Automatic Processing End ####################
##################################################################################################

