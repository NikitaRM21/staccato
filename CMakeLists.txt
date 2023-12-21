################################################################################
#   Staccato: Disjoint Support Decompositions from BDDs                        #
#   Copyright (C) 2003-2010  University of Michigan                            #
#   http://www.eecs.umich.edu/staccato                                         #
#                                                                              #
#   Contributors include Stephen Plaza and Valeria Bertacco                    #
#                                                                              #
#   This library is free software; you can redistribute it and/or              #
#   modify it under the terms of the GNU Lesser General Public                 #
#   License as published by the Free Software Foundation; either               #
#   version 2.1 of the License, or (at your option) any later version.         #
#                                                                              #
#   This library is distributed in the hope that it will be useful,            #
#   but WITHOUT ANY WARRANTY; without even the implied warranty of             #
#   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU          #
#   Lesser General Public License for more details.                            #
#                                                                              #
#   You should have received a copy of the GNU Lesser General Public           #
#   License along with this library; if not, write to the Free Software        #
#   Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA              #
#   02110-1301  USA                                                            #
#                                                                              #
#   Stephen Plaza <splaza@umich.edu>                                           #
#   Valeria Bertacco <valeria@umich.edu>                                       #
#                                                                              #
#   University of Michigan                                                     #
#   Electrical Engineering and Computer Science Dept.                          #
#   2260 Hayward St.                                                           #
#   Ann Arbor, MI 48109-2121                                                   #
################################################################################

cmake_minimum_required(VERSION 3.13)
project(STACCATO)

set(SRCS
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
option(CUDD_INCLUDE_PATH "Path to CUDD include directory")

# Check if building library is shared 
if(BUILD_SHARED_LIBS)
        add_library(STACCATO SHARED ${SRCS})

        if(DEFINED ENV{CUDD_DIR})
            set(CUDD_LIBRARY_PATH "$ENV{CUDD_DIR}/libcudd.so")
        else()
            set(CUDD_LIBRARY_PATH "/usr/local/lib/libcudd.so")
        endif()

        target_link_libraries(STACCATO PUBLIC ${CUDD_LIBRARY_PATH})
    
else()
    add_library(STACCATO STATIC ${SRCS})
endif()

if(CUDD_INCLUDE_PATH)
    target_include_directories(STACCATO
        PUBLIC
        ${CUDD_INCLUDE_PATH}
    )
else()
    message(FATAL_ERROR "You must specify the path to the CUDD include directory. Use -DCUDD_INCLUDE_PATH=/path/to/your/cudd/include")
endif()

# Custom target for clean
add_custom_target(cleaning
    COMMAND rm -f ${CMAKE_CURRENT_SOURCE_DIR}/libSTACCATO.a
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    COMMENT "Cleaning libSTACCATO.a"
)
