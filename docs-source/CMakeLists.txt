#
# Build and install API documentation
#

find_package(Doxygen QUIET)
set(OPENMM_GENERATE_API_DOCS OFF CACHE BOOL "Whether to create API documentation using Doxygen")

IF(DOXYGEN_EXECUTABLE)
    # Generate C++ API documentation
    IF (OPENMM_GENERATE_API_DOCS)
        ADD_SUBDIRECTORY(api-c++)
    ENDIF (OPENMM_GENERATE_API_DOCS)

    # Generate Python API documentation
    IF (OPENMM_BUILD_PYTHON_WRAPPERS AND OPENMM_GENERATE_API_DOCS)
        ADD_SUBDIRECTORY(api-python)
    ENDIF (OPENMM_BUILD_PYTHON_WRAPPERS AND OPENMM_GENERATE_API_DOCS)
ENDIF(DOXYGEN_EXECUTABLE)


#
# Build and install the User Guide and Developer Guide
#

SET(SPHINX_BUILD_DIR "${CMAKE_BINARY_DIR}/sphinx-docs/")
FILE(MAKE_DIRECTORY "${SPHINX_BUILD_DIR}")

FILE(GLOB_RECURSE USER_GUIDE_FILES ${CMAKE_CURRENT_SOURCE_DIR}/usersguide/*)
FILE(GLOB_RECURSE DEVELOPER_GUIDE_FILES ${CMAKE_CURRENT_SOURCE_DIR}/developerguide/*)
ADD_CUSTOM_COMMAND(
    OUTPUT "${SPHINX_BUILD_DIR}/userguide/latex/OpenMMUsersGuide.pdf"
    COMMAND "${CMAKE_MAKE_PROGRAM}" BUILDDIR="${SPHINX_BUILD_DIR}/userguide" OPENMM_VERSION="${OPENMM_MAJOR_VERSION}.${OPENMM_MINOR_VERSION}" latexpdf
    DEPENDS ${USER_GUIDE_FILES}
    WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/usersguide"
    COMMENT "Generating PDF user guide"
)
ADD_CUSTOM_COMMAND(
    OUTPUT "${SPHINX_BUILD_DIR}/developerguide/latex/OpenMMDeveloperGuide.pdf"
    COMMAND "${CMAKE_MAKE_PROGRAM}" BUILDDIR="${SPHINX_BUILD_DIR}/developerguide" OPENMM_VERSION="${OPENMM_MAJOR_VERSION}.${OPENMM_MINOR_VERSION}" latexpdf
    DEPENDS ${DEVELOPER_GUIDE_FILES}
    WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/developerguide"
    COMMENT "Generating PDF developer guide"
)
ADD_CUSTOM_TARGET(sphinxpdf
    DEPENDS "${SPHINX_BUILD_DIR}/userguide/latex/OpenMMUsersGuide.pdf" "${SPHINX_BUILD_DIR}/developerguide/latex/OpenMMDeveloperGuide.pdf"
)

ADD_CUSTOM_COMMAND(
    OUTPUT "${SPHINX_BUILD_DIR}/userguide/html/index.html"
    COMMAND "${CMAKE_MAKE_PROGRAM}" BUILDDIR="${SPHINX_BUILD_DIR}/userguide" OPENMM_VERSION="${OPENMM_MAJOR_VERSION}.${OPENMM_MINOR_VERSION}" html
    DEPENDS ${USER_GUIDE_FILES}
    WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/usersguide"
    COMMENT "Generating HTML user guide"
)
ADD_CUSTOM_COMMAND(
    OUTPUT "${SPHINX_BUILD_DIR}/developerguide/html/index.html"
    COMMAND "${CMAKE_MAKE_PROGRAM}" BUILDDIR="${SPHINX_BUILD_DIR}/developerguide" OPENMM_VERSION="${OPENMM_MAJOR_VERSION}.${OPENMM_MINOR_VERSION}" html
    DEPENDS ${DEVELOPER_GUIDE_FILES}
    WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/developerguide"
    COMMENT "Generating HTML developer guide"
)
ADD_CUSTOM_TARGET(sphinxhtml
    DEPENDS "${SPHINX_BUILD_DIR}/userguide/html/index.html" "${SPHINX_BUILD_DIR}/developerguide/html/index.html"
)

install(FILES "${SPHINX_BUILD_DIR}/userguide/latex/OpenMMUsersGuide.pdf" "${SPHINX_BUILD_DIR}developerguide/latex/OpenMMDeveloperGuide.pdf"
    DESTINATION docs/ OPTIONAL)

FILE(GLOB LICENSE_FILES "licenses/*.txt")
install(FILES ${LICENSE_FILES}
    DESTINATION licenses/)
