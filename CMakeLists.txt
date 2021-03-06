cmake_minimum_required(VERSION 2.8.4)
project(TgBot)

### options
option(ENABLE_SAMPLE "Set to ON to enable building of sample echobot" ON)
option(ENABLE_TESTS "Set to ON to enable building of tests" OFF)

### sources
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
include_directories(include)
set(SRC_LIST
    src/Api.cpp
    src/TgTypeParser.cpp
    src/TgException.cpp
    src/net/Url.cpp
    src/net/HttpClient.cpp
    src/net/HttpParser.cpp
    src/net/TgLongPoll.cpp
    src/tools/StringTools.cpp
        src/types/InlineQueryResult.cpp)

### libs
# threads
find_package(Threads REQUIRED)

# openssl
find_package(OpenSSL REQUIRED)
include_directories(${OPENSSL_INCLUDE_DIR})

# boost
set(Boost_USE_MULTITHREADED ON)
find_package(Boost COMPONENTS system iostreams unit_test_framework REQUIRED)
include_directories(${Boost_INCLUDE_DIR})

set(LIB_LIST
    ${CMAKE_THREAD_LIBS_INIT}
    ${OPENSSL_LIBRARIES}
    ${Boost_LIBRARIES}
)

### building project
add_library(${PROJECT_NAME} ${SRC_LIST})
target_link_libraries(${PROJECT_NAME} ${LIB_LIST})
install(TARGETS ${PROJECT_NAME} DESTINATION lib)
install(DIRECTORY include/ DESTINATION include)

### tests
if (ENABLE_TESTS)
    message(STATUS "Building of tests is enabled")
    enable_testing()
    add_subdirectory(test)
endif()

#echobot
if (ENABLE_SAMPLE)
    ADD_SUBDIRECTORY( samples/echobot out/echobot )
endif()