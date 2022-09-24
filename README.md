# cmake 学习指南

cmake阅读资料主要参考了[cmake官方手册](https://cmake.org/cmake/help/latest/guide/tutorial/)，工作目录中是官方提供的参考示例，这里根据文档学习了cmake的使用，并记录下部分学习心得

1. option结合if达到开关的效果：

    ```cmake
    option(USE_MYMATH "Use tutorial provided math implementation" ON)

    if(USE_MYMATH)
        add_subdirectory(MathFunctions)
        list(APPEND EXTRA_LIBS MathFunctions)
        list(APPEND EXTRA_INCLUDE "${PROJECT_SOURCE_DIR}/MathFunctions")
    endif()
    ```
- 其中`list`根据第一个参数选择效果不同，`APPEND`将第三个参数加入到第二个命名变量中去
- **cmake使用 $ 引用变量时，带不带双引号貌似效果是一样的**
- `add_subdirectory`是将子级目录下的CMakeLists.txt构建引入进来，相当于提醒当前CMakeLists.txt(1)别忘了下去看看这个CMakeLists.txt(2)里面的内容，项目结构如下所示：

    ```
    -----/root
        |----MathFunctions
            |----CMakeLists.txt(2)
            |----MathFunctions.h
            |----mysqrt.cxx
        |----tutorial.cxx
        |----CMakeLists.txt(1)
    ```
2. target_link_libraries的作用：

    ```cmake
    # add the executable
    add_executable(Tutorial tutorial.cxx)

    target_link_libraries(Tutorial PUBLIC ${EXTRA_LIBS})
    ```
    这个的作用是把已有的library链接到编译好的可执行文件Tutorial上，这里的`{EXTRA_LIBS}`是在上面`list`中加入的，因为在子级目录下的CMakeLists.txt(2)中声明了`add_library(MathFunctions mysqrt.cxx)` ，本质上就是把mysqrt.cxx加入了一个叫MathFunctions的lib中，外面的CMakeLists.txt(1)中又add_subdirectory所以可以在这里链接库

    - **需要着重强调一下的是`add_executable(Tutorial tutorial.cxx)`中的*Tutorial*是生成的可执行文件的名字，并不是项目project中声明的名字，这里一样完全是巧合，这也解释了为什么`target_link_libraries`、`target_include_directories`要写在后面，是因为要不然的话*Tutorial*是还没有声明的，会出现错误**

3. target_include_directories：

   ```cmake
    target_include_directories(Tutorial PUBLIC
        "${PROJECT_BINARY_DIR}"
        ${EXTRA_INCLUDE})
    ```
    主要是用来链接头文件(.h)的，`${EXTRA_INCLUDE}`因为前面和option一起用的缘故，还有可选的功能
    
    --------

    注意关键字`INTERFACE`，如官网解释的那样:

    > Remember INTERFACE means things that consumers require but the producer doesn't.
    
    也就是说`INTERFACE`主要是对其他引用该lib的内容起作用，下面这句的意思是，当前文件夹下的所有文件都需要MathFunctions提供的头文件，主要是为了更好的控制lib的链接
    ```cmake
    target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          )
    ```

4. install
    执行install的时候最好加上--prefix，不指明安装地址的话，默认应该是安装到/usr/local对应的bin、lib、include下面去了
    ```bash
    cmake --install . --prefix "/Volumes/SSD/cmake_test/Step1_build/install"
    ```