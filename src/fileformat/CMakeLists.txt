include_directories ("${PROJECT_BINARY_DIR}/" "${PROJECT_SOURCE_DIR}/src/pfs")
if( NOT HAS_GETOPT )
	include_directories ("${GETOPT_INCLUDE}")
endif( NOT HAS_GETOPT )

link_directories("${PROJECT_SOURCE_DIR}/src/pfs")

set( SHELL_CMDS pfsin pfsout pfsindcraw pfsinffmpeg pfsoutffmpeg )
#set( SHELL_CMDS  )
foreach(SHCMD ${SHELL_CMDS}) 
  # Replace the tag with the path to bash
  add_custom_command( OUTPUT ${SHCMD}
    DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/${SHCMD}.in
    COMMAND sed -e "'s;@BASH_PATH@;${BASH_EXECUTABLE};g'" ${CMAKE_CURRENT_SOURCE_DIR}/${SHCMD}.in >${CMAKE_CURRENT_BINARY_DIR}/${SHCMD} )
  add_custom_target(${SHCMD}_sh ALL DEPENDS ${SHCMD})
endforeach(SHCMD)
    
#file(READ ${CMAKE_CURRENT_SOURCE_DIR}/pfsin.in file_content)
#string(REGEX REPLACE "@BASH_PATH@" "${BASH_EXECUTABLE}" file_content "${file_content}")
#file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/pfsin" "${file_content}")	

#file(READ ${CMAKE_CURRENT_SOURCE_DIR}/pfsout.in file_content)
#string(REGEX REPLACE "(@BASH_PATH@)" "${BASH_EXECUTABLE}" file_content "${file_content}")
#file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/pfsout" "${file_content}")	

#file(READ ${CMAKE_CURRENT_SOURCE_DIR}/pfsindcraw.in file_content)
#string(REGEX REPLACE "(@BASH_PATH@)" "${BASH_EXECUTABLE}" file_content "${file_content}")
#file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/pfsindcraw" "${file_content}")	


install (FILES "${CMAKE_CURRENT_BINARY_DIR}/pfsin" "${CMAKE_CURRENT_BINARY_DIR}/pfsout" 
         "${CMAKE_CURRENT_BINARY_DIR}/pfsindcraw" "${CMAKE_CURRENT_BINARY_DIR}/pfsinffmpeg" "${CMAKE_CURRENT_BINARY_DIR}/pfsoutffmpeg" 
         PERMISSIONS OWNER_EXECUTE GROUP_EXECUTE WORLD_EXECUTE OWNER_WRITE WORLD_READ GROUP_READ OWNER_READ
         DESTINATION bin)

install (FILES pfsin.1 DESTINATION ${MAN_DIR})
install (FILES pfsindcraw.1 DESTINATION ${MAN_DIR})
install (FILES pfsout.1 DESTINATION ${MAN_DIR})
install (FILES pfsinffmpeg.1 DESTINATION ${MAN_DIR})
install (FILES pfsoutffmpeg.1 DESTINATION ${MAN_DIR})

add_executable(pfsinyuv pfsinyuv.cpp "${GETOPT_OBJECT}")
target_link_libraries(pfsinyuv pfs)
install (TARGETS pfsinyuv DESTINATION bin)
install (FILES pfsinyuv.1 DESTINATION ${MAN_DIR})

add_executable(pfsoutyuv pfsoutyuv.cpp "${GETOPT_OBJECT}")
target_link_libraries(pfsoutyuv pfs)
install (TARGETS pfsoutyuv DESTINATION bin)
install (FILES pfsoutyuv.1 DESTINATION ${MAN_DIR})

add_executable(pfsinrgbe pfsinrgbe.cpp rgbeio.cpp rgbeio.h "${GETOPT_OBJECT}")
target_link_libraries(pfsinrgbe pfs)
install (TARGETS pfsinrgbe DESTINATION bin)
install (FILES pfsinrgbe.1 DESTINATION ${MAN_DIR})

add_executable(pfsoutrgbe pfsoutrgbe.cpp rgbeio.cpp rgbeio.h "${GETOPT_OBJECT}") 
target_link_libraries(pfsoutrgbe pfs)
install (TARGETS pfsoutrgbe DESTINATION bin)
install (FILES pfsoutrgbe.1 DESTINATION ${MAN_DIR})

add_executable(pfsinpfm pfsinpfm.cpp "${GETOPT_OBJECT}")
target_link_libraries(pfsinpfm pfs)
install (TARGETS pfsinpfm DESTINATION bin)
install (FILES pfsinpfm.1 DESTINATION ${MAN_DIR})

add_executable(pfsoutpfm pfsoutpfm.cpp "${GETOPT_OBJECT}")
target_link_libraries(pfsoutpfm pfs)
install (TARGETS pfsoutpfm DESTINATION bin)
install (FILES pfsoutpfm.1 DESTINATION ${MAN_DIR})

if( OPENEXR_FOUND )
    include_directories(${OPENEXR_INCLUDES}/OpenEXR ${IMATH_INCLUDES}/Imath )
  	#message( "OpenEXR includes: ${OPENEXR_INCLUDES}" )
    #message( "Imath includes: ${IMATH_INCLUDES}" )
    #include_directories(/opt/homebrew/opt/openexr/include/OpenEXR /opt/homebrew/opt/ilmbase/include/OpenEXR/)

    message( "OpenEXR libs: ${OPENEXR_LIBRARIES} ${ILMBASE_LIBRARIES}" )

    add_executable(pfsinexr pfsinexr.cpp "${GETOPT_OBJECT}")
    #target_link_libraries(pfsinexr pfs /opt/homebrew/opt/openexr/lib/libIex.dylib /opt/homebrew/opt/openexr/lib/libIlmThread.dylib /opt/homebrew/opt/openexr/lib/libOpenEXR.dylib /opt/homebrew/opt/openexr/lib/libOpenEXRCore.dylib /opt/homebrew/opt/openexr/lib/libOpenEXRUtil.dylib /opt/homebrew/opt/openexr/lib/libIlmThread.dylib /opt/homebrew/opt/ilmbase/lib/libHalf.dylib /opt/homebrew/opt/ilmbase/lib/libImath.dylib)
    target_link_libraries(pfsinexr pfs ${OPENEXR_LIBRARIES})
    set_property(TARGET pfsinexr PROPERTY CXX_STANDARD 11)
    install (TARGETS pfsinexr DESTINATION bin)
    install (FILES pfsinexr.1 DESTINATION ${MAN_DIR})

    if( 0 )
    add_executable(pfsoutexr pfsoutexr.cpp "${GETOPT_OBJECT}")
    message( "OpenEXR Libraries: " ${OPENEXR_LIBS})
    #target_link_libraries(pfsoutexr pfs ${OPENEXR_LIBS})
    target_link_libraries(pfsoutexr pfs /opt/homebrew/opt/openexr/lib/libIex.dylib /opt/homebrew/opt/openexr/lib/libIlmThread.dylib /opt/homebrew/opt/openexr/lib/libOpenEXR.dylib /opt/homebrew/opt/openexr/lib/libOpenEXRCore.dylib /opt/homebrew/opt/openexr/lib/libOpenEXRUtil.dylib /opt/homebrew/opt/openexr/lib/libIlmThread.dylib /opt/homebrew/opt/ilmbase/lib/libHalf.dylib /opt/homebrew/opt/ilmbase/lib/libImath.dylib /opt/homebrew/opt/ilmbase/lib/libIex.dylib)
    set_property(TARGET pfsoutexr PROPERTY CXX_STANDARD 11)
    install (TARGETS pfsoutexr DESTINATION bin)
    install (FILES pfsoutexr.1 DESTINATION ${MAN_DIR})
    endif(0)
 endif( OPENEXR_FOUND )

if( ImageMagick_FOUND )
    include_directories(${ImageMagick_INCLUDE_DIRS})
    add_executable(pfsinimgmagick pfsinimgmagick.cpp "${GETOPT_OBJECT}")
    target_link_libraries(pfsinimgmagick pfs ${ImageMagick_LIBRARIES})
    install (TARGETS pfsinimgmagick DESTINATION bin)
    install (FILES pfsinimgmagick.1 DESTINATION ${MAN_DIR})
    set_target_properties(pfsinimgmagick PROPERTIES COMPILE_FLAGS "-DMAGICKCORE_HDRI_ENABLE=0 -DMAGICKCORE_QUANTUM_DEPTH=16")

    add_executable(pfsoutimgmagick pfsoutimgmagick.cpp "${GETOPT_OBJECT}")
    target_link_libraries(pfsoutimgmagick pfs ${ImageMagick_LIBRARIES})
    install (TARGETS pfsoutimgmagick DESTINATION bin)
    install (FILES pfsoutimgmagick.1 DESTINATION ${MAN_DIR})
    set_target_properties(pfsoutimgmagick PROPERTIES COMPILE_FLAGS "-DMAGICKCORE_HDRI_ENABLE=0 -DMAGICKCORE_QUANTUM_DEPTH=16")

endif( ImageMagick_FOUND )

if( NETPBM_FOUND )

    include_directories(${NETPBM_INCLUDE_DIR})

    add_executable(pfsinppm pfsinppm.cpp ppmio.cpp "${GETOPT_OBJECT}")
    target_link_libraries(pfsinppm pfs ${NETPBM_LIBRARIES})
    install (TARGETS pfsinppm DESTINATION bin)
    install (FILES pfsinppm.1 DESTINATION ${MAN_DIR})

    add_executable(pfsoutppm pfsoutppm.cpp ppmio.cpp "${GETOPT_OBJECT}")
    target_link_libraries(pfsoutppm pfs ${NETPBM_LIBRARIES})
    install (TARGETS pfsoutppm DESTINATION bin)
    install (FILES pfsoutppm.1 DESTINATION ${MAN_DIR})

endif( NETPBM_FOUND )

if( TIFF_FOUND )

    include_directories(${TIFF_INCLUDE_DIR})

    add_executable(pfsintiff pfsintiff.cpp hdrtiffio.cpp "${GETOPT_OBJECT}")
    target_link_libraries(pfsintiff pfs ${TIFF_LIBRARY})
    install (TARGETS pfsintiff DESTINATION bin)
    install (FILES pfsintiff.1 DESTINATION ${MAN_DIR})

    add_executable(pfsouttiff pfsouttiff.cpp hdrtiffio.cpp "${GETOPT_OBJECT}")
    target_link_libraries(pfsouttiff pfs ${TIFF_LIBRARY})
    install (TARGETS pfsouttiff DESTINATION bin)
    install (FILES pfsoutppm.1 DESTINATION ${MAN_DIR} RENAME pfsouttiff.1)

endif( TIFF_FOUND )

	
