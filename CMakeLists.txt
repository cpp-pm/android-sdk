# Copyright (c) 2015, Ruslan Baratov
# All rights reserved.

cmake_minimum_required(VERSION 3.0)

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/android-001.tar.gz"
    SHA1 "bceb57f1d7da5c7b0ba67339377a3866b5e530f0"
)

project(AndroidSDK)

include(CMakeParseArguments)

function(unified_install)
  cmake_parse_arguments(x "" "DESTINATION" "OBJECTS" ${ARGV})
  # -> x_DESTINATION
  # -> x_OBJECTS

  string(COMPARE NOTEQUAL "${x_UNPARSED_ARGUMENTS}" "" has_unparsed)
  if(has_unparsed)
    message(FATAL_ERROR "Unparsed: ${x_UNPARSED_ARGUMENTS}")
  endif()

  set(dest "android-sdk/${x_DESTINATION}")

  foreach(object ${x_OBJECTS})
    if(NOT EXISTS "${object}")
      message(FATAL_ERROR "Object not exists: ${object}")
    endif()
    if(IS_DIRECTORY "${object}")
      install(DIRECTORY "${object}" DESTINATION "${dest}")
    else()
      install(
          FILES "${object}"
          DESTINATION "${dest}"
          PERMISSIONS
              OWNER_WRITE
              OWNER_READ GROUP_READ WORLD_READ
              OWNER_EXECUTE GROUP_EXECUTE WORLD_EXECUTE
      )
    endif()
  endforeach()
endfunction()

function(parse_version_revision)
  set(one FULL VERSION REVISION)
  cmake_parse_arguments(x "" "${one}" "" "${ARGV}")
  # x_FULL (in) full string with version (e.g. '22_r01')
  # x_VERSION (out)
  # x_REVISION (out)

  string(COMPARE EQUAL "${x_FULL}" "" is_empty)
  if(is_empty)
    message(FATAL_ERROR "Expected FULL")
  endif()

  string(COMPARE EQUAL "${x_VERSION}" "" is_empty)
  if(is_empty)
    message(FATAL_ERROR "Expected VERSION")
  endif()

  string(COMPARE EQUAL "${x_REVISION}" "" is_empty)
  if(is_empty)
    message(FATAL_ERROR "Expected REVISION")
  endif()

  string(COMPARE NOTEQUAL "${x_UNPARSED_ARGUMENTS}" "" has_unparsed)
  if(has_unparsed)
    message(FATAL_ERROR "Unparsed: ${x_UNPARSED_ARGUMENTS}")
  endif()

  if(NOT x_FULL MATCHES "^([0-9]+)_r([0-9]+)$")
    message(FATAL_ERROR "Incorrect version: ${x_FULL}")
  endif()

  set(version "${CMAKE_MATCH_1}")
  set(revision "${CMAKE_MATCH_2}")

  string(REGEX REPLACE "^0" "" revision "${revision}") # 01 -> 1

  set("${x_VERSION}" "${version}" PARENT_SCOPE)
  set("${x_REVISION}" "${revision}" PARENT_SCOPE)
endfunction()

###
hunter_add_package(Android-SDK-Tools)
file(GLOB x "${ANDROID-SDK-TOOLS_ROOT}/*")
unified_install(OBJECTS ${x} DESTINATION "tools")

###
hunter_add_package(Android-SDK-Platform-tools)
file(GLOB x "${ANDROID-SDK-PLATFORM-TOOLS_ROOT}/*")
unified_install(OBJECTS ${x} DESTINATION "platform-tools")

###
hunter_add_package(Android-SDK-Platform)
file(GLOB x "${ANDROID-SDK-PLATFORM_ROOT}/*")
unified_install(
    OBJECTS ${x}
    DESTINATION "platforms/${HUNTER_Android-SDK-Platform_VERSION}"
)

###
hunter_add_package(Android-Build-Tools)
file(GLOB x "${ANDROID-BUILD-TOOLS_ROOT}/*")
unified_install(
    OBJECTS ${x}
    DESTINATION "build-tools/${HUNTER_Android-Build-Tools_VERSION}"
)

###
hunter_add_package(Sources-for-Android-SDK)
file(GLOB x "${SOURCES-FOR-ANDROID-SDK_ROOT}/*")
unified_install(
    OBJECTS ${x}
    DESTINATION "sources/android-${HUNTER_Sources-for-Android-SDK_VERSION}"
)

###
hunter_add_package(Android-Google-APIs)

parse_version_revision(
    FULL "${HUNTER_Android-Google-APIs_VERSION}"
    VERSION version
    REVISION revision
)

set(props "${CMAKE_BINARY_DIR}/gen/Android-Google-APIs/source.properties")

# Used variables:
# * version
# * revision
configure_file("Android-Google-APIs.source.properties" "${props}" @ONLY)

file(GLOB x "${ANDROID-GOOGLE-APIS_ROOT}/*")

unified_install(
    OBJECTS ${x} ${props}
    DESTINATION "add-ons/addon-google_apis-google-${version}"
)

###
hunter_add_package(Android-Google-APIs-Intel-x86-Atom-System-Image)
file(GLOB x "${ANDROID-GOOGLE-APIS-INTEL-X86-ATOM-SYSTEM-IMAGE_ROOT}/*")
unified_install(
    OBJECTS ${x}
    DESTINATION "system-images/android-${HUNTER_Android-Google-APIs-Intel-x86-Atom-System-Image_VERSION}/google_apis/x86"
)

###
hunter_add_package(Android-Intel-x86-Atom-System-Image)
file(GLOB x "${ANDROID-INTEL-X86-ATOM-SYSTEM-IMAGE_ROOT}/*")
unified_install(
    OBJECTS ${x}
    DESTINATION "system-images/android-${HUNTER_Android-Intel-x86-Atom-System-Image_VERSION}/default/x86"
)

###
hunter_add_package(Android-Support-Repository)
file(GLOB x "${ANDROID-SUPPORT-REPOSITORY_ROOT}/*")
unified_install(
    OBJECTS ${x}
    DESTINATION "extras/android/m2repository"
)

###
hunter_add_package(Android-Google-Repository)

set(props "${CMAKE_BINARY_DIR}/gen/Google-Repository/source.properties")
configure_file("Google-Repository.source.properties" "${props}" @ONLY)

file(GLOB x "${ANDROID-GOOGLE-REPOSITORY_ROOT}/*")
unified_install(
    OBJECTS ${x} ${props}
    DESTINATION "extras/google/m2repository"
)
