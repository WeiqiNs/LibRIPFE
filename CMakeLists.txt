# Set cmake version.
cmake_minimum_required(VERSION 3.20)

# Project name and language.
project(
        LibRIPFE
        VERSION 0.1
        DESCRIPTION "This library implements a few inner product functional encryption schemes."
        LANGUAGES CXX
)

# Set standards.
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Include desired modules.
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

# Enable the testing.
enable_testing()

# Find required libraries.
find_package(RBP REQUIRED)

# Find the google test library for testing.
find_package(GTest)
if (NOT GTest_FOUND)
    include(FetchContent)
    FetchContent_Declare(
            googletest
            URL https://github.com/google/googletest/archive/refs/heads/main.zip
            DOWNLOAD_EXTRACT_TIMESTAMP TRUE
    )
    # For Windows: Prevent overriding the parent project's compiler/linker settings
    set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
    FetchContent_MakeAvailable(googletest)
endif ()

# Add desired subdirectories.
add_subdirectory(src)
add_subdirectory(test)

# Install targets and files.
install(TARGETS RIPFE
        EXPORT RIPFETargets
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/RIPFE)

install(EXPORT RIPFETargets
        FILE RIPFETargets.cmake
        NAMESPACE RIPFE::
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/RIPFE)

# Install the configuration file.
configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/RIPFEConfig.cmake.in
        "${CMAKE_CURRENT_BINARY_DIR}/RIPFEConfig.cmake"
        INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/RIPFE)

write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/RIPFEConfigVersion.cmake"
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY SameMajorVersion)

install(FILES
        "${CMAKE_CURRENT_BINARY_DIR}/RIPFEConfig.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/RIPFEConfigVersion.cmake"
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/RIPFE)

# Install the dependency file.
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/RIPFEDependencies.cmake.in
        "${CMAKE_CURRENT_BINARY_DIR}/RIPFEDependencies.cmake"
        @ONLY)

install(FILES
        "${CMAKE_CURRENT_BINARY_DIR}/RIPFEDependencies.cmake"
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/RIPFE)
