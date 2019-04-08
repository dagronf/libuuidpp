cmake_minimum_required (VERSION 2.6)
set(CMAKE_CXX_STANDARD 17)
project (uuid_test)

add_executable(uuid_test main.cpp)

if (NOT APPLE)
   add_library(libuuid SHARED IMPORTED GLOBAL)
   if (WIN32)
      target_link_libraries (uuid_test libuuid.dll)
   else()
      target_link_libraries (uuid_test libuuid.so)
   endif()
endif()
