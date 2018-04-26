# Copyright (c) 2015-2017, Ruslan Baratov
# All rights reserved.

cmake_minimum_required(VERSION 3.0)

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.20.55.tar.gz"
    SHA1 "c9d1407a3640be13887f3aa7a8d0c24c74cab5aa"
)

project(AndroidSDK)

hunter_add_package(android_sdk_tools_packer)
hunter_add_package(android_sdk_platform_tools_packer)
hunter_add_package(android_sdk_platform_packer)
hunter_add_package(android_build_tools_packer)
hunter_add_package(sources_for_android_sdk_packer)
