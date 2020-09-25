cmake_minimum_required(VERSION 3.10)

project(DockerClient)

# Add rapidJSON dependency
set(RAPIDJSON_BUILD_TESTS OFF CACHE BOOL "")
set(RAPIDJSON_BUILD_EXAMPLES OFF CACHE BOOL "")
set(RAPIDJSON_BUILD_DOC OFF CACHE BOOL "")

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/rapidjson)
find_package(RapidJSON)


include(FetchContent)
# curl
message(STATUS "Fetching curl")
FetchContent_Declare(curl
        GIT_REPOSITORY https://github.com/curl/curl
        GIT_TAG curl-7_70_0
        GIT_SHALLOW TRUE
        )
if (NOT curl_POPULATE)
    FetchContent_Populate(curl)
    add_subdirectory(${curl_SOURCE_DIR} ${curl_BINARY_DIR})
endif ()

add_library(docker-cpp STATIC "")

set(DOCKER_CLIENT_INCLUDE_DIRS ${CMAKE_CURRENT_SOURCE_DIR} PARENT_SCOPE)
target_include_directories(docker-cpp
        PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}
        PUBLIC ${RapidJSON_INCLUDE_DIR}
        )

target_link_libraries(docker-cpp PUBLIC CURL::libcurl)

target_sources(docker-cpp PRIVATE
        "${CMAKE_CURRENT_SOURCE_DIR}/docker.cpp"
        )
