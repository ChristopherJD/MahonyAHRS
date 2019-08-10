############################## Project Version ################################

CMAKE_MINIMUM_REQUIRED(VERSION 2.8.1)

SET(AHRS_VERSION_MAJOR 0)
SET(AHRS_VERSION_MINOR 1)
SET(AHRS_VERSION_PATCH 0)
SET(AHRS_VERSION_STRING 
    ${AHRS_VERSION_MAJOR}.${AHRS_VERSION_MINOR}.${AHRS_VERSION_PATCH})
SET(LIB_BUILD_VERSION ${AHRS_VERSION_STRING} CACHE STRING "Build Version")
OPTION(BUILD_VERSION "Set the version being built." ${LIB_BUILD_VERSION})
ADD_DEFINITIONS(-DBUILD_VERSION="${BUILD_VERSION}")
###############################################################################

############################ Project Settings #################################
PROJECT(AHRS)
SET(PROJECT AHRS)
SET(LICENSE "GPLV3")
###############################################################################

########################### Build and include settings ########################
SET(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -DDEBUG=1 -g")
STRING(CONCAT CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -Wall -pedantic")

INCLUDE_DIRECTORIES(
    inc
)

LINK_DIRECTORIES(
    ${LINK_DIRECTORIES}
)

########################## C Library ##########################################
SET(SOURCES 
    "src/MahonyAHRS.cpp" 
)

ADD_LIBRARY(AHRS SHARED ${SOURCES} ${CONFIGS})
TARGET_LINK_LIBRARIES(AHRS m)
SET_TARGET_PROPERTIES(AHRS PROPERTIES VERSION ${LSM9DS1_VERSION_STRING}
                                          SOVERSION ${LSM9DS1_VERSION_MAJOR})

########################## C Library ##########################################
INSTALL(TARGETS AHRS
	DESTINATION usr/lib/
    COMPONENT binaries
)

######################## RPM Package ##########################################
# Unit test picks up lsm9ds1 lib so I need to manually do this
SET(CPACK_RPM_PACKAGE_AUTOREQ no)
SET(CPACK_RPM_PACKAGE_RELOCATABLE ON)
SET(DESTDIR /)
SET(CPACK_RPM_PACKAGE_ARCHITECTURE "raspberrypi3")
SET(CPACK_PACKAGE_VERSION ${BUILD_VERSION})
SET(CPACK_GENERATOR "RPM;TGZ")
SET(CPACK_PACKAGE_NAME ${PROJECT})
SET(CPACK_PACKAGE_RELEASE 1)
SET(CPACK_PACKAGE_CONTACT "Christopher Jordan-Denny")
SET(CPACK_PACKAGE_VENDOR "Christopher Jordan-Denny")
SET(CPACK_PACKAGING_INSTALL_PREFIX ${DESTDIR})
STRING(CONCAT CPACK_PACKAGE_FILE_NAME 
    "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}"
    "-${CPACK_RPM_PACKAGE_ARCHITECTURE}")
INCLUDE(CPack)
###############################################################################
