cmake_minimum_required(VERSION 3.18)
project(laya LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(LAYA_SOURCES
    src/questions.cpp
    src/sequence.cpp
    src/presets.cpp
    src/language.cpp
    src/router.cpp
    src/engine.cpp
    src/server.cpp
    src/main.cpp
)

add_executable(laya ${LAYA_SOURCES})

target_include_directories(laya PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_SOURCE_DIR}/../../runtime/include
    ${CMAKE_CURRENT_SOURCE_DIR}/../../third_party/ggml/include
    ${CMAKE_CURRENT_SOURCE_DIR}/../../third_party/ggml/src
)

target_link_libraries(laya PRIVATE ggmlc_runtime)

if (GGMLC_ENABLE_AMDXDNA)
    file(RELATIVE_PATH _laya_library_dir
        "${CMAKE_INSTALL_FULL_BINDIR}" "${CMAKE_INSTALL_FULL_LIBDIR}")
    get_filename_component(_laya_xrt_library_dir "${GGMLC_XRT_COREUTIL_LIBRARY}" DIRECTORY)
    set_target_properties(laya PROPERTIES
        INSTALL_RPATH "$ORIGIN/${_laya_library_dir};${_laya_xrt_library_dir}"
        INSTALL_RPATH_USE_LINK_PATH FALSE)
endif()
install(TARGETS laya RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}" COMPONENT Laya)

if (WIN32)
    target_link_libraries(laya PRIVATE ws2_32)
endif()
