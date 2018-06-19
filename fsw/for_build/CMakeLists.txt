# Set local variables
set(APPTARGET lc)

# Set source files
set(APP_SRC 
	${CMAKE_CURRENT_SOURCE_DIR}/../src/lc_action.c
	${CMAKE_CURRENT_SOURCE_DIR}/../src/lc_app.c
	${CMAKE_CURRENT_SOURCE_DIR}/../src/lc_cmds.c
	${CMAKE_CURRENT_SOURCE_DIR}/../src/lc_custom.c
	${CMAKE_CURRENT_SOURCE_DIR}/../src/lc_watch.c)
	
# Set include paths
set(APP_INC_DIRS 
	${CMAKE_CURRENT_SOURCE_DIR}/../src/)
include_directories(${APP_INC_DIRS})
include_directories(${CFE_INC_DIRS})
include_directories(${OSAL_INC_DIRS})
include_directories(${PSP_INC_DIRS})
	
# Build application
add_app(${APP_FILE} SHARED ${APP_SRC})

# Build tables
add_app(lc_def_adt STATIC ${TABLE_DIR}/lc_def_adt.c)
add_app(lc_def_wdt STATIC ${TABLE_DIR}/lc_def_wdt.c)
set_target_properties(lc_def_adt PROPERTIES PREFIX "" SUFFIX ".tbl")
set_target_properties(lc_def_wdt PROPERTIES PREFIX "" SUFFIX ".tbl")

add_subdirectory(../unit_test unit_test)

# Generate documentation
find_package(Doxygen)
if(DOXYGEN_FOUND)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/user_doxy.in ${CMAKE_CURRENT_BINARY_DIR}/user_doxy @ONLY)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/detail_doxy.in ${CMAKE_CURRENT_BINARY_DIR}/detail_doxy @ONLY)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/codewalk_doxy.in ${CMAKE_CURRENT_BINARY_DIR}/codewalk_doxy @ONLY)
    add_custom_target(lc-docs
        COMMENT "Generating API documentation with Doxygen" VERBATIM
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/users_guide/html/${APPTARGET}/
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/users_guide/latex/${APPTARGET}/
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/detailed_design/html/${APPTARGET}/
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/detailed_design/latex/${APPTARGET}/
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/cwt/html/${APPTARGET}/
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/cwt/latex/${APPTARGET}/
        COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/user_doxy
        COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/detail_doxy
        COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/codewalk_doxy
        #COMMAND make -C ${CMAKE_BINARY_DIR}/docs/users_guide/latex/${APPTARGET}
        #COMMAND make -C ${CMAKE_BINARY_DIR}/docs/detailed_design/latex/${APPTARGET}
        #COMMAND make -C ${CMAKE_BINARY_DIR}/docs/cwt/latex/${APPTARGET}
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/
    )
endif(DOXYGEN_FOUND)
