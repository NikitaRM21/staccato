cmake_minimum_required(VERSION 3.5)
project(STACCATO)

set (SRCS
    src/DSDDecompose.c
    src/DSDInterface.c
    src/DSDManager.c
    src/DSDManipulations.c
    src/DSDNewDecompose.c
    src/DSDOrDecompose.c
    src/DSDPrimeDecompose.c
    src/DSDUtilities.c
    src/DSDXorDecompose.c
    src/fixheap.c
)

add_compile_options(-O3 -funroll-all-loops)

option(BUILD_SHARED_LIBS "Build shared libraries (ON) or static libraries (OFF)" OFF)

# Check if building library is shared 
if(BUILD_SHARED_LIBS)
    if(DEFINED ENV{UTOPIA_HOME})
        message(STATUS "UTOPIA_HOME is set to: $ENV{UTOPIA_HOME}")
        add_library(STACCATO SHARED ${SRCS})

        if(DEFINED ENV{CUDD_DIR})
            set(CUDD_LIBRARY_PATH "$ENV{CUDD_DIR}/libcudd.so")
        else()
            set(CUDD_LIBRARY_PATH "/usr/local/lib/libcudd.so")
        endif()

        target_link_libraries(STACCATO PUBLIC ${CUDD_LIBRARY_PATH})
    else()
        message(FATAL_ERROR "UTOPIA_HOME environment variable is not set.")
    endif()
    
else()
    add_library(STACCATO STATIC ${SRCS})
endif()

target_include_directories(STACCATO
    PUBLIC
    #path/to/your/cudd/include/dir
)

