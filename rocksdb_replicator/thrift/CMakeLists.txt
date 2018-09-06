cmake_minimum_required(VERSION 3.1)

# Build thrift file
set(GEN_FILES
    "gen-cpp2/replicator_types.tcc"
    "gen-cpp2/replicator_types.cpp"
    "gen-cpp2/replicator_types.h"
    "gen-cpp2/replicator_data.h"
    "gen-cpp2/replicator_data.cpp"
    "gen-cpp2/replicator_constants.h"
    "gen-cpp2/replicator_constants.cpp"
    "gen-cpp2/Replicator.tcc"
    "gen-cpp2/Replicator.cpp"
    "gen-cpp2/Replicator.h"
    "gen-cpp2/ReplicatorAsyncClient.cpp")


add_custom_command(
  OUTPUT ${GEN_FILES}
  COMMAND thrift1 --gen mstch_cpp2:process_in_event_base,include_prefix=${CMAKE_CURRENT_BINARY_DIR} --templates /usr/local/include/thrift/templates
          -o ./ ${CMAKE_CURRENT_SOURCE_DIR}/replicator.thrift
  DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/replicator.thrift)

add_library(replicator_thrift ${GEN_FILES})
target_link_libraries(replicator_thrift thrift thriftprotocol thrift-core thriftfrozen2 wangle protocol transport concurrency async gssapi_krb5 ssl crypto)
