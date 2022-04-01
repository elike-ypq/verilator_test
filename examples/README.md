## Verilator 示例

### 1	文件结构如下

```
├── cmake_hello_c ---------------------------------->CMake hello项目
│   ├── build
│   ├── CMakeLists.txt  ----------------------------->cmake 的主文件
│   ├── Makefile	----------------------------------->集成make处理
│   ├── logs   --------------------------------->调试输出
│   └── out
├── cmake_hello_sc
│   ├── build
│   ├── CMakeLists.txt
│   ├── logs
│   └── Makefile
├── cmake_protect_lib
│   ├── CMakeLists.txt
│   └── Makefile
├── cmake_tracing_c
│   ├── build
│   ├── CMakeLists.txt
│   ├── logs
│   └── Makefile
├── cmake_tracing_sc
│   ├── build
│   ├── CMakeLists.txt
│   ├── logs
│   └── Makefile
├── make_hello_c
│   ├── Makefile
│   ├── sim_main.cpp
│   └── top.v
├── make_hello_sc
│   ├── Makefile
│   ├── obj_dir
│   ├── sc_main.cpp
│   └── top.v
├── make_protect_lib
│   ├── Makefile
│   ├── secret_impl.v
│   ├── sim_main.cpp
│   └── top.v
├── make_tracing_c
│   ├── input.vc
│   ├── logs
│   ├── Makefile
│   ├── Makefile_obj
│   ├── obj_dir
│   ├── sim_main.cpp
│   ├── sub.v
│   └── top.v
├── make_tracing_sc
│   ├── input.vc
│   ├── logs
│   ├── Makefile
│   ├── Makefile_obj
│   ├── obj_dir
│   ├── sc_main.cpp
│   ├── sub.v
│   └── top.v
├── README.md
```

其中源文件为`top.v sub.v sim_main.cpp sc_main.cpp`，其中`top.v sub.v`为SystemVerilog语言编写的文件，`sc_main`为SystemC语言的文件。

注意在安装SystemC时候，如下

```bash
cd systemc-2.3.2
autoconf
mkdir objdir
cd objdir
../configure --prefix=/usr/local/systemc-2.3.2 CPPFLAGS='-std=c++14' CXXFLAGS='-std=c++14'
make
# make check
make install
```

其中`CXXFLAGS='-std=c++14'`必须加上，否则会出现

如下问题

```text
undefined reference to `sc_core::sc_api_version_2_3_3_cxx201402L<&sc_core::SC_DISABLE_VIRTUAL_BIND_UNDEFINED_>::sc_api_version_2_3_3_cxx201402L(sc_core::sc_writer_policy)
```



### 2	生成Microsoft Visual Studio 2019项目

上述文件起头为`cmake`的，都可以在cmake下进行配置执行。需要将上述Makefile文件中的重新注释即可，当前可在`mingw64`下执行成功。修改之后，可以在MVSC下编译通过，且生成了项目文件`*.sln`，可以实现在MVSC下调试。**但是在MVSC下执行存在无法记录`*.vcd`波形文件的问题**。

```makefile
@echo "-- VERILATE ----------------"
#	mkdir -p build && cd build && cmake ..
	mkdir -p build && cd build && cmake .. -G "MinGW Makefiles"
	@echo
	@echo "-- BUILD -------------------"
	cmake --build build -j

	@echo
	@echo "-- RUN ---------------------"
	@mkdir -p logs
#	build/Debug/example +trace
	build/example +trace
```

还有一点，为了能在MVSC下成功编译，需要在`CMakelists.txt`中加上

```cmake
if(MSVC)
    string(APPEND CMAKE_CXX_FLAGS " /Zc:__cplusplus")
endif()
```

