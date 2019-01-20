## Laboratory work VII

Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```ShellSession
$ open https://github.com/ruslo/hunter
```

## Tasks

- [ ] 1. Создать публичный репозиторий с названием **lab07** на сервисе **GitHub**
- [ ] 2. Выполнить инструкцию учебного материала
- [ ] 3. Ознакомиться со ссылками учебного материала
- [ ] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=<имя_пользователя>
$ export HUNTER_VERSION=<текущая_версия_пакетного_менеджера|v0.23.89>
```

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```ShellSession
$ wget https://github.com/${GITHUB_USERNAME}/lab06/archive/v0.1.0.0.tar.gz
$ export PRINT_SHA1=`openssl sha1 v0.1.0.0.tar.gz | cut -d'=' -f2 | cut -c2-41`
$ echo $PRINT_SHA1
$ rm -rf v0.1.0.0.tar.gz
```

```ShellSession
$ git clone https://github.com/ruslo/hunter projects/hunter
$ cd projects/hunter && git checkout ${HUNTER_VERSION}
$ git checkout -b package_print
$ git remote show
$ hub fork
$ git remote show
$ git remote show ${GITHUB_USERNAME}
```

```ShellSession
$ mkdir cmake/projects/print
$ cat > cmake/projects/print/hunter.cmake <<EOF
include(hunter_add_version)
include(hunter_cacheable)
include(hunter_cmake_args)
include(hunter_download)
include(hunter_pick_scheme)

hunter_add_version(
    PACKAGE_NAME
    print
    VERSION
    "0.1.0.0"
    URL
    "https://github.com/${GITHUB_USERNAME}/lab06/archive/v0.1.0.0.tar.gz"
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
hunter_cacheable(print)
hunter_download(PACKAGE_NAME print)
EOF
```

```ShellSession
$ cat >> cmake/configs/default.cmake <<EOF
hunter_config(print VERSION 0.1.0.0)
EOF

```

```ShellSession
$ git add .
$ git commit -m"added print package"
$ git push ${GIHUB_USERNAME} package_print
$ git tag ${HUNTER_VERSION}.1
$ git push ${GIHUB_USERNAME} package_print --tags
$ cd ..
```

```ShellSession
$ export HUNTER_ROOT=`pwd`/hunter
$ mkdir lab07 && cd lab07
$ git init
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab07
```

```ShellSession
$ mkdir sources
$ cat > sources/demo.cpp <<EOF
#include <print.hpp>

#include <cstdlib>

int main(int argc, char* argv[])
{
  const char* log_path = std::getenv("LOG_PATH");
  if (log_path == nullptr)
  {
    std::cerr << "undefined environment variable: LOG_PATH" << std::endl; 
  }
  std::string text;
  while(std::cin >> text)
  {
    std::ofstream out{log_path, std::ios_base::app};
    print(text, out);
    out << std::endl;
  }
}
EOF
```

```ShellSession
$ wget https://github.com/hunter-packages/gate/archive/v0.8.1.tar.gz 
$ tar -xzvf v0.8.1.tar.gz gate-0.8.1/cmake/HunterGate.cmake
$ mkdir cmake
$ mv gate-0.8.1/cmake/HunterGate.cmake cmake
$ rm -rf gate*/
$ rm *.tar.gz
```

```ShellSession
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
EOF
```

```ShellSession
$ wget https://github.com/${GITHUB_USERNAME}/hunter/archive/${HUNTER_VERSION}.1.tar.gz
$ export HUNTER_SHA1=`openssl sha1 ${HUNTER_VERSION}.1.tar.gz | cut -d'=' -f2 | cut -c2-41`
$ echo ${HUNTER_SHA1}
$ rm -rf ${HUNTER_VERSION}.1.tar.gz
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF

include(cmake/HunterGate.cmake)

huntergate(
    URL "https://github.com/${GITHUB_USERNAME}/hunter/archive/${HUNTER_VERSION}.1.tar.gz"
    SHA1 "${HUNTER_SHA1}"
)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF

project(demo)

hunter_add_package(print)
find_package(print)

add_executable(demo \${CMAKE_CURRENT_SOURCE_DIR}/sources/demo.cpp)
target_link_libraries(demo print)

install(TARGETS demo RUNTIME DESTINATION bin)
EOF
```

```ShellSession
$ cat > .gitignore <<EOF
*build*/
*install*/
*.swp
EOF
```

```ShellSession
$ cat > README.md <<EOF
[![Build Status](https://travis-ci.org/${GITHUB_USERNAME}/lab07.svg?branch=master)](https://travis-ci.org/${GITHUB_USERNAME}/lab07)
the demo application redirects data from stdin to a file **log.txt** using a package **print**.
EOF
```

```ShellSession
$ cat > .travis.yml <<EOF
language: cpp

script:   
- cmake -H. -B_build
- cmake --build _build
EOF
```

```ShellSession
$ travis lint
```

```ShellSession
$ git add .
$ git commit -m"first commit"
$ git push origin master
```

```ShellSession
$ travis login --auto
$ travis enable
```

```ShellSession
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
$ cmake --build _build --target install
$ mkdir artifacts && cd artifacts
$ echo "text1 text2 text3" | ../_install/bin/demo
$ cat log.txt
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=07
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [polly](https://github.com/ruslo/polly)

```
Copyright (c) 2015-2019 The ISC Authors
```
