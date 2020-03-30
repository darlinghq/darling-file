project(file_cmd)

cmake_minimum_required(VERSION 2.4.0)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
	cmake_policy(SET CMP0005 NEW)
endif(COMMAND cmake_policy)

include(darling_exe)
add_definitions(-DHAVE_CONFIG_H -DMAGIC="/usr/share/file/magic")

add_definitions(
	-Wno-non-literal-null-conversion
	-Wno-format-security
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D_POSIX_C_SOURCE -nostdinc -fPIC")

include_directories(BEFORE ${CMAKE_CURRENT_SOURCE_DIR}/..)
include_directories(${CMAKE_SOURCE_DIR}/src/libc/include/FreeBSD)
include_directories(${CMAKE_SOURCE_DIR}/src/libc/include/NetBSD)
include_directories(${CMAKE_SOURCE_DIR}/src/libc/gen)
include_directories(${CMAKE_SOURCE_DIR}/src/libc/darwin)
include_directories(${CMAKE_SOURCE_DIR}/src/libc/stdtime/FreeBSD)
include_directories(${CMAKE_SOURCE_DIR}/src/launchd/liblaunch)
include_directories(${CMAKE_SOURCE_DIR}/src/external/libdispatch)
include_directories(${CMAKE_SOURCE_DIR}/src/external/zlib)
include_directories(${CMAKE_SOURCE_DIR}/src/external/bzip2)

set(libmagic_sources
	src/apprentice.c
	src/apptype.c
	src/ascmagic.c
	src/cdf.c
	src/cdf_time.c
	src/compress.c
	src/encoding.c
	src/fsmagic.c
	src/funcs.c
	src/is_tar.c
	src/magic.c
	src/print.c
	src/readcdf.c
	src/readelf.c
	src/readmacho.c
	src/softmagic.c
)

add_darling_object_library(magic ${libmagic_sources})
# The library is not present on real macOS
#target_link_libraries(magic system)
#install(TARGETS magic
#	DESTINATION libexec/darling/usr/lib)

if (NOT DARLING_NO_EXECUTABLES)
	add_darling_executable(file src/file.c
		$<TARGET_OBJECTS:magic>)
	target_link_libraries(file system z)

	install(TARGETS file
		DESTINATION libexec/darling/usr/bin)

	install(FILES magic/magic.mgc
		DESTINATION libexec/darling/usr/share/file)
	install(DIRECTORY magic/Magdir/
		DESTINATION libexec/darling/usr/share/file/magic)

	install(FILES ../file.1 DESTINATION libexec/darling/usr/share/man/man1)
	install(FILES ../magic.5 DESTINATION libexec/darling/usr/share/man/man5)

endif (NOT DARLING_NO_EXECUTABLES)

