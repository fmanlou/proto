find_package(protobuf CONFIG REQUIRED)
find_package(gRPC CONFIG REQUIRED)

set(PROTO_FILES ${CMAKE_CURRENT_SOURCE_DIR}/data.proto
                ${CMAKE_CURRENT_SOURCE_DIR}/service.proto)
set(PROTO_TARGET proto)

add_library(${PROTO_TARGET} ${PROTO_FILES})
target_link_libraries(
  ${PROTO_TARGET} PUBLIC protobuf::libprotobuf gRPC::grpc gRPC::grpc++
                         gRPC::grpc++_reflection)
target_include_directories(${PROTO_TARGET} PUBLIC ${CMAKE_CURRENT_BINARY_DIR})

get_target_property(grpc_cpp_plugin_location gRPC::grpc_cpp_plugin LOCATION)
protobuf_generate(TARGET ${PROTO_TARGET} LANGUAGE cpp)
protobuf_generate(
  TARGET
  ${PROTO_TARGET}
  LANGUAGE
  grpc
  GENERATE_EXTENSIONS
  .grpc.pb.h
  .grpc.pb.cc
  PLUGIN
  "protoc-gen-grpc=${grpc_cpp_plugin_location}")
