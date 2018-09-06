cmake_minimum_required(VERSION 3.1)

# Build thrift file
set(GEN_FILES
    "gen-cpp2/counter_types.tcc"
    "gen-cpp2/counter_types.cpp"
    "gen-cpp2/counter_types.h"
    "gen-cpp2/counter_constants.h"
    "gen-cpp2/counter_constants.cpp"
    "gen-cpp2/counter_data.h"
    "gen-cpp2/counter_data.cpp"
    "gen-cpp2/Counter.tcc"
    "gen-cpp2/Counter.cpp"
    "gen-cpp2/Counter.h"
    "gen-cpp2/CounterAsyncClient.cpp")

add_custom_command(
  OUTPUT ${GEN_FILES}
  COMMAND thrift1 --gen mstch_cpp2:include_prefix=${CMAKE_CURRENT_BINARY_DIR} --templates /usr/local/include/thrift/templates/
  -I ${CMAKE_CURRENT_SOURCE_DIR}/../../.. -o ./ ${CMAKE_CURRENT_SOURCE_DIR}/counter.thrift
  DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/counter.thrift
  DEPENDS rocksdb_admin
)
INCLUDE_DIRECTORIES( ${CMAKE_BINARY_DIR}/rocksdb_admin/gen-cpp2 )
add_library(counter_thrift ${GEN_FILES})
target_link_libraries(counter_thrift thrift thriftprotocol thriftcpp2 thrift-core folly async protocol security transport concurrency com_err ssl crypto)
