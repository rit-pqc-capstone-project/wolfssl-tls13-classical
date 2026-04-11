cmake_minimum_required(VERSION 3.16)
project(tls13-hybrid C)

# Force CMake to look for static libraries (.a) only for the next search
set(CMAKE_FIND_LIBRARY_SUFFIXES ".a")

# Locate the wolfSSL headers
find_path(WOLFSSL_INCLUDE_DIR 
    NAMES wolfssl/options.h wolfssl/ssl.h
    HINTS ${WOLFSSL_ROOT}/include
    REQUIRED
)

# Locate the static library specifically
find_library(WOLFSSL_LIBRARY 
    NAMES wolfssl
    HINTS ${WOLFSSL_ROOT}/lib ${WOLFSSL_ROOT}/lib64
    REQUIRED
)

# --- Build Server ---
add_executable(server server.c)
target_include_directories(server PRIVATE ${WOLFSSL_INCLUDE_DIR})
target_link_libraries(server PRIVATE ${WOLFSSL_LIBRARY} pthread m)

# --- Build Client ---
add_executable(client client.c)
target_include_directories(client PRIVATE ${WOLFSSL_INCLUDE_DIR})
target_link_libraries(client PRIVATE ${WOLFSSL_LIBRARY} pthread m)