CMAKE_MINIMUM_REQUIRED(VERSION 3.15)
if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

project(CNPY VERSION 2.0)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

option(ENABLE_STATIC "Build static (.a) library" ON)

find_package(ZLIB REQUIRED)

add_executable(example1 example1.cpp)
add_library(cnpy "cnpy.cpp")

target_link_libraries(cnpy ${ZLIB_LIBRARIES})
target_include_directories(cnpy PUBLIC ${ZLIB_INCLUDE_DIRS})
target_link_libraries(example1 cnpy)

# install
set(CMAKE_INSTALL_PREFIX ${CMAKE_CURRENT_SOURCE_DIR}/install)

target_include_directories(cnpy PUBLIC 
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)

install(TARGETS "cnpy" EXPORT cnpyConfig
        LIBRARY DESTINATION lib 
        PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

install(FILES "cnpy.h" DESTINATION include)

install(FILES "mat2npz" "npy2mat" "npz2mat" 
        DESTINATION bin 
        PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

install(EXPORT cnpyConfig
    FILE cnpyTarget.cmake
    NAMESPACE Cnpy::
    DESTINATION share/cmake/cnpy
)

include(CMakePackageConfigHelpers)

configure_package_config_file(
    "${CMAKE_SOURCE_DIR}/cnpyConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/cnpyConfig.cmake"
    INSTALL_DESTINATION lib/cmake/CNPY
)

write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/cnpyConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

install(
    FILES "${CMAKE_CURRENT_BINARY_DIR}/cnpyConfig.cmake"
          "${CMAKE_CURRENT_BINARY_DIR}/cnpyConfigVersion.cmake"
    DESTINATION share/cmake/CNPY
)

install(EXPORT cnpyConfig DESTINATION share/cmake/CNPY)
