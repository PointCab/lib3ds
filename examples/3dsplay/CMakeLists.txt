FIND_PACKAGE(OpenGL)
FIND_PACKAGE(FreeGLUT REQUIRED)
FIND_PACKAGE(OpenCV REQUIRED)
IF(FreeGLUT_FOUND)
	IF(MSVC)
		ADD_DEFINITIONS(-D_CRT_SECURE_NO_WARNINGS)
	ENDIF(MSVC)
	
	SET(FreeGLUT_LIBRARY_DIRS ${FreeGLUT_DIR}/../..)
	
	MESSAGE(STATUS "OpenCV_LIBS ${OpenCV_LIBS} ")
	MESSAGE(STATUS "OPENCV_LIB_DIR ${OPENCV_LIB_DIR} ")
	MESSAGE(STATUS "OpenCV_INCLUDE_DIRS ${OpenCV_INCLUDE_DIRS} ")
	MESSAGE(STATUS "FreeGLUT_DIR ${FreeGLUT_DIR} ")
	MESSAGE(STATUS "FreeGLUT_LIBRARY_DIRS ${FreeGLUT_LIBRARY_DIRS}")
	
	set(FreeGLUT_BASE_LIBS
		freeglut
		# Add other modules as needed, WITHOUT 'd' suffix or version number
	)

	# Construct the list of libraries using generator expressions
	# This is the key change for multi-config generators
	set(FreeGLUT_LIBS_GENERATOR_EXPRESSIONS "")
	foreach(lib_name IN LISTS FreeGLUT_BASE_LIBS)
		# This generator expression means:
		#	 If the configuration is "Debug", use "${lib_name}d"
		# Otherwise (e.g., Release, RelWithDebInfo), use "${lib_name}"
		list(APPEND FreeGLUT_LIBS_GENERATOR_EXPRESSIONS
        "$<$<CONFIG:Debug>:${lib_name}d>"  # Debug configuration gets 'd' suffix
        "$<$<NOT:$<CONFIG:Debug>>:${lib_name}>" # Other configurations don't
    )
	endforeach()

	
	ADD_EXECUTABLE(3dsplay 3dsplay.cpp tga.c tga.h)
	
	INCLUDE_DIRECTORIES( ${lib3ds_SOURCE_DIR}/src ${OPENGL_INCLUDE_DIR} ${FreeGLUT_INCLUDE_DIR} ${OPENCV_INCLUDE_DIRS} )
	# Add library directories so the linker can find the .lib/.so/.dylib files
	target_link_directories(3dsplay PRIVATE ${FreeGLUT_LIBRARY_DIRS} )
	
	
	TARGET_LINK_LIBRARIES(3dsplay lib3ds FreeGLUT::freeglut  ${OpenCV_LIBS} ${FreeGLUT_LIBS_GENERATOR_EXPRESSIONS})
ENDIF(FreeGLUT_FOUND)