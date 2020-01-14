add_executable(main main.cpp)
target_link_libraries(main ${CONAN_LIBS})
add_subdirectory(${FLATBUFFERS_SRC_DIR}
                 ${CMAKE_CURRENT_BINARY_DIR}/flatbuffers-build
                 EXCLUDE_FROM_ALL)
# Now simply link against flatbuffers as needed to your already declared target.
# The flatbuffers target carry header search path automatically if CMake > 2.8.11.
target_link_libraries(main PRIVATE flatbuffers)

set(COMMON_GENERATED_DIR "${CMAKE_CURRENT_BINARY_DIR}/generated")

file(GLOB FLATBUFFER_SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/*.fbs
)
set(FLATBUFFERS_FLATC_SCHEMA_EXTRA_ARGS "--gen-qobject-api;--gen-object-api")
build_flatbuffers(${FLATBUFFER_SOURCES} 
        "" 
        fbs_target 
        "" 
        "${COMMON_GENERATED_DIR}/fbgenerated" 
        "" 
        "")
add_dependencies(main fbs_target)
target_include_directories(main
PUBLIC    
"${COMMON_GENERATED_DIR}"
"${FLATBUFFERS_INCLUDE_DIRS}")
