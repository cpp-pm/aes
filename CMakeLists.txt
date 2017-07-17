cmake_minimum_required(VERSION 3.0)
project(aes VERSION 0.0.1)

add_library(aes aes.c aes.h)

set(AES_TYPE "128" CACHE STRING "AES: 128, 192, 256")

string(COMPARE EQUAL "${AES_TYPE}" "128" is_128)
string(COMPARE EQUAL "${AES_TYPE}" "192" is_192)
string(COMPARE EQUAL "${AES_TYPE}" "256" is_256)

if(is_128)
  target_compile_definitions(aes PUBLIC AES128=1)
elseif(is_192)
  target_compile_definitions(aes PUBLIC AES192=1)
elseif(is_256)
  target_compile_definitions(aes PUBLIC AES256=1)
else()
  message(FATAL_ERROR "Logic error")
endif()

####
# Installation (https://github.com/forexample/package-example)

set(config_install_dir "lib/cmake/${PROJECT_NAME}")
set(include_install_dir "include")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)

# Use variables:
# * PROJECT_VERSION
write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

# Use variables:
#   * targets_export_name
#   * PROJECT_NAME
configure_package_config_file(
    "cmake/Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)

install(
    TARGETS aes
    EXPORT "${targets_export_name}"
    LIBRARY DESTINATION "lib"
    ARCHIVE DESTINATION "lib"
    RUNTIME DESTINATION "bin"
    INCLUDES DESTINATION "${include_install_dir}"
)

install(
    FILES aes.h
    DESTINATION "${include_install_dir}"
)

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

install(
    EXPORT "${targets_export_name}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)
