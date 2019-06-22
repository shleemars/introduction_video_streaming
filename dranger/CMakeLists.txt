cmake_minimum_required(VERSION 3.7)
project(FFmpegHitchhiker C)

set(CMAKE_C_STANDARD 99)

find_package(PkgConfig REQUIRED)
pkg_check_modules(FFMPEG REQUIRED libavcodec libavformat libswscale)
pkg_check_modules(SDL2 REQUIRED sdl2)

link_directories(${FFMPEG_LIBRARY_DIRS} ${SDL2_LIBRARY_DIRS})

add_executable(tutorial01 tutorial01.c)
target_include_directories(tutorial01 PUBLIC ${FFMPEG_INCLUDE_DIRS})
target_link_libraries(tutorial01 PUBLIC ${FFMPEG_LIBRARIES})
target_compile_options(tutorial01 PUBLIC ${FFMPEG_OTHER_CFLAGS})

add_executable(tutorial02 tutorial02.c)
target_include_directories(tutorial02 PUBLIC ${FFMPEG_INCLUDE_DIRS} ${SDL2_INCLUDE_DIRS})
target_link_libraries(tutorial02 PUBLIC ${FFMPEG_LIBRARIES} ${SDL2_LIBRARIES})
target_compile_options(tutorial02 PUBLIC ${FFMPEG_OTHER_CFLAGS} ${SDL2_OTHER_CFLAGS})

add_executable(tutorial03 tutorial03.c)
target_include_directories(tutorial03 PUBLIC ${FFMPEG_INCLUDE_DIRS} ${SDL2_INCLUDE_DIRS})
target_link_libraries(tutorial03 PUBLIC ${FFMPEG_LIBRARIES} ${SDL2_LIBRARIES})
target_compile_options(tutorial03 PUBLIC ${FFMPEG_OTHER_CFLAGS} ${SDL2_OTHER_CFLAGS})
