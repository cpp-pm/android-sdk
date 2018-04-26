# Copyright (c) 2015-2017, Ruslan Baratov
# All rights reserved.

cmake_minimum_required(VERSION 3.0)

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.20.56.tar.gz"
    SHA1 "f8455a93aabc0514f0a84dcead9ac634e48a5dda"
)

project(Android-SDK)

hunter_add_package(android_sdk_tools_packer)
hunter_add_package(android_sdk_platform_tools_packer)
hunter_add_package(android_sdk_platform_packer)
hunter_add_package(android_build_tools_packer)
hunter_add_package(sources_for_android_sdk_packer)

install(FILES LICENSE DESTINATION licenses/Android-SDK)
