project(CrashSender)

# Create the list of source files
aux_source_directory( . source_files )
file( GLOB header_files *.h )

# Enable usage of precompiled header
set(srcs_using_precomp ${source_files})
list(REMOVE_ITEM srcs_using_precomp ./stdafx.cpp ./md5.cpp ./base64.cpp)
add_msvc_precompiled_header(stdafx.h ./stdafx.cpp srcs_using_precomp)

list(APPEND source_files	
	./CrashSender.rc 
	${CRASHRPT_ROOT}/reporting/CrashRpt/Utility.cpp 
	${CRASHRPT_ROOT}/reporting/CrashRpt/SharedMem.cpp)
	
# Define _UNICODE (use wide-char encoding)
add_definitions(-D_UNICODE )

fix_default_compiler_settings_()

# Add include dir
include_directories( ${CRASHRPT_ROOT}/include 
                            ${CRASHRPT_ROOT}/reporting/CrashRpt
                            ${CRASHRPT_ROOT}/thirdparty/wtl 
                            ${CRASHRPT_ROOT}/thirdparty/zlib
                            ${CRASHRPT_ROOT}/thirdparty/minizip  
                            ${CRASHRPT_ROOT}/thirdparty/jpeg 
                            ${CRASHRPT_ROOT}/thirdparty/libpng 
                            ${CRASHRPT_ROOT}/thirdparty/tinyxml
                            ${CRASHRPT_ROOT}/thirdparty/dbghelp/include 
							${CRASHRPT_ROOT}/thirdparty/libogg/include
							${CRASHRPT_ROOT}/thirdparty/libtheora/include)

if(NOT CMAKE_CL_64)
	link_directories( ${CRASHRPT_ROOT}/thirdparty/dbghelp/lib )
else(NOT CMAKE_CL_64)
	link_directories( ${CRASHRPT_ROOT}/thirdparty/dbghelp/lib/amd64 )
endif(NOT CMAKE_CL_64)

# Add executable build target
add_executable(CrashSender WIN32 ${source_files} ${header_files})

# Add input link libraries
target_link_libraries(CrashSender zlib minizip libjpeg libpng tinyxml libogg libtheora WS2_32.lib Dnsapi.lib wininet.lib Rpcrt4.lib Gdi32.lib shell32.lib Comdlg32.lib version.lib psapi.lib)

# Add compiler flags (/MP for multi-processor compilation, /Os to favor small code)
set_target_properties(CrashRpt PROPERTIES COMPILE_FLAGS "/Os")
#if(${MSVC_VERSION} GREATER 1310)
#	set_target_properties(CrashRpt PROPERTIES COMPILE_FLAGS "/MP")
#endif(${MSVC_VERSION} GREATER 1310)

set_target_properties(CrashSender PROPERTIES 
				DEBUG_POSTFIX ${CRASHRPT_VER}d
				BETA_POSTFIX ${CRASHRPT_VER} 
				RELEASE_POSTFIX ${CRASHRPT_VER}
				RELWITHDEBINFO_POSTFIX ${CRASHRPT_VER})