
if(CMAKE_COMPILER_IS_GNUCXX OR "${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
    add_compile_options(-Wall -Wextra -Wpedantic)
endif()

if(MSVC)
    add_compile_options(/W4 /permissive-)
endif()

set(CMAKE_CXX_EXTENSIONS Off)

add_library(catch_main catch_main.cpp)
set_target_properties(catch_main PROPERTIES
    CXX_STANDARD ${TCB_SPAN_TEST_CXX_STD})

set(TEST_FILES
    test_span.cpp
)

if(${TCB_SPAN_TEST_CXX_STD} GREATER 17 OR ${TCB_SPAN_TEST_CXX_STD} EQUAL 17)
    list(APPEND TEST_FILES
        test_deduction_guides.cpp
        test_structured_bindings.cpp)
endif()

add_executable(test_span ${TEST_FILES})
target_link_libraries(test_span PUBLIC span catch_main)
set_target_properties(test_span PROPERTIES
    CXX_STANDARD ${TCB_SPAN_TEST_CXX_STD})
add_test(NAME test_span COMMAND test_span)

add_executable(test_span_contract_checking
    test_contract_checking.cpp)
target_link_libraries(test_span_contract_checking PUBLIC span catch_main)
set_target_properties(test_span_contract_checking PROPERTIES
    CXX_STANDARD ${TCB_SPAN_TEST_CXX_STD})
add_test(NAME test_span_contract_checking COMMAND test_span_contract_checking)
