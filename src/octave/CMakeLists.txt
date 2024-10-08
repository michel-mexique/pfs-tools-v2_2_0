include_directories("${PROJECT_BINARY_DIR}/" "${PROJECT_SOURCE_DIR}/src/pfs" "${PROJECT_SOURCE_DIR}/src/octave/help_files" ${OCTAVE_DEST_DIR})
link_directories("${PROJECT_SOURCE_DIR}/src/pfs")

set(HELP_FILES pfsget.help pfsopen.help pfsput.help pfsread.help pfstransform_colorspace.help pfswrite.help)

set(SRC_OCT pfsread pfswrite pfstransform_colorspace pfsopen pfsget pfsput pfsclose)

set(SRC_M pfsview.m pfsview_rgb.m pfsview_list.m pfs_read_rgb.m pfs_read_xyz.m pfs_read_luminance.m pfs_write_rgb.m pfssize.m pfs_open_frames.m pfs_close_frames.m)

set(OCT_SCRIPTS pfsoctavelum pfsoctavergb pfsstat)

set(CMAKE_CXXFLAGS "-cxx -largeArrayDims")
set(ENV{CXXFLAGS} ${CMAKE_CXXFLAGS})

SET( CMAKE_CXX_COMPILER MKOCTFILE )
SET( CMAKE_C_COMPILER   MKOCTFILE )

SET( CMAKE_SHARED_LIBRARY_PREFIX )

set(HEADERS "${PROJECT_SOURCE_DIR}/src/pfs/")

#Building phase
foreach(SRC ${SRC_OCT})
  get_filename_component(OCT_NAME ${SRC} NAME)
  add_custom_command(
	OUTPUT ${OCT_NAME}.oct
	COMMAND ${MKOCTFILE} "${CMAKE_CURRENT_SOURCE_DIR}/${OCT_NAME}.cpp"
	ARGS -I${HEADERS} -I${CMAKE_CURRENT_SOURCE_DIR}/help_files
	-L$<TARGET_LINKER_FILE_DIR:pfs> -lpfs
	DEPENDS ${OCT_NAME}.cpp pfs
	COMMENT "Generating ${OCT_NAME}.oct"
	VERBATIM)
endforeach(SRC)

foreach(SRC ${SRC_OCT})
	add_custom_target(${SRC}.mkoct ALL DEPENDS ${SRC}.oct)
endforeach(SRC)

#Installation
foreach(SRC ${SRC_OCT}) 
	install (FILES ${CMAKE_CURRENT_BINARY_DIR}/${SRC}.oct 
	         PERMISSIONS OWNER_EXECUTE GROUP_EXECUTE WORLD_EXECUTE OWNER_WRITE WORLD_READ GROUP_READ OWNER_READ
	         DESTINATION "${OCTAVE_OCT_DIR}")
endforeach(SRC)

foreach(SRC ${SRC_M}) 
	install (FILES ${SRC} DESTINATION "${OCTAVE_M_DIR}")
endforeach(SRC)

install (FILES ${OCT_SCRIPTS} 
         PERMISSIONS OWNER_EXECUTE GROUP_EXECUTE WORLD_EXECUTE OWNER_WRITE WORLD_READ GROUP_READ OWNER_READ
         DESTINATION bin)
		 
foreach(SCRIPT ${OCT_SCRIPTS})

install (FILES ${SCRIPT}.1 DESTINATION ${MAN_DIR})

endforeach(SCRIPT ${OCT_SCRIPTS})
