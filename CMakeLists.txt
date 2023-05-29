cmake_minimum_required(VERSION 3.10)

set(PATCH_VERSION "9")
set(PROJECT_VESRION 0.0.${PATCH_VERSION})

project(oneWorkPlace VERSION ${PROJECT_VESRION})

find_package(Boost COMPONENTS unit_test_framework REQUIRED)

configure_file(pragmaversion.h.in pragmaversion.h)

add_executable(oneWorkPlace_pr main.cpp)
add_library(libVersion libVersion.cpp)
add_executable(boost_test_version boost_test_version.cpp)

set_target_properties(oneWorkPlace_pr libVersion boost_test_version PROPERTIES
    CXX_STANDARD 14
    CXX_STANDARD_REQUIRED ON
)

target_include_directories(libVersion
    PRIVATE "${CMAKE_BINARY_DIR}"
)
set_target_properties(boost_test_version PROPERTIES
    COMPILE_DEFINITIONS BOOST_TEST_DYN_LINK
    INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIR}
)

target_link_libraries(oneWorkPlace_pr PRIVATE
libVersion
)
target_link_libraries(boost_test_version
    ${Boost_LIBRARIES}
    libVersion
)


install(TARGETS oneWorkPlace_pr RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_PACKAGE_CONTACT example@example.com)

include(CPack)

enable_testing()

add_test(boost_test_version boost_test_version)