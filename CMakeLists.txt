project(kodiutilities)

cmake_minimum_required(VERSION 2.6)
enable_language(CXX)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR})

find_package(kodi REQUIRED)
find_package(TinyXML REQUIRED)
# TODO: find_package(Threads REQUIRED)
include(UseMultiArch.cmake)

set(kodiutilities_NAME kodiutilities)
set(kodiutilities_DESCRIPTION "Kodi add-on utilities library")
set(kodiutilities_VERSION_MAJOR ${APP_VERSION_MAJOR})
set(kodiutilities_VERSION_MINOR ${APP_VERSION_MINOR})
set(kodiutilities_VERSION_PATCH 0)

if(NOT WIN32)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
endif()

set(kodiutilities_INCLUDE_DIRS ${TINYXML_INCLUDE_DIR} "${CMAKE_INSTALL_PREFIX}/include/kodi")
set(kodiutilities_LIBRARIES ${CMAKE_THREAD_LIBS_INIT} ${TINYXML_LIBRARIES})

set(SOURCES src/util/XMLUtils.cpp
            src/util/fstrcmp.c
            src/util/StringUtils.cpp)
include_directories(${TINYXML_INCLUDE_DIR} ${KODI_INCLUDE_DIR})

add_library(kodiutilities ${SOURCES} ${PLAT_SOURCES})
target_link_libraries(kodiutilities ${kodiutilities_LIBRARIES})
set_target_properties(kodiutilities PROPERTIES VERSION ${kodiutilities_VERSION_MAJOR}.${kodiutilities_VERSION_MINOR}.${kodiutilities_VERSION_PATCH}
                                              SOVERSION ${kodiutilities_VERSION_MAJOR}.0)

install(TARGETS kodiutilities DESTINATION ${CMAKE_INSTALL_LIBDIR})
install(FILES src/util/buffer.h
              src/util/StringUtils.h
              src/util/StdString.h
              src/util/util.h
              src/util/XMLUtils.h
        DESTINATION include/kodi/util)

IF(NOT WIN32)
  # Pkgconfig
  include(PkgConfigHandler.cmake)
  configure_pc_file(kodiutilities kodiutilities.pc.in
                                 kodiutilities.pc
                                 ${CMAKE_INSTALL_PREFIX}
                                 ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}
                                 ${CMAKE_INSTALL_PREFIX}/include)

  install(FILES ${CMAKE_BINARY_DIR}/kodiutilities.pc
          DESTINATION ${CMAKE_INSTALL_LIBDIR_NOARCH}/pkgconfig)
ENDIF(NOT WIN32)

# config mode
configure_file (kodiutilities-config.cmake.in
                kodiutilities-config.cmake @ONLY)
install(FILES ${CMAKE_BINARY_DIR}/kodiutilities-config.cmake
        DESTINATION ${CMAKE_INSTALL_LIBDIR_NOARCH}/kodiutilities)

# Quell warnings with in-tree builds
set(KODI_BUILD_DIR ${KODI_BUILD_DIR})
