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
- cd /home/travis/build/therealmal/lab06/solver_application/_build
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
    secure: "BoCfsNT3PnRhwO3TcoH+u5jLoxwU0Ou5uV8rXtkAbSmGgyKQeVtFdesuPia13WERI0Tw5YDfBfoDE/m/NsYFo2LSuJ++Rd4MRbAUATvQtSUF8ElC2cQBVIASFQ/qTGfJLuTNC0hmA7QwiDGohlfx0SKbSAyjswfQtCAUbi9YHV4BYetUGb3woGnZH6taCaxe3oLhqakYW0uT5PaIDyngT1vcIMtgSQRTwZIcb+ZSVAjpaV+hNyMauckih5geooPUopEKFKmqI9AXaFNuqbydkQkT11Bj1gmyK6mpDnl7sheUY4LtfJJzVePDy3/4ocq4V7Rcsfxkw4PfTooWI9gu9RZIQkXCAB11chP8Nz+/iEuJWQRCD7uwvhhpYdhpWrQumS5S7N5H5dMrMPG8j0Du6g7TIZ2zEIiLQuQj/uJFLhsfPcaXxRKsKu5mCNR7agNIfQBTQj8CYRVczWxPuJynZ6iDUx7RvrW8hjspOXJtDia6tX6poMmtC7nZBnKFfu7ImeO87zc+9TSPcAYFQ6kLQRnh3HVIdXoDQpoEiZ3JwFYmctDcyajHQBVLiXQ5YJwbe6xRgL2bhTO8PrXgJVXQy3HU/NdXJiG95592eNJXyeht88I/CkB/rtfURHKKygGMl3U7NatCQNv0q6f0WjewooVH+fDe0zRa3XPx7H0zefQ="
  file: "/home/travis/build/therealmal/lab06/solver_application/_build/solver-0.1.0.0-Linux.deb"
  skip_cleanup: true
```
