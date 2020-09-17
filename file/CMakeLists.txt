project(file_cmd)

cmake_minimum_required(VERSION 2.4.0)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
	cmake_policy(SET CMP0005 NEW)
endif(COMMAND cmake_policy)

include(darling_exe)
add_compile_definitions(
	HAVE_CONFIG_H
	MAGIC="/usr/share/file/magic"
	BUILTIN_MACHO
)

add_definitions(
	-Wno-non-literal-null-conversion
	-Wno-format-security
)

include_directories(BEFORE ${CMAKE_CURRENT_SOURCE_DIR}/..)

set(libmagic_sources
	src/apprentice.c
	src/apptype.c
	src/ascmagic.c
	src/cdf.c
	src/cdf_time.c
	src/compress.c
	src/encoding.c
	src/fsmagic.c
	src/der.c
	src/funcs.c
	src/is_tar.c
	src/magic.c
	src/print.c
	src/is_json.c
	src/buffer.c
	src/readcdf.c
	src/readelf.c
	src/readmacho.c
	src/softmagic.c
)

add_darling_static_library(magic FAT SOURCES ${libmagic_sources})
# The library is not present on real macOS
#install(TARGETS magic
#	DESTINATION libexec/darling/usr/lib)

if (NOT DARLING_NO_EXECUTABLES)
	add_darling_executable(file src/file.c)
	target_link_libraries(file
		magic
		z

		system
	)

	install(TARGETS file
		DESTINATION libexec/darling/usr/bin)

	install(FILES ../gen/magic.mgc
		DESTINATION libexec/darling/usr/share/file)
	install(DIRECTORY magic/Magdir/
		DESTINATION libexec/darling/usr/share/file/magic)

	install(FILES ../gen/file.1 DESTINATION libexec/darling/usr/share/man/man1)
	install(FILES ../gen/libmagic.3 DESTINATION libexec/darling/usr/share/man/man3)
	install(FILES ../gen/magic.5 DESTINATION libexec/darling/usr/share/man/man5)

endif (NOT DARLING_NO_EXECUTABLES)

