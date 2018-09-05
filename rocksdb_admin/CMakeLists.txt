cmake_minimum_required(VERSION 3.1)

set(GEN_FILES
    "gen-cpp2/rocksdb_admin_types.tcc"
    "gen-cpp2/rocksdb_admin_types.cpp"
    "gen-cpp2/rocksdb_admin_types.h"
    "gen-cpp2/rocksdb_admin_data.h"
    "gen-cpp2/rocksdb_admin_data.cpp"
    "gen-cpp2/rocksdb_admin_constants.h"
    "gen-cpp2/rocksdb_admin_constants.cpp"
    "gen-cpp2/Admin.tcc"
    "gen-cpp2/Admin.cpp"
    "gen-cpp2/Admin.h"
    "gen-cpp2/AdminAsyncClient.cpp")

add_custom_command(
  OUTPUT ${GEN_FILES}
  COMMAND thrift1 --gen mstch_cpp2:include_prefix=${CMAKE_CURRENT_BINARY_DIR} -I .  --templates /usr/local/include/thrift/templates
  -o ./ ${CMAKE_CURRENT_SOURCE_DIR}/rocksdb_admin.thrift
  DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/rocksdb_admin.thrift
)

AUX_SOURCE_DIRECTORY(./ SRC_FILES)

add_library(rocksdb_admin ${GEN_FILES} ${SRC_FILES})
target_link_libraries(rocksdb_admin thrift thriftcpp2 rocksdb rocksdb_replicator folly glog pthread boost_filesystem rocksdb_glogger common jvm)

add_subdirectory(tests)
