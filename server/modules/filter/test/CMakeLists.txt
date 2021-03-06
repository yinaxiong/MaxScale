aux_source_directory(${CMAKE_SOURCE_DIR}/server/core CORE_ALL)
foreach(VAR ${CORE_ALL})
  if(NOT( (${VAR} MATCHES "max[a-z_]*.c") OR (${VAR} MATCHES "gateway.c")))
	list(APPEND CORE ${VAR})
  endif()
endforeach()

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
add_executable(harness_ui harness_ui.c harness_common.c)
add_executable(harness harness_util.c harness_common.c ${CORE})
target_link_libraries(harness_ui fullcore log_manager utils)
target_link_libraries(harness fullcore)
execute_process(COMMAND ${CMAKE_COMMAND} -E copy ${ERRMSG} ${CMAKE_CURRENT_BINARY_DIR})
add_test(TestHintfilter /bin/sh -c "MAXSCALE_HOME=\"${CMAKE_BINARY_DIR}\" ${CMAKE_CURRENT_BINARY_DIR}/harness -i ${CMAKE_CURRENT_SOURCE_DIR}/hint_testing.input -o ${CMAKE_CURRENT_BINARY_DIR}/hint_testing.output -c ${CMAKE_CURRENT_SOURCE_DIR}/hint_testing.cnf -t 1 -s 1 -e ${CMAKE_CURRENT_SOURCE_DIR}/hint_testing.expected")

add_test(TestRegexfilter /bin/sh -c "MAXSCALE_HOME=\"${CMAKE_BINARY_DIR}\" ${CMAKE_CURRENT_BINARY_DIR}/harness -i ${CMAKE_CURRENT_SOURCE_DIR}/regextest.input -o ${CMAKE_CURRENT_BINARY_DIR}/regextest.output -c ${CMAKE_CURRENT_SOURCE_DIR}/regextest.cnf -t 1 -s 1 -e ${CMAKE_CURRENT_SOURCE_DIR}/regextest.expected")

add_test(TestTeeRecursion ${CMAKE_CURRENT_SOURCE_DIR}/tee_recursion.sh
  ${CMAKE_BINARY_DIR}
  ${CMAKE_SOURCE_DIR}
  ${TEST_USER}
  ${TEST_PASSWORD}
  ${TEST_HOST}
  ${TEST_PORT})
