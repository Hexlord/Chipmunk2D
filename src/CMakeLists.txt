file(GLOB CHIPMUNK_SOURCE_FILES "${CMAKE_CURRENT_SOURCE_DIR}/*.c")
file(GLOB CHIPMUNK_INCLUDE_FILES "${CMAKE_CURRENT_SOURCE_DIR}/../include/chipmunk/*.h")

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../include)

# Chipmunk2D 7.0.3
set(CHIPMUNK_VERSION_MAJOR 7)
set(CHIPMUNK_VERSION_MINOR 0)
set(CHIPMUNK_VERSION_PATCH 3)
set(CHIPMUNK_VERSION "${CHIPMUNK_VERSION_MAJOR}.${CHIPMUNK_VERSION_MINOR}.${CHIPMUNK_VERSION_PATCH}")
message("Configuring Chipmunk2D version ${CHIPMUNK_VERSION}")


if(CHIPMUNK_BUILD_SHARED)
  add_library(chipmunk SHARED
    ${CHIPMUNK_SOURCE_FILES}
  )
  # Tell MSVC to compile the code as C++.
  if(MSVC)
    set_source_files_properties(${CHIPMUNK_SOURCE_FILES} PROPERTIES LANGUAGE CXX)
    set_target_properties(chipmunk PROPERTIES LINKER_LANGUAGE CXX)
  endif(MSVC)
  # set the lib's version number
  # But avoid on Android because symlinks to version numbered .so's don't work with Android's Java-side loadLibrary.
  if(NOT ANDROID)
    set_target_properties(chipmunk PROPERTIES
      SOVERSION ${CHIPMUNK_VERSION_MAJOR}
      VERSION ${CHIPMUNK_VERSION})
  endif(NOT ANDROID)
  if(ANDROID OR UNIX)
	  # need to explicitly link to the math library because the CMake/Android toolchains may not do it automatically
	  target_link_libraries(chipmunk m)
  endif(ANDROID OR UNIX)
  install(TARGETS chipmunk RUNTIME DESTINATION ${CHIPMUNK_BIN_INSTALL_DIR}
                           LIBRARY DESTINATION ${CHIPMUNK_LIB_INSTALL_DIR}
                           ARCHIVE DESTINATION ${CHIPMUNK_LIB_INSTALL_DIR})
endif(CHIPMUNK_BUILD_SHARED)

if(CHIPMUNK_BUILD_STATIC)
  add_library(chipmunk-static STATIC
    ${CHIPMUNK_SOURCE_FILES}
  )
  # Tell MSVC to compile the code as C++.
  if(MSVC)
    set_source_files_properties(${CHIPMUNK_SOURCE_FILES} PROPERTIES LANGUAGE CXX)
    set_target_properties(chipmunk-static PROPERTIES LINKER_LANGUAGE CXX)
  endif(MSVC)
  # Sets chipmunk-static to output "libchipmunk.a" not "libchipmunk-static.a"
  #set_target_properties(chipmunk-static PROPERTIES OUTPUT_NAME "chipmunk")
  if(CHIPMUNK_INSTALL_STATIC)
    install(TARGETS chipmunk-static ARCHIVE DESTINATION ${CHIPMUNK_LIB_INSTALL_DIR})
  endif(CHIPMUNK_INSTALL_STATIC)
endif(CHIPMUNK_BUILD_STATIC)

if(CHIPMUNK_BUILD_SHARED OR CHIPMUNK_INSTALL_STATIC)
  # FIXME: change to PUBLIC_HEADER to allow building frameworks
  install(FILES ${CHIPMUNK_INCLUDE_FILES} DESTINATION include/chipmunk)
endif(CHIPMUNK_BUILD_SHARED OR CHIPMUNK_INSTALL_STATIC)
