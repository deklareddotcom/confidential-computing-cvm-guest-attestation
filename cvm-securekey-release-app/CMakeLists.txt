cmake_minimum_required(VERSION 3.5)

project(AzureAttestSKR)

set(CMAKE_PROJECT_TARGET AzureAttestSKR)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# define TRACE constant for extensive logging.
if (CMAKE_BUILD_TYPE MATCHES Debug)
    add_definitions (-DTRACE)
endif()

add_definitions (-DPLATFORM_UNIX)

include(FetchContent)
FetchContent_Declare(
    azuresdk
    GIT_REPOSITORY      https://github.com/Azure/azure-sdk-for-cpp.git
    GIT_TAG             azure-identity_1.5.0-beta.2
)
FetchContent_GetProperties(azuresdk)
if(NOT azuresdk_POPULATED)
    FetchContent_Populate(azuresdk)
    # Adding all Azure SDK libraries
    # add_subdirectory(${azuresdk_SOURCE_DIR} ${azuresdk_BINARY_DIR} EXCLUDE_FROM_ALL)
    # Adding one Azure SDK Library only (Storage blobs)
    add_subdirectory(${azuresdk_SOURCE_DIR}/sdk/identity/azure-identity ${azuresdk_BINARY_DIR} EXCLUDE_FROM_ALL)
endif()

include_directories(
     /usr/include/azguestattestation1
     /usr/include/jsoncpp
)

link_directories (
    /usr/lib
    /usr/lib/x86_64-linux-gnu
)

add_executable(${CMAKE_PROJECT_TARGET}
    AttestationUtil.cpp
    Logger.cpp
    Main.cpp
)

find_package(azure-identity-cpp CONFIG REQUIRED)
target_link_libraries(${CMAKE_PROJECT_TARGET} azguestattestation curl jsoncpp PRIVATE Azure::azure-identity)