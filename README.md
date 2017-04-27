## Laboratory work X

Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```bash
$ open https://github.com/ruslo/hunter
```

## Tasks

- [ ] 1. Создать публичный репозиторий с названием **lab10** на сервисе **GitHub**
- [ ] 2. Выполнить инструкцию учебного материала
- [ ] 3. Ознакомиться со ссылками учебного материала

## Tutorial

```bash
$ export GITHUB_USERNAME=<имя_пользователя>
```

```bash
$ curl https://github.com/${GITHUB_USERNAME}/lab9/archive/v0.1.0.0.tar.gz
$ export PRINT_SHA1=`openssl sha1 v0.1.0.0.tar.gz`
```

```bash
$ git clone https://github.com/ruslo/hunter hunter
$ cd hunter && cd cmake
$ mkdir projects/print
$ cat > projects/print/hunter.cmake <<EOF
include(hunter_add_version)
include(hunter_cacheable)
include(hunter_cmake_args)
include(hunter_download)
include(hunter_pick_scheme)

hunter_add_version(
    PACKAGE_NAME
    puffin-buffer
    VERSION
    "0.1.0.0"
    URL
    "https://github.com/${GITHUB_USERNAME}/print/archive/v0.1.0.0.tar.gz"
    SHA1
    ${PRINT_SHA1}
)

hunter_pick_scheme(DEFAULT url_sha1_cmake)

hunter_cmake_args(
    print
    CMAKE_ARGS
    BUILD_EXAMPLES=NO
    BUILD_TESTS=NO
)
hunter_cacheable(puffin-buffer)
hunter_download(PACKAGE_NAME puffin-buffer)
EOF
$ cat >> configs/default.cmake <<EOF
hunter_config(print VERSION 0.1.0.0)
EOF
$ cd ../..
```

```bash
$ env HUNTER_ROOT `pwd`/hunter
$ git clone https://github.com/${GITHUB_USERNAME}/lab10
$ cd lab10
```

```bash
$ mkdir sources
$ cat > sources/demo.cpp <<EOF
#include <print.hpp>

int main(int argc, char** argv) {
	std::string text;
	while(cin >> text) {
		std::ofstream out("log.txt");
		print(text, out);
	}
}
EOF
$ mkdir cmake
$ curl https://...hunter/gate -o cmake/HunterGate.cmake
```

```bash
cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
```

```bash
$ cat >> CMakeLists.txt <<EOF
include(cmake/HunterGate.cmake)

HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.7.0.tar.gz"
    SHA1 "e730118c7ec65126398f8d4f09daf9366791ede0"
)
```

```bash
$ cat >> CMakeLists.txt <<EOF
project(demo)

hunter_add_package(print)
find_package(print)

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/sources/demo.cpp)
target_link_libraries(demo print)

install(TARGETS demo RUNTIME DESTINATION bin)
EOF
```

```bash
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
$ cmake --build _build --target install
$ mkdir artifacts && cd artifacts
$ ../_install/demo << "text1 text2 text3"
$ cat log.txt
```

```bash
$ git add artifacts
$ git commit -m"added doxygen.conf"
$ git push origin master
```

## Links

- [polly](https://github.com/ruslo/polly)
- [conan](https://conan.io)
