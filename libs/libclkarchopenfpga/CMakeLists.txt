cmake_minimum_required(VERSION 3.9)

project("libclkarchopenfpga")

file(GLOB_RECURSE EXEC_SOURCES test/*.cpp)
file(GLOB_RECURSE LIB_SOURCES src/*.cpp)
file(GLOB_RECURSE LIB_HEADERS src/*.h)
files_to_dirs(LIB_HEADERS LIB_INCLUDE_DIRS)

#Remove test executable from library
list(REMOVE_ITEM LIB_SOURCES ${EXEC_SOURCES})

#Create the library
add_library(libclkarchopenfpga STATIC
             ${LIB_HEADERS}
             ${LIB_SOURCES})
target_include_directories(libclkarchopenfpga PUBLIC ${LIB_INCLUDE_DIRS})
set_target_properties(libclkarchopenfpga PROPERTIES PREFIX "") #Avoid extra 'lib' prefix

#Specify link-time dependancies
target_link_libraries(libclkarchopenfpga
                      libopenfpgautil
                      libopenfpgashell
                      libarchopenfpga
                      librrgraph
                      libvtrutil
                      libpugiutil)

#Create the test executable
foreach(testsourcefile ${EXEC_SOURCES})
    # Use a simple string replace, to cut off .cpp.
    get_filename_component(testname ${testsourcefile} NAME_WE)
    add_executable(${testname} ${testsourcefile})
    # Make sure the library is linked to each test executable
    target_link_libraries(${testname} libclkarchopenfpga)
endforeach(testsourcefile ${EXEC_SOURCES})

install(TARGETS libclkarchopenfpga
        DESTINATION bin
        COMPONENT openfpga_package
)
