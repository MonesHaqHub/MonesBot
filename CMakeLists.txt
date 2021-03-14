cmake_minimum_required(VERSION 3.16)
project(echo_bot)

set(CMAKE_CXX_STANDARD 17)

add_subdirectory(lib/discordpp)           # discordpp
add_subdirectory(lib/rest-beast)          # discordpp-rest-beast
add_subdirectory(lib/websocket-simpleweb) # discordpp-websocket-beast
add_subdirectory(lib/plugin-overload)     # discordpp-plugin-overload
add_subdirectory(lib/plugin-responder)    # discordpp-plugin-responder
add_subdirectory(lib/plugin-ratelimit)    # discordpp-plugin-ratelimit
add_subdirectory(lib/plugin-constructs)   # discordpp-plugin-construct

set(SOURCE_FILES main.cc)
add_executable(${PROJECT_NAME} ${SOURCE_FILES})
target_precompile_headers(${PROJECT_NAME} PUBLIC ${PROJECT_SOURCE_DIR}/include.hh)

#set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} --save-temps")

if (WIN32)
    add_definitions(-D_WIN32_WINNT=0x0601) # Windows 7
endif ()

if (MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /bigobj /wd4250 /wd4244")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /bigobj")
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif ()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -pedantic")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -fsanitize=address -fno-omit-frame-pointer")

INCLUDE_DIRECTORIES(
        ${discordpp_SOURCE_DIR}
        ${discordpp-rest-beast_SOURCE_DIR}
        ${discordpp-websocket-simpleweb_SOURCE_DIR}
        ${discordpp-plugin-overload_SOURCE_DIR}
        ${discordpp-plugin-responder_SOURCE_DIR}
        ${discordpp-plugin-ratelimit_SOURCE_DIR}
)

TARGET_LINK_LIBRARIES(
        ${PROJECT_NAME} PRIVATE
        discordpp
        discordpp-rest-beast
        discordpp-websocket-simpleweb
        discordpp-plugin-constructs
        nlohmann_json::nlohmann_json
)

if (EXISTS ${CMAKE_SOURCE_DIR}/token.dat)
    configure_file(token.dat ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)
elseif (EXISTS ${CMAKE_CURRENT_BINARY_DIR}/token.dat)
    file(REMOVE ${CMAKE_CURRENT_BINARY_DIR}/token.dat)
endif ()
