cmake_minimum_required(VERSION 3.13...3.15)

if(CMAKE_VERSION VERSION_LESS "3.15")
    include(${CMAKE_PROJECT_INCLUDE_BEFORE})
    set(CMAKE_PROJECT_INCLUDE_BEFORE ${CMAKE_PROJECT_INCLUDE_BEFORE} CACHE PATH "custom cmake module")
endif()
# =======================================
project(span LANGUAGES CXX VERSION 0.9.0)
# =======================================
if(CMAKE_VERSION VERSION_LESS "3.15")
    include(${CMAKE_PROJECT_INCLUDE})
    set(CMAKE_PROJECT_INCLUDE ${CMAKE_PROJECT_INCLUDE} CACHE PATH "custom cmake module")
endif()


if(PROJECT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
    set(SPAN_MASTER_PROJECT ON)
endif()


include(GNUInstallDirs)


add_library(span INTERFACE)
add_library(tcb::span ALIAS span)
target_include_directories(span INTERFACE
        "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
        "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
)
target_compile_features(span INTERFACE cxx_std_17)

option(TCB_SPAN_STD_COMPLIANT_MODE "Be strict C++20 complient" OFF)
if(TCB_SPAN_STD_COMPLIANT_MODE)
    target_compile_definitions(span INTERFACE TCB_SPAN_STD_COMPLIANT_MODE)
endif()


option(BUILD_TESTS "Build tests" ${SPAN_MASTER_PROJECT})
if(BUILD_TESTS)
    enable_testing()

    set(TCB_SPAN_TEST_CXX_STD 17 CACHE STRING "C++ standard version for testing")
    add_subdirectory(test)
endif()


option(SPAN_INSTALL "Generate the install target" ${SPAN_MASTER_PROJECT})
if(SPAN_INSTALL)
    include(CMakePackageConfigHelpers)

    install(TARGETS span EXPORT ${PROJECT_NAME}Targets)
    install(DIRECTORY include/tcb DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
    set(configFile ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake)
    set(versionFile ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake)
    set(configInstallDestination lib/cmake/${PROJECT_NAME})

    configure_package_config_file(
        ${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
        ${configFile}
        INSTALL_DESTINATION ${configInstallDestination}
    )
    write_basic_package_version_file(
        ${versionFile}
        COMPATIBILITY SameMajorVersion
    )

    install(FILES ${configFile} ${versionFile} DESTINATION ${configInstallDestination})
    install(
        EXPORT ${PROJECT_NAME}Targets
        NAMESPACE "tcb::"
        DESTINATION ${configInstallDestination}
    )
endif()

