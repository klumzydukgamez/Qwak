cmake_minimum_required(VERSION 3.25)
project(
	Qwak
	VERSION 0.1.0
	LANGUAGES C
)

set(CMAKE_C_STANDARD          23)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS        OFF)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(Qwak_CORE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/Core")
set(Qwak_CORE_SOURCE_DIR "${Qwak_CORE_DIR}/Source")
set(Qwak_CORE_INCLUDE_DIR "${Qwak_CORE_DIR}/Include")
set(Qwak_TOOLS_DIR "${CMAKE_CURRENT_SOURCE_DIR}/Tools")
set(Qwak_TOOLS_SOURCE_DIR "${Qwak_TOOLS_DIR}/Source")
set(Qwak_TESTS_DIR "${CMAKE_CURRENT_SOURCE_DIR}/Tests")
set(Qwak_TESTS_SOURCE_DIR "${Qwak_TESTS_DIR}/Source")

add_library(QwakCore STATIC)
set_target_properties(
	QwakCore PROPERTIES
	OUTPUT_NAME "QwakCore"
)
file(
	GLOB_RECURSE Qwak_CORE_SOURCES
	CONFIGURE_DEPENDS
	"${Qwak_CORE_SOURCE_DIR}/*.c"
)
target_sources(
	QwakCore PRIVATE
	${Qwak_CORE_SOURCES}
)
target_include_directories(
	QwakCore PRIVATE
	${Qwak_CORE_SOURCE_DIR}
)
target_include_directories(
	QwakCore PUBLIC
	${Qwak_CORE_INCLUDE_DIR}
)

file(
	GLOB_RECURSE Qwak_TOOLS_SOURCES
	RELATIVE ${Qwak_TOOLS_SOURCE_DIR}
	"${Qwak_TOOLS_SOURCE_DIR}/*.c"
)
foreach(TOOL_SOURCE IN LISTS Qwak_TOOLS_SOURCES)
	get_filename_component(
		TOOL_SOURCE_WO_EXTENSION
		${TOOL_SOURCE} NAME_WE
	)
	set(TOOL_NAME "Qwak${TOOL_SOURCE_WO_EXTENSION}")
	add_executable(${TOOL_NAME})
	set_target_properties(
		${TOOL_NAME} PROPERTIES
		OUTPUT_NAME ${TOOL_NAME}
	)
	target_sources(
		${TOOL_NAME} PRIVATE
		"${Qwak_TOOLS_SOURCE_DIR}/${TOOL_SOURCE}"
	)
	target_link_libraries(
		${TOOL_NAME} PRIVATE
		QwakCore
	)
endforeach()

file(
	GLOB_RECURSE Qwak_TESTS_SOURCES
	RELATIVE ${Qwak_TESTS_SOURCE_DIR}
	"${Qwak_TESTS_SOURCE_DIR}/*.c"
)
foreach(TEST_SOURCE IN LISTS Qwak_TESTS_SOURCES)
	get_filename_component(
		TEST_SOURCE_WO_EXTENSION
		${TEST_SOURCE} NAME_WE
	)
	set(TEST_NAME "QwakTest${TEST_SOURCE_WO_EXTENSION}")
	add_executable(${TEST_NAME})
	set_target_properties(
		${TEST_NAME} PROPERTIES
		OUTPUT_NAME ${TEST_NAME}
	)
	target_sources(
		${TEST_NAME} PRIVATE
		"${Qwak_TESTS_SOURCE_DIR}/${TEST_SOURCE}"
	)
	target_link_libraries(
		${TEST_NAME} PRIVATE
		QwakCore
	)
endforeach()
