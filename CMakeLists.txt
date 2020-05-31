cmake_minimum_required(VERSION 3.10)

project(
    serial
        VERSION 1.0.0
        LANGUAGES CXX
)

## Sources
set(serial_SRCS
    src/serial.cc
    include/serial/serial.h
    include/serial/v8stdint.h
)

## Platform dependent sources
if(UNIX)
    list(APPEND serial_SRCS src/impl/unix.cc)
    list(APPEND serial_SRCS src/impl/list_ports/list_ports_linux.cc)
else()
    # If windows
    list(APPEND serial_SRCS src/impl/win.cc)
    list(APPEND serial_SRCS src/impl/list_ports/list_ports_win.cc)
endif() 

## Add library
add_library(${PROJECT_NAME} ${serial_SRCS})

## Link libraries
if(UNIX)
    target_link_libraries(${PROJECT_NAME} rt pthread)
else()
    target_link_libraries(${PROJECT_NAME} setupapi)
endif()

## Include headers
target_include_directories(
    ${PROJECT_NAME}
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)

## Export target to config.cmake
export(
    TARGETS ${PROJECT_NAME}
    NAMESPACE ${PROJECT_NAME}::
    FILE cmake/${PROJECT_NAME}-config.cmake
)

## Install library
install(
    TARGETS
        ${PROJECT_NAME}
    DESTINATION
        lib/serial
)

## Install headers
install(
    FILES
        include/serial/serial.h
     DESTINATION
        include/serial
)

## Install config.cmake
install(
    FILES
        cmake/${PROJECT_NAME}-config.cmake
    DESTINATION
        lib/cmake/serial
)
