# Build the library
# =================
project(Lib2 VERSION 1.2.3)
add_library(Lib2 Lib2.h Lib2.cpp)

# Use a general prefix for the project
set(VARS_PREFIX Lib2)

# Export the includes needed to who inherits this library
# Set this up properly for handling either BUILD and INSTALL trees
set(${VARS_PREFIX}_INCLUDE_BUILD ${CMAKE_CURRENT_SOURCE_DIR})
set(${VARS_PREFIX}_INCLUDE_INSTALL ${CMAKE_INSTALL_INCLUDEDIR}/${VARS_PREFIX})

target_include_directories(${VARS_PREFIX} INTERFACE
                           $<BUILD_INTERFACE:${${VARS_PREFIX}_INCLUDE_BUILD}>
                           $<INSTALL_INTERFACE:${${VARS_PREFIX}_INCLUDE_INSTALL}>)

# Assign some useful properties
set_target_properties(${VARS_PREFIX} PROPERTIES VERSION ${PROJECT_VERSION}
PUBLIC_HEADER Lib2.h)

# Install the library
# ===================
install(TARGETS ${VARS_PREFIX}
        EXPORT ${VARS_PREFIX}Targets
        RUNTIME       DESTINATION ${CMAKE_INSTALL_BINDIR}
        LIBRARY       DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE       DESTINATION ${CMAKE_INSTALL_LIBDIR}
        INCLUDES      DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
        PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${VARS_PREFIX})

install(EXPORT ${VARS_PREFIX}Targets
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${VARS_PREFIX}
        NAMESPACE ${VARS_PREFIX}::)

# Set the variables used for PackageConfigVersion and PackageConfig
set(PACKAGECONFIG_INCLUDE_DIRS ${${VARS_PREFIX}_INCLUDE_INSTALL})
set(CMAKECONFIG_INSTALL_DIR ${CMAKE_INSTALL_LIBDIR}/cmake/${VARS_PREFIX})

# Create the PackageConfigVersion.cmake
write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/${VARS_PREFIX}ConfigVersion.cmake
                                 COMPATIBILITY AnyNewerVersion)
# Create the PackageConfig.cmake
configure_package_config_file(${CMAKE_SOURCE_DIR}/cmake/GenericPackageConfig.cmake.in
                              ${CMAKE_CURRENT_BINARY_DIR}/${VARS_PREFIX}Config.cmake
                              PATH_VARS VARS_PREFIX PACKAGECONFIG_INCLUDE_DIRS
                              INSTALL_DESTINATION ${CMAKECONFIG_INSTALL_DIR})

# Install the PackageConfig.cmake and PackageConfigVersion.cmake files
install(FILES
        ${CMAKE_CURRENT_BINARY_DIR}/${VARS_PREFIX}Config.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/${VARS_PREFIX}ConfigVersion.cmake
        DESTINATION ${CMAKECONFIG_INSTALL_DIR})
