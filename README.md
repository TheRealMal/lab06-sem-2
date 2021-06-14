[![Build Status](https://travis-ci.com/therealmal/lab06.svg)](https://travis-ci.com/therealmal/lab06)

## TP Labs 06
`therealmal | Малютин Роман ИУ8-12`  
Повторяя команды из туториала, заменяя описание солвера и конфиг, получаем такой CMakeLists:

```
cmake_minimum_required(VERSION 3.4)
project(formatter)
set(SOLV_VERSION_MAJOR 0)
set(SOLV_VERSION_MINOR 1)
set(SOLV_VERSION_PATCH 0)
set(SOLV_VERSION_TWEAK 0)
set(SOLV_VERSION
  ${SOLV_VERSION_MAJOR}.${SOLV_VERSION_MINOR}.${SOLV_VERSION_PATCH}.${SOLV_VERSION_TWEAK})
set(SOLV_VERSION_STRING "v${SOLV_VERSION}")

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter STATIC formatter_lib/formatter.cpp)
include_directories(formatter_lib)

add_library(formatter_ex STATIC formatter_ex_lib/formatter_ex.cpp)
target_link_libraries(formatter_ex formatter)
include_directories(formatter_ex_lib)

add_executable(hello_world hello_world_application/hello_world.cpp)
target_link_libraries(hello_world formatter formatter_ex)

add_library(solver_lib STATIC solver_lib/solver.cpp)
include_directories(solver_lib)

add_executable(solver solver_application/equation.cpp)
target_link_libraries(solver formatter formatter_ex solver_lib)

include(CPackConfig.cmake)
```
И вот такой CPackConfig.cmake

```
include(InstallRequiredSystemLibraries)
set(CPACK_PACKAGE_CONTACT therealmal23@gmail.com)
set(CPACK_PACKAGE_VERSION_MAJOR ${SOLV_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${SOLV_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${SOLV_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK ${SOLV_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION ${SOLV_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE ${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")

set(CPACK_RESOURCE_FILE_LICENSE ${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README ${CMAKE_CURRENT_SOURCE_DIR}/README.md)
set(CPACK_RPM_PACKAGE_NAME "solv-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "solv")
set(CPACK_RPM_CHANGELOG_FILE ${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
set(CPACK_DEBIAN_PACKAGE_NAME "solv-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)

include(CPack)
```
Выполняем сборку проекта и собираем архив
```
$ cmake -H. -B_build
$ cmake --build _build
$ cd _build
$ cpack -G DEB
$ cd ..
```

После надо заполнить .travis.yml
Для получения кода используем travis encrypt <Токен с правами repo>
```
language: cpp

compiler:
- gcc
- clang
os:
 - linux

jobs:
  include:
  - name: "all projects"
    script:
    - cmake -H. -B_build
    - cmake --build _build
  - name: "each CMakeLists.txt"

script:
- source ./script
- cd /home/travis/build/TheRealMal/lab06/solver_application/_build
- cpack -G DEB
    

addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data

deploy:
  provider: releases
  api_key:
    secure: "UZzlaD1cVp4IxQDLKRVn2Z12Tvf0QKhBmoWDZULPxGLtF8my9JisbkpJAVghbG0IPTTvUACIq5MX2u+TQEwWdgMYqrfoETiHv8d8xKvm2C9PCm0Snbwx8BgLi2rbGCpSyvxKmrklyYXqHwCdrFHY/a9widVp/zkPm3LCNl7jnj+1GOtphAVoYq0ej4SJcyvEY9bSRjPYeDo+AWCzWZgPLeRQj+VxZhtQbDV5oSYb0NwKAYqB60TQhznAlJUWnsxeQp3Dm9pXj8iBpj0INacjQxCsfBt+SRgOlO2reXzXpiiMfXeD4N9Mf0/aSzlykXuuMeH0eC15LzKbhYzG4mzAr2iui7sfIoMvmAbEHbvWq/2lGD+iz0/c6RF0X5PG9UbNUhe+Fo8FNl8pc1K56FYnCcTFRWYAjfbQhr6pJX2r/aoA1NWD0yQsbHnFDogMfClkreyTgi1D8lqNsraeHCkq9lSpn5OLRi7J0fSdrQolxaW7MgcxFeE69vfh8M8ffhBOSCe8iXjZbq/CmyWuiIzbfkQ12jNAHV7LmhGs4ODawE46yNPrzKHmeuVVnhFzlXrfHwUW9fByqGpRqV3ixI/+C2QTDYasrDToN43vq1dq4vjqI7TJWACD/Rw/pW9tJSYAE1GGyq0F72bqqj8Hfy6Q/dXvCklpXeO4a+HgbHfl6TM="  file: "/home/travis/build/TheRealMal/lab06/solver_application/_build/solver-0.1.0.0-Linux.deb"
  skip_cleanup: true
  on:
    branch: main
```
