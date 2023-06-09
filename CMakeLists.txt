CMAKE_MINIMUM_REQUIRED(VERSION 3.10)

PROJECT("L0phtCrack 7")

INCLUDE(cmake/MakeDist.cmake)

# Add new build types
message("* Adding build types...")
SET(CMAKE_CXX_FLAGS_BETA
    "${CMAKE_CXX_FLAGS_RELWITHDEBINFO} -DBETA=1"
    CACHE STRING "Flags used by the C++ compiler during beta builds."
    FORCE )
SET(CMAKE_C_FLAGS_BETA
    "${CMAKE_C_FLAGS_RELWITHDEBINFO} -DBETA=1"
    CACHE STRING "Flags used by the C compiler during beta builds."
    FORCE )
SET(CMAKE_EXE_LINKER_FLAGS_BETA
    "${CMAKE_LINKER_FLAGS_RELWITHDEBINFO} /DEBUG"
    CACHE STRING "Flags used for linking binaries during beta builds."
    FORCE )
SET(CMAKE_SHARED_LINKER_FLAGS_BETA
    "${CMAKE_SHARED_LINKER_FLAGS_RELWITHDEBINFO} /DEBUG"
    CACHE STRING "Flags used by the shared libraries linker during beta builds."
    FORCE )

MARK_AS_ADVANCED(
    CMAKE_CXX_FLAGS_BETA
    CMAKE_C_FLAGS_BETA
    CMAKE_EXE_LINKER_FLAGS_BETA
    CMAKE_SHARED_LINKER_FLAGS_BETA )

IF( MSVC )
  SET ( CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} /MANIFEST:NO")
	SET ( CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /MANIFEST:NO")

	# Add global optimization flags
	# XXX: Switch to /O2 when we pick a compiler that doesn't fucking hang 'Generating code'
	SET( CMAKE_CXX_FLAGS_BETA "${CMAKE_CXX_FLAGS_BETA} /Ob2 /Oi /Os /Oy" )
	SET( CMAKE_C_FLAGS_BETA "${CMAKE_C_FLAGS_BETA} /Ob2 /Oi /Os /Oy" )
	SET( CMAKE_CXX_FLAGS_RELWITHDEBINFO "${CMAKE_CXX_FLAGS_RELWITHDEBINFO} /Ob2 /Oi /Os /Oy" )		
	SET( CMAKE_C_FLAGS_RELWITHDEBINFO "${CMAKE_C_FLAGS_RELWITHDEBINFO} /Ob2 /Oi /Os /Oy" )

	if(CMAKE_CONFIGURATION_TYPES)
		set(CMAKE_CONFIGURATION_TYPES Debug RelWithDebInfo Beta)
		set(CMAKE_CONFIGURATION_TYPES "${CMAKE_CONFIGURATION_TYPES}" CACHE STRING
			"Reset the configurations to what we need" FORCE)
	endif()

	IF("${CMAKE_SIZEOF_VOID_P}" EQUAL "8")
		SET(LC7_PLATFORM "win64") 
		SET(LC7_PLATFORM_BITS "64")
	ELSE()
		SET(LC7_PLATFORM "win32") 	
		SET(LC7_PLATFORM_BITS "32")
	ENDIF()
	
ENDIF( MSVC )

IF(MSVC)
	SET(JTREXE_USE_CRASHDUMP 1)
	SET(JTREXE_CRASHDUMP_INCLUDES "/I${CMAKE_CURRENT_SOURCE_DIR}/lc7core/include /I${CMAKE_CURRENT_SOURCE_DIR}/lc7jtr/include /I${CMAKE_CURRENT_SOURCE_DIR}/lc7/include /I${CMAKE_CURRENT_SOURCE_DIR}/external/crashrpt/include")
	SET(JTREXE_CRASHDUMP_LIBRARIES CrashRpt)

	set_property(GLOBAL PROPERTY USE_FOLDERS ON)
ENDIF()

ADD_SUBDIRECTORY(external/crashrpt)
ADD_SUBDIRECTORY(external/qtkeychain)
ADD_SUBDIRECTORY(external/qtpropertybrowser)
ADD_SUBDIRECTORY(external/qtsingleapplication)
ADD_SUBDIRECTORY(external/jtrdll/jtrdll)
ADD_SUBDIRECTORY(external/quazip)
option(BUILD_EXAMPLES "" OFF)
option(BUILD_TESTING "" OFF)
ADD_SUBDIRECTORY(external/libssh2)
ADD_SUBDIRECTORY(lc7jtr)
ADD_SUBDIRECTORY(lc7core)
ADD_SUBDIRECTORY(lc7base)
ADD_SUBDIRECTORY(lc7password)
ADD_SUBDIRECTORY(lc7reports)
ADD_SUBDIRECTORY(lc7wizard)
ADD_SUBDIRECTORY(lc7importwin)
ADD_SUBDIRECTORY(lc7importunix)
ADD_SUBDIRECTORY(lc7)

# Agent must be built using visual studio solution
#ADD_SUBDIRECTORY(lc7agent)
#ADD_SUBDIRECTORY(lc7dump)
	
FUNCTION(SET_LC7_TARGET_PROPERTIES_CONFIG SUFFIX DIST)

	SET_TARGET_PROPERTIES( qt5keychain
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
	)

	SET_TARGET_PROPERTIES( qtpropertybrowser
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
	)

	SET_TARGET_PROPERTIES( qtsingleapplication
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
	)

	SET_TARGET_PROPERTIES( quazip
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
	)

	IF( NOT(${LC7_PLATFORM} STREQUAL "win32"))
		SET_TARGET_PROPERTIES( jtrdll_xop
			PROPERTIES
			LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
			RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
		)

		SET_TARGET_PROPERTIES( jtrdll_avx
			PROPERTIES
			LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
			RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
		)

		SET_TARGET_PROPERTIES( jtrdll_avx2
			PROPERTIES
			LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
			RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
		)
	ENDIF()

	SET_TARGET_PROPERTIES( jtrdll_sse41
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
	)

	SET_TARGET_PROPERTIES( jtrdll_ssse3
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
	)
	
	SET_TARGET_PROPERTIES( jtrdll_sse2
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
	)

	SET_TARGET_PROPERTIES( jtrexe
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
	)

	SET_TARGET_PROPERTIES( lc7jtr
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}
	)

	SET_TARGET_PROPERTIES( lc7core
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
	)

	SET_TARGET_PROPERTIES( lc7base
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7base{2f63a714-8518-4ab6-ba7d-5440888dfc8a}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7base{2f63a714-8518-4ab6-ba7d-5440888dfc8a}
	)

	SET_TARGET_PROPERTIES( lc7password
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7password{072cbbd8-84ad-481f-b0a6-398b121434db}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7password{072cbbd8-84ad-481f-b0a6-398b121434db}
	)

	SET_TARGET_PROPERTIES( lc7importwin
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7importwin{17324176-3fa7-4c1a-9204-3f391b6b3599}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7importwin{17324176-3fa7-4c1a-9204-3f391b6b3599}
	)

	SET_TARGET_PROPERTIES( lc7importunix
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7importunix{62a9e7aa-58f4-45ba-8fd7-5ebeb3d80551}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7importunix{62a9e7aa-58f4-45ba-8fd7-5ebeb3d80551}
	)

	SET_TARGET_PROPERTIES( lc7wizard
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7wizard{43b6f871-7951-4b81-814b-f41ef64c993b}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7wizard{43b6f871-7951-4b81-814b-f41ef64c993b}
	)

	SET_TARGET_PROPERTIES( lc7
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}
	)	
ENDFUNCTION()


FUNCTION(SET_LC7REPORTS_TARGET_PROPERTIES_CONFIG SUFFIX DIST)

	SET_TARGET_PROPERTIES( lc7reports
		PROPERTIES
		LIBRARY_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7reports{ee2be381-bd1f-481b-92dd-04b873ceb7ce}
		RUNTIME_OUTPUT_DIRECTORY${SUFFIX} ${DIST}/lcplugins/lc7reports{ee2be381-bd1f-481b-92dd-04b873ceb7ce}
	)

ENDFUNCTION()


# SINGLE CONFIG GENERATOR SETTINGS
SET_LC7_TARGET_PROPERTIES_CONFIG("" "${CMAKE_CURRENT_BINARY_DIR}/dist")
SET_LC7REPORTS_TARGET_PROPERTIES_CONFIG("" "${CMAKE_CURRENT_BINARY_DIR}/dist_lc7reports/")

# MULTI CONFIG GENERATOR SETTINGS
FOREACH(CFG ${CMAKE_CONFIGURATION_TYPES})
	STRING(TOUPPER ${CFG} UPPERCFG)
	SET_LC7_TARGET_PROPERTIES_CONFIG("_${UPPERCFG}" "${CMAKE_CURRENT_BINARY_DIR}/dist/${CFG}")
	SET_LC7REPORTS_TARGET_PROPERTIES_CONFIG("_${UPPERCFG}" "${CMAKE_CURRENT_BINARY_DIR}/dist_lc7reports/${CFG}")
ENDFOREACH()


# MAKE DISTRIBUTION
ADD_CUSTOM_TARGET(dist ALL
    DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/__header.h
)

IF(MSVC)
	SET (QTLIBLIST Qt5Widgets Qt5Concurrent Qt5Core Qt5Gui Qt5Multimedia Qt5MultimediaWidgets Qt5Network Qt5Sql Qt5PrintSupport Qt5Help)
	SET (QTPLUGLIST imageformats/qgif imageformats/qico imageformats/qicns imageformats/qjpeg imageformats/qsvg platforms/qwindows sqldrivers/qsqlite printsupport/windowsprintersupport styles/qwindowsvistastyle)
	SET (QTUTILLIST "") #icudt55.dll icuin55.dll icuuc55.dll)
	SET (TEMPLATEDIRS "${CMAKE_CURRENT_SOURCE_DIR}/dist/${LC7_PLATFORM}/common" "${CMAKE_CURRENT_SOURCE_DIR}/dist/${LC7_PLATFORM}/$<CONFIG>")
	SET (_DEBUG_RELEASE "$<$<CONFIG:Debug>:Debug>$<$<NOT:$<CONFIG:Debug>>:RelWithDebInfo>")
	SET (EXTRAS 
		"${CMAKE_BINARY_DIR}/openssl/bin-dll-${_DEBUG_RELEASE}/libeay32.dll,libeay32.dll" "${CMAKE_BINARY_DIR}/openssl/bin-dll-${_DEBUG_RELEASE}/ssleay32.dll,ssleay32.dll"
		"${CMAKE_BINARY_DIR}/openssl/bin-dll-${_DEBUG_RELEASE}/libeay32.dll,libeay32.dll" "${CMAKE_BINARY_DIR}/openssl/bin-dll-${_DEBUG_RELEASE}/ssleay32.dll,ssleay32.dll"
		"${CMAKE_BINARY_DIR}/openssl/bin-dll-${_DEBUG_RELEASE}/libeay32.dll,lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}/libeay32.dll" "${CMAKE_BINARY_DIR}/openssl/bin-dll-${_DEBUG_RELEASE}/ssleay32.dll,lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}/ssleay32.dll"
		"${CMAKE_BINARY_DIR}/openssl/bin-dll-${_DEBUG_RELEASE}/libeay32.dll,lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}/libeay32.dll" "${CMAKE_BINARY_DIR}/openssl/bin-dll-${_DEBUG_RELEASE}/ssleay32.dll,lcplugins/lc7jtr{9846c8cf-1db1-467e-83c2-c5655aa81936}/ssleay32.dll"
		)
ENDIF()

LIST(APPEND TEMPLATEDIRS "${CMAKE_CURRENT_SOURCE_DIR}/dist/common/common" "${CMAKE_CURRENT_SOURCE_DIR}/dist/common/$<CONFIG>")

IF(DEFINED CMAKE_BUILD_TYPE)
	MESSAGE("Single-config builds are not supported currently. CMAKE_BUILD_TYPE is: ${CMAKE_BUILD_TYPE}")
	SET(LC7_DIST "${CMAKE_CURRENT_BINARY_DIR}/dist")
ELSE()
	SET(LC7_DIST "${CMAKE_CURRENT_BINARY_DIR}/dist/$<CONFIG>")
ENDIF()

MAKE_DIST(dist "${LC7_DIST}" TEMPLATEDIRS QTLIBLIST QTPLUGLIST QTUTILLIST EXTRAS)



