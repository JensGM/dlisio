set(CMAKE_CXX_STANDARD 11)

add_library(dlisio src/dlisio.cpp)
target_include_directories(dlisio
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
           $<INSTALL_INTERFACE:include>
)
target_compile_options(dlisio
    BEFORE
    PRIVATE $<$<CONFIG:Debug>:${warnings-c++}>
)
target_compile_definitions(dlisio
    PRIVATE ${htons}
)
target_link_libraries(dlisio
    PUBLIC ${ws2}
)

install(TARGETS dlisio
        EXPORT dlisio
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
install(DIRECTORY include/ DESTINATION include)
install(EXPORT dlisio DESTINATION share/dlisio/cmake FILE dlisio-config.cmake)
export(TARGETS dlisio FILE dlisio-config.cmake)

if(NOT BUILD_TESTING)
    return()
endif()

add_executable(testsuite test/testsuite.cpp
                         test/storage-unit-label.cpp
                         test/visible-record-label.cpp
                         test/logical-record-segment-header.cpp
                         test/encryption-packet.cpp
                         test/types.cpp
)
target_link_libraries(testsuite dlisio catch2)
add_test(NAME core COMMAND testsuite)
