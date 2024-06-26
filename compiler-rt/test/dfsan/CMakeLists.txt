set(DFSAN_LIT_TESTS_DIR ${CMAKE_CURRENT_SOURCE_DIR})

set(DFSAN_TESTSUITES)
 
set(DFSAN_TEST_ARCH ${DFSAN_SUPPORTED_ARCH})
if(APPLE)
  darwin_filter_host_archs(DFSAN_SUPPORTED_ARCH DFSAN_TEST_ARCH)
endif()

foreach(arch ${DFSAN_TEST_ARCH})
  set(DFSAN_TEST_TARGET_ARCH ${arch})
  string(TOLOWER "-${arch}" DFSAN_TEST_CONFIG_SUFFIX)
  get_test_cc_for_arch(${arch} DFSAN_TEST_TARGET_CC DFSAN_TEST_TARGET_CFLAGS)
  string(TOUPPER ${arch} ARCH_UPPER_CASE)
  set(CONFIG_NAME ${ARCH_UPPER_CASE}Config)

  configure_lit_site_cfg(
    ${CMAKE_CURRENT_SOURCE_DIR}/lit.site.cfg.py.in
    ${CMAKE_CURRENT_BINARY_DIR}/${CONFIG_NAME}/lit.site.cfg.py)
  list(APPEND DFSAN_TESTSUITES ${CMAKE_CURRENT_BINARY_DIR}/${CONFIG_NAME})
endforeach()

set(DFSAN_TEST_DEPS ${SANITIZER_COMMON_LIT_TEST_DEPS})
list(APPEND DFSAN_TEST_DEPS dfsan)

add_lit_testsuite(check-dfsan "Running the DataFlowSanitizer tests"
  ${DFSAN_TESTSUITES}
  DEPENDS ${DFSAN_TEST_DEPS})
