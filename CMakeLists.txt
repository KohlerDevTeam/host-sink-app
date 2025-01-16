cmake_minimum_required(VERSION  3.16.3)
project(connect_host_app)
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
include(GNUInstallDirs)

include(CheckCCompilerFlag)
check_c_compiler_flag(-Wall                  CC_HAVE_WALL)
check_c_compiler_flag(-Wno-stringop-overread CC_HAVE_WNO_STRINGOP_OVERREAD)
check_c_compiler_flag(-Wno-stringop-overflow CC_HAVE_WNO_STRINGOP_OVERFLOW)


# Compile options
if(CC_HAVE_WALL)
    add_compile_options(-Wall)
endif()
if(CC_HAVE_WNO_STRINGOP_OVERREAD)
    add_compile_options(-Wno-stringop-overread)
endif()
if(CC_HAVE_WNO_STRINGOP_OVERFLOW)
    add_compile_options(-Wno-stringop-overflow)
endif()

add_definitions(-DSL_CATALOG_CONNECT_AES_SECURITY_PRESENT
                )
add_compile_options(-I/root/parts/ncp-connect/src/include)
link_directories(/root/stage/usr/local/lib)

# Source files and executable
#find_package(Boost 1.70.0 REQUIRED COMPONENTS system
find_package(Boost 1.70.0 REQUIRED)

include(FetchContent)
FetchContent_Declare(
  cli
  GIT_REPOSITORY https://github.com/daniele77/cli.git
  GIT_TAG v2.0.2
)
FetchContent_MakeAvailable(cli)

add_executable(host_sink_app 
               app_init.c
               app_process.c
               app_cli.cpp
               app_common.c
               cli_commands.cpp
               main.cpp
               )

target_include_directories(host_sink_app PUBLIC
               ${CMAKE_CURRENT_SOURCE_DIR}
               ./
               config
               inc
               )

# External libraries
target_link_libraries(host_sink_app PUBLIC connecthost)
target_link_libraries(host_sink_app PRIVATE cli::cli)