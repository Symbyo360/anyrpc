cmake_minimum_required(VERSION 3.9)

project(anyrpc VERSION 1.0.2 LANGUAGES CXX)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>")

# list the header files
set(HEADER_FILES
	include/anyrpc/anyrpc.h
	include/anyrpc/api.h
	include/anyrpc/client.h
	include/anyrpc/connection.h
	include/anyrpc/document.h
	include/anyrpc/error.h
	include/anyrpc/handler.h
	include/anyrpc/logger.h
	include/anyrpc/method.h
	include/anyrpc/reader.h
	include/anyrpc/server.h
	include/anyrpc/socket.h
	include/anyrpc/stream.h
	include/anyrpc/value.h
	include/anyrpc/version.h
	include/anyrpc/internal/base64.h
	include/anyrpc/internal/http.h
	include/anyrpc/internal/inttypes.h
	include/anyrpc/internal/mingw.condition_variable.h
	include/anyrpc/internal/mingw.mutex.h
	include/anyrpc/internal/mingw.thread.h
	include/anyrpc/internal/stdint.h
	include/anyrpc/internal/strtod.h
	include/anyrpc/internal/time.h
	include/anyrpc/internal/unicode.h
	include/anyrpc/json/jsonclient.h
	include/anyrpc/json/jsonreader.h
	include/anyrpc/json/jsonserver.h
	include/anyrpc/json/jsonwriter.h
	include/anyrpc/messagepack/messagepackclient.h
	include/anyrpc/messagepack/messagepackformat.h
	include/anyrpc/messagepack/messagepackreader.h
	include/anyrpc/messagepack/messagepackserver.h
	include/anyrpc/messagepack/messagepackwriter.h
	include/anyrpc/messagepack/sysdep.h
	include/anyrpc/xml/xmlclient.h
	include/anyrpc/xml/xmlreader.h
	include/anyrpc/xml/xmlserver.h
	include/anyrpc/xml/xmlwriter.h
)

# list the source files
set(SOURCE_FILES
	src/client.cpp
	src/CMakeLists.txt
	src/connection.cpp
	src/document.cpp
	src/handler.cpp
	src/logger.cpp
	src/method.cpp
	src/reader.cpp
	src/server.cpp
	src/socket.cpp
	src/stream.cpp
	src/value.cpp
	src/internal/base64.cpp
	src/internal/http.cpp
	src/internal/time.cpp
	src/internal/unicode.cpp
	src/json/jsonclient.cpp
	src/json/jsonreader.cpp
	src/json/jsonserver.cpp
	src/json/jsonwriter.cpp
	src/messagepack/messagepackclient.cpp
	src/messagepack/messagepackreader.cpp
	src/messagepack/messagepackserver.cpp
	src/messagepack/messagepackwriter.cpp
	src/xml/xmlclient.cpp
	src/xml/xmlreader.cpp
	src/xml/xmlserver.cpp
	src/xml/xmlwriter.cpp
)


# add library target
add_library(anyrpc
	${HEADER_FILES}
	${SOURCE_FILES}
)

add_library(AnyRPC::anyrpc ALIAS anyrpc)

# make it reflect the same structure as the one on disk
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${HEADER_FILES})
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${SOURCE_FILES})


# Windows specfic flags to enable W variations of winapi
if(WIN32)
	target_link_libraries(anyrpc
		PRIVATE
			ws2_32
	)
endif()

# enable C++17
# disable any compiler specifc extensions
# add d suffix in debug mode
target_compile_features(anyrpc PUBLIC cxx_std_17)
set_target_properties(anyrpc PROPERTIES
	CXX_EXTENSIONS OFF
	DEBUG_POSTFIX d
)

if(BUILD_SHARED_LIBS)
	target_compile_definitions(anyrpc
		PRIVATE
			ANYRPC_DLL_BUILD=1
		INTERFACE
			ANYRPC_DLL=1
	)
endif(BUILD_SHARED_LIBS)

# define debug macro
target_compile_definitions(anyrpc PRIVATE "$<$<CONFIG:DEBUG>:DEBUG>")

# list include directories
target_include_directories(anyrpc
	PUBLIC
	${CMAKE_CURRENT_SOURCE_DIR}/include
	PRIVATE
	${CMAKE_CURRENT_SOURCE_DIR}/src
)
