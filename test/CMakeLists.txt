# Meta
project(tests)

# Dependencies
cmake_minimum_required(VERSION 3.1.0)

find_package(Boost 1.55 COMPONENTS
  date_time
  filesystem
  system
  unit_test_framework
  context
  coroutine
  regex
  REQUIRED)

# Config

if(NOT Boost_USE_STATIC_LIBS)
  add_definitions(-DBOOST_TEST_DYN_LINK)
endif()

link_directories("${target}" ${Boost_LIBRARIES_DIRS})

# Test's stuff

enable_testing()

set(tests
  "request_good"
  "request_bad_a"
  "request_bad_b"
  "response"
  "chunk_size"
  "content_length"
  "common"
  "utils"
  "request_response_common"
  "parser_dont_violate_odr"
)

set(tests11
  "errc"
  "algorithm"
  "datetime"
  "mocksocket"
  "socket"
  "traits"
  "file_server"
  "request11"
  "routing"
  "request_response_wrapper"
  "socket_state"
  "client_socket"
)

macro(add_test_target target version)
  add_executable("${target}" "${target}.cpp")

  set_property(TARGET "${target}" PROPERTY CXX_STANDARD ${version})
  set_property(TARGET "${target}" PROPERTY CXX_STANDARD_REQUIRED ON)

  if(WIN32)
    if(MSVC)
      set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /SAFESEH:NO")
      set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} /SAFESEH:NO")
      set(CMAKE_MODULE_LINKER_FLAGS "${CMAKE_MODULE_LINKER_FLAGS} /SAFESEH:NO")
    endif()
  endif()

  target_compile_definitions("${target}"
    PRIVATE BOOST_COROUTINES_NO_DEPRECATION_WARNING)

  target_include_directories("${target}"
    PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/../include" ${Boost_INCLUDE_DIR})

  target_link_libraries("${target}"
    ${Boost_DATE_TIME_LIBRARY}
    ${Boost_FILESYSTEM_LIBRARY}
    ${Boost_SYSTEM_LIBRARY}
    ${Boost_UNIT_TEST_FRAMEWORK_LIBRARY}
    ${Boost_COROUTINE_LIBRARY}
    ${Boost_CONTEXT_LIBRARY}
    ${Boost_REGEX_LIBRARY})

  add_test(NAME "${target}" COMMAND $<TARGET_FILE:${target}>)
endmacro()

foreach(test ${tests})
  add_test_target("${test}" 98)
endforeach()

foreach(test ${tests11})
  add_test_target("${test}" 11)
endforeach()

include(CTest)
