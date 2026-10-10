cmake_minimum_required(VERSION 3.21)
project(YKKZ000DllPluginApi LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(NOT CMAKE_SIZEOF_VOID_P EQUAL 8)
  message(FATAL_ERROR "x64 builds only")
endif()

if(MSVC)
  # Static CRT: eliminates the VCRUNTIME140_1.dll dependency, preventing the proxy DLL from
  # blocking game startup when a newer VC++ runtime is missing.
  set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
  add_compile_options(/W4 /permissive- /Zc:__cplusplus /utf-8 /EHsc)
endif()

# Convenience for collecting artifacts uniformly (not deployment)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin")
foreach(cfg DEBUG RELEASE RELWITHDEBINFO MINSIZEREL)
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_${cfg} "${CMAKE_BINARY_DIR}/bin")
endforeach()

# Shared headers: INTERFACE target, propagates the include directory through linking. The API plugins
# link no third-party libraries (see natives/vcpkg.json).
add_library(ykkz000_bridge INTERFACE)
target_include_directories(ykkz000_bridge INTERFACE
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>")
target_compile_features(ykkz000_bridge INTERFACE cxx_std_20)

# Shared plugin APIs. Each is a real plugin (it exports GetPlugin/GetPluginManifest) so the loader
# initializes it before the consumers that declare it as a manifest dependency.
add_subdirectory(plugin-api-effecttype)
add_subdirectory(plugin-api-persistence)
