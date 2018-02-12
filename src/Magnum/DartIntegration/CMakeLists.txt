#
#   This file is part of Magnum.
#
#   Copyright © 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018
#             Vladimír Vondruš <mosra@centrum.cz>
#   Copyright © 2018 Konstantinos Chatzilygeroudis <costashatz@gmail.com>
#
#   Permission is hereby granted, free of charge, to any person obtaining a
#   copy of this software and associated documentation files (the "Software"),
#   to deal in the Software without restriction, including without limitation
#   the rights to use, copy, modify, merge, publish, distribute, sublicense,
#   and/or sell copies of the Software, and to permit persons to whom the
#   Software is furnished to do so, subject to the following conditions:
#
#   The above copyright notice and this permission notice shall be included
#   in all copies or substantial portions of the Software.
#
#   THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
#   IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
#   FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
#   THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
#   LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
#   FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
#   DEALINGS IN THE SOFTWARE.
#

find_package(Magnum REQUIRED SceneGraph MeshTools Primitives)
find_package(DART CONFIG REQUIRED)

if(BUILD_STATIC)
    set(MAGNUM_DARTINTEGRATION_BUILD_STATIC 1)
endif()

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/configure.h.cmake
               ${CMAKE_CURRENT_BINARY_DIR}/configure.h)

set(MagnumDartIntegration_SRCS
    ConvertShapeNode.cpp
    DartObject.cpp
    DartSkeleton.cpp)

set(MagnumDartIntegration_HEADERS
    ConvertShapeNode.h
    DartIntegration.h
    DartObject.h
    DartSkeleton.h

    visibility.h)

# DartIntegration library
add_library(MagnumDartIntegration ${SHARED_OR_STATIC}
    ${MagnumDartIntegration_SRCS}
    ${MagnumDartIntegration_HEADERS})
target_include_directories(MagnumDartIntegration PUBLIC
    ${PROJECT_SOURCE_DIR}/src
    ${PROJECT_BINARY_DIR}/src)
# Silence pedantic warnings in DART headers
target_include_directories(MagnumDartIntegration SYSTEM PUBLIC ${DART_INCLUDE_DIRS} PRIVATE ${CMAKE_CURRENT_BINARY_DIR})
set_target_properties(MagnumDartIntegration PROPERTIES
    DEBUG_POSTFIX "-d"
    FOLDER "Magnum/DartIntegration")
if(BUILD_STATIC_PIC)
    set_target_properties(MagnumDartIntegration PROPERTIES POSITION_INDEPENDENT_CODE ON)
endif()
target_link_libraries(MagnumDartIntegration
    Magnum::Magnum
    Magnum::SceneGraph
    Magnum::Primitives
    Magnum::MeshTools
    ${DART_LIBRARIES})

install(TARGETS MagnumDartIntegration
    RUNTIME DESTINATION ${MAGNUM_BINARY_INSTALL_DIR}
    LIBRARY DESTINATION ${MAGNUM_LIBRARY_INSTALL_DIR}
    ARCHIVE DESTINATION ${MAGNUM_LIBRARY_INSTALL_DIR})
install(FILES ${MagnumDartIntegration_HEADERS} DESTINATION ${MAGNUM_INCLUDE_INSTALL_DIR}/DartIntegration)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/configure.h DESTINATION ${MAGNUM_INCLUDE_INSTALL_DIR}/DartIntegration)

if(BUILD_TESTS)
    add_subdirectory(Test)
endif()

# Magnum DART integration target alias for superprojects
add_library(MagnumIntegration::Dart ALIAS MagnumDartIntegration)