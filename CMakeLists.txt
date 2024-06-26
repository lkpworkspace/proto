cmake_minimum_required(VERSION 3.10)
if (POLICY CMP0091)
  cmake_policy(SET CMP0091 NEW)
endif()
project(myframe_pb VERSION 0.1.2)

#### cmake module
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

#### compile option
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED YES)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
add_compile_options("$<$<CXX_COMPILER_ID:MSVC>:/source-charset:utf-8>")

#### path setting
set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")
set(MYFRAME_INSTALL_LIB_DIR ${CMAKE_INSTALL_PREFIX}/lib)
set(MYFRAME_INSTALL_HEADER_DIR ${CMAKE_INSTALL_PREFIX}/include/myframe_pb)

find_package(Protobuf REQUIRED)

include_directories(${Protobuf_INCLUDE_DIRS})

### PROTO
file(GLOB proto_files "proto/*.proto")
PROTOBUF_GENERATE_CPP(PROTO_SRCS PROTO_HDRS ${proto_files})

add_library(myframe_pb SHARED ${PROTO_SRCS} ${PROTO_HDRS})
target_link_libraries(myframe_pb
    PUBLIC
    ${Protobuf_LIBRARIES}
)
target_include_directories(myframe_pb
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    INTERFACE
    $<INSTALL_INTERFACE:include>
)

### install
install(TARGETS
    myframe_pb
    EXPORT "${PROJECT_NAME}Targets"
    LIBRARY DESTINATION ${MYFRAME_INSTALL_LIB_DIR}
    ARCHIVE DESTINATION ${MYFRAME_INSTALL_LIB_DIR}
    RUNTIME DESTINATION ${MYFRAME_INSTALL_LIB_DIR}
)
install(DIRECTORY proto DESTINATION ${MYFRAME_INSTALL_HEADER_DIR})
install(FILES
    ${PROTO_HDRS}
    PERMISSIONS
    OWNER_READ
    GROUP_READ
    WORLD_READ
    DESTINATION ${MYFRAME_INSTALL_HEADER_DIR}
)

include(InstallingConfigs)
