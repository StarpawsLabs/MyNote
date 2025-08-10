***pybind11***
**仓库地址** https://github.com/pybind/pybind11.git

下载完后放到extern文件夹中


根目录创建文件CMakeLists.txt,其中编写代码
```CMakeLists.txt
    cmake_minimum_required(VERSION 3.4)
    project(example)
    add_subdirectory(extern/pybind11)
    pybind11_add_module(example example.cpp)
```



随后接着写代码即可

在主代码文件下面创建接口代码

**绑定函数代码：**
```cpp
    //bind.cpp
    #include<pybind11/pybind11.h>
    YBIND11_MODULE(example,m){//example:模块名称 m:模块信息
       m.doc() = "Your module`s introduction";//编辑模块介绍
       m.def("MyAdd", &MyAdd, "Your func`s introduction");//编辑函数介绍和绑定函数
    }
```

**绑定类代码**
```cpp
    //bind.cpp
    #include<pybind11/pybind11.h>
    #include"Example.hpp"
    PYBIND11_MODULE(example, m) {
        pybind11::class_<Example>(m, "Example")
            .def(pybind11::init<int, int>())//构造函数
            .def("Func1", &Example::Func1)//函数1
            .def("Func2", &Example::Func2)//函数2
            .def_static("StaticFunc", &Example::StaticFunc);//静态函数
}
```

根目录建立build文件夹，进入其中执行


``` cmd
    cmake ..
    make
```



