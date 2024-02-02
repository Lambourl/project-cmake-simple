cmake_minimum_required(VERSION 3.17.0)
project(MathFunctions VERSION "0.0.1")


add_library(MathFunctions  "${CMAKE_CURRENT_LIST_DIR}/MathFunctions.cxx" )
add_library(MathFunctions::MathFunctions ALIAS MathFunctions)

target_include_directories(MathFunctions BEFORE PUBLIC 
  $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/> 
  $<INSTALL_INTERFACE:${include_install_dir}/>)

#

find_package(Threads)
target_link_libraries(MathFunctions
  
  ${CMAKE_THREAD_LIBS_INIT}
)

set(include_install_dir "include")
# Installing

# Layout. This works for all platforms:
#   * <prefix>/lib/cmake/<PROJECT-NAME>
#   * <prefix>/lib/
#   * <prefix>/include/
set(config_install_dir "lib/cmake/${PROJECT_NAME}")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

# Configuration
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

# Include module with fuction 'write_basic_package_version_file'
include(CMakePackageConfigHelpers)

# Configure '<PROJECT-NAME>ConfigVersion.cmake'
# Note: PROJECT_VERSION is used as a VERSION
write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

# Configure '<PROJECT-NAME>Config.cmake'
# Use variables:
#   * targets_export_name
#   * PROJECT_NAME
configure_package_config_file(
    "${CMAKE_CURRENT_LIST_DIR}/cmake/modules/Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)


# Targets:
install(
    TARGETS MathFunctions
    EXPORT "${targets_export_name}"
    LIBRARY DESTINATION "lib"
    ARCHIVE DESTINATION "lib"
    RUNTIME DESTINATION "bin"
    INCLUDES DESTINATION "${include_install_dir}"
)

# Headers:
install(
    DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/
    DESTINATION "${include_install_dir}"
    FILES_MATCHING PATTERN "*.[ih]*"
    PATTERN ${CMAKE_CURRENT_BINARY_DIR}/build EXCLUDE
     PATTERN "/build*" EXCLUDE 
 PATTERN ".git/*" EXCLUDE 
 PATTERN ".tipistore/*" EXCLUDE 
 PATTERN "doc/*" EXCLUDE 
 PATTERN "node_modules/*" EXCLUDE 
)



# Config
#   * <prefix>/lib/cmake/modern-cmake-examples-simple-project/modern-cmake-examples-simple-projectConfig.cmake
#   * <prefix>/lib/cmake/modern-cmake-examples-simple-project/modern-cmake-examples-simple-projectConfigVersion.cmake
#   * <prefix>/lib/cmake/modern-cmake-examples-simple-project/modern-cmake-examples-simple-projectTargets.cmake
install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)
install(
    EXPORT "${targets_export_name}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)

