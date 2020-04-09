cmake_minimum_required(VERSION 2.8.11)
cmake_policy(SET CMP0003 NEW)
project(picoquic C CXX)
find_package (Threads REQUIRED)

set(CMAKE_C_STANDARD 11)

set(CMAKE_C_FLAGS "-std=c99 -Wall -Werror -O2 -g ${CC_WARNING_FLAGS} ${CMAKE_C_FLAGS}")

if(DISABLE_DEBUG_PRINTF)
    set(CMAKE_C_FLAGS "-DDISABLE_DEBUG_PRINTF ${CMAKE_C_FLAGS}")
endif()

set(PICOQUIC_LIBRARY_FILES
    picoquic/bbr.c
    picoquic/bytestream.c
	picoquic/cc_common.c
    picoquic/cubic.c
    picoquic/fastcc.c
    picoquic/frames.c
    picoquic/intformat.c
    picoquic/logger.c
    picoquic/logwriter.c
    picoquic/newreno.c
    picoquic/packet.c
    picoquic/picohash.c
    picoquic/picosocks.c
    picoquic/picosplay.c
    picoquic/quicctx.c
    picoquic/sacks.c
    picoquic/sender.c
    picoquic/sim_link.c
    picoquic/spinbit.c
    picoquic/ticket_store.c
    picoquic/token_store.c
    picoquic/tls_api.c
    picoquic/transport.c
    picoquic/util.c
)

set(PICOHTTP_LIBRARY_FILES
    picohttp/democlient.c
    picohttp/demoserver.c
    picohttp/h3zero.c
	picohttp/siduck.c
)

set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
find_package(PTLS REQUIRED)
message(STATUS "picotls/include: ${PTLS_INCLUDE_DIRS}" )
message(STATUS "picotls libraries: ${PTLS_LIBRARIES}" )

find_package(OpenSSL )
message(STATUS "root: ${OPENSSL_ROOT_DIR}")
message(STATUS "OpenSSL_VERSION: ${OPENSSL_VERSION}")
message(STATUS "OpenSSL_INCLUDE_DIR: ${OPENSSL_INCLUDE_DIR}")
message(STATUS "OpenSSL_LIBRARIES: ${OPENSSL_LIBRARIES}")

include_directories(picoquic
    ${PTLS_INCLUDE_DIRS} ${OPENSSL_INCLUDE_DIR})

add_library(picoquic-core
    ${PICOQUIC_LIBRARY_FILES}
)

add_library(picohttp-core
    ${PICOHTTP_LIBRARY_FILES}
)

add_executable(picoquicdemo
    picoquicfirst/picoquicdemo.c
    picoquicfirst/getopt.c
)

target_link_libraries(picoquicdemo
    picoquic-core
    picohttp-core
    ${PTLS_LIBRARIES}
    ${OPENSSL_LIBRARIES}
    ${CMAKE_DL_LIBS}
    ${CMAKE_THREAD_LIBS_INIT}
)

target_include_directories(picoquicdemo PRIVATE picohttp)

# get all project files for formatting
file(GLOB_RECURSE CLANG_FORMAT_SOURCE_FILES *.c *.h)

# Adds clangformat as target that formats all source files
add_custom_target(
    clangformat
    COMMAND clang-format
    -style=Webkit
    -i
    ${CLANG_FORMAT_SOURCE_FILES}
)
