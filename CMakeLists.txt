cmake_minimum_required(VERSION 3.10)

set(PROJECT_NAME
        cpphttp)

project(${PROJECT_NAME})

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_BUILD_TYPE Debug)

list(APPEND CMAKE_MODULE_PATH 
    "${CMAKE_CURRENT_LIST_DIR}/cmake")


option(RANDOM_FAIL "Make unit tests fail at random" OFF)


set(BUILD_ORM FALSE CACHE INTERNAL "")
set(BUILD_CTL FALSE CACHE INTERNAL "")
set(BUILD_EXAMPLES FALSE CACHE INTERNAL "")

set(JSONCPP_WITH_TESTS OFF CACHE INTERNAL "")

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR} CACHE PATH "" FORCE)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
#set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})



add_subdirectory(ext/jsoncpp)
add_subdirectory(ext/drogon)

if(RANDOM_FAIL)
    add_compile_definitions(RANDOM_FAIL=1)
endif()


# this find will just give as interface target
# Drogon::Drogon
# it does not really checks for drogon libraries presence
# it assumes they exists
find_package(Drogon)

add_executable(${PROJECT_NAME}
        main.cpp
        src/controllers/InfoCtrl.cpp)

target_include_directories(${PROJECT_NAME}
    PRIVATE 
        ext/)


    #set(CMAKE_EXE_LINKER_FLAGS "-static-libgcc -static-libstdc++")


target_link_libraries(${PROJECT_NAME} 
    PRIVATE 
        Drogon::Drogon)

if (${BUILD_TESTS})
    enable_testing()
    add_subdirectory(ext/googletest)
    add_subdirectory(tests)
endif()

