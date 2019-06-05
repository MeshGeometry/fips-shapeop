if (NOT FIPS_IMPORT)
    cmake_minimum_required(VERSION 2.8)
    get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
    include("${FIPS_ROOT_DIR}/cmake/fips.cmake")
    fips_setup()
    fips_project(fips-shapeop)
endif()

if ((FIPS_MACOS OR FIPS_WINDOWS OR FIPS_LINUX) AND NOT (FIPS_RASPBERRYPI OR FIPS_OSX_USE_ARC OR FIPS_UWP))

     fips_begin_lib(libShapeOp)

    include_directories(
        "libShapeOp/src"
        "libShapeOp/api"
        "${FIPS_ROOT_DIR}/../libigl/libigl/external/eigen"
        )
    fips_files(
        "libShapeOp/src/Common.h"
        "libShapeOp/src/Constraint.h"
        "libShapeOp/src/Constraint.cpp"
        "libShapeOp/src/Solver.h"
        "libShapeOp/src/Solver.cpp"
        "libShapeOp/src/Force.h"
        "libShapeOp/src/Force.cpp"
        "libShapeOp/src/LSSolver.h"
        "libShapeOp/src/LSSolver.cpp"
        "libShapeOp/src/Types.h"
        "libShapeOp/api/API.h"
        "libShapeOp/api/API.cpp"
        )
    fips_end_lib()

    # Compiler flags
    # if(CMAKE_CXX_COMPILER_ID MATCHES "Clang") #Tests for Clang and AppleClang which is defined in CMake 3.0 and higher
    #     message("Clang.")
    #     set(CMAKE_CXX_FLAGS "-std=c++11 -stdlib=libc++ -Wall -g")
    #     set(CMAKE_CXX_FLAGS_RELEASE "-O3 -DNDEBUG")
    # #    set(CMAKE_CXX_FLAGS_DEBUG "-g")
    #     add_definitions(-DSHAPEOP_CLANG)
    #     add_definitions(-DSHAPEOP_EXPORT)
    #     set(OPENMP OFF CACHE BOOL "OpenMP")
    # elseif("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    #     message("GCC.")
    #     set(CMAKE_CXX_FLAGS "-std=c++11 -Wall -g")
    #     set(CMAKE_CXX_FLAGS_RELEASE "-Ofast -DNDEBUG")
    # #    set(CMAKE_CXX_FLAGS_DEBUG "-g")
    #     add_definitions(-DSHAPEOP_GCC)
    #     add_definitions(-DSHAPEOP_EXPORT)
    #     set(OPENMP ON CACHE BOOL "OpenMP")
    # elseif("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Intel")
    #     message("ICC.")
    #     if(WIN32)
    #         set(CMAKE_CXX_FLAGS "/Qstd=c++11")
    #         set(CMAKE_CXX_FLAGS_RELEASE "/O3 /DNDEBUG")
    #         add_definitions(/DSHAPEOP_ICC)
    #         add_definitions(/DSHAPEOP_EXPORT)
    #     else()
    #         set(CMAKE_CXX_FLAGS "-std=c++11")
    #         set(CMAKE_CXX_FLAGS_RELEASE "-O3 -DNDEBUG")
    #         add_definitions(-DSHAPEOP_ICC)
    #         add_definitions(-DSHAPEOP_EXPORT)
    #     endif()
    #     set(OPENMP OFF CACHE BOOL "OpenMP")
    if(MSVC)
        message("MSVC.")
        set(CMAKE_CXX_FLAGS_DEBUG "/MTd")
        set(CMAKE_CXX_FLAGS_RELEASE "/MT /Ox /Ot /fp:fast /GS- /GL /DNDEBUG")
        set(CMAKE_EXE_LINKER_FLAGS_RELEASE "/LTCG /INCREMENTAL:NO /OPT:REF")
        add_definitions(/DSHAPEOP_MSVC)
        add_definitions(/DSHAPEOP_EXPORT)
        set(OPENMP ON CACHE BOOL "OpenMP")
    endif()

    if (!FIPS_LINUX) # Having a hard time getting this working on linux
        FIND_PACKAGE(OpenMP QUIET)
    endif()
    if(OPENMP_FOUND)
      message("OpenMP found. OpenMP activated in release.")
      set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} ${OpenMP_CXX_FLAGS}")
      set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} ${OpenMP_C_FLAGS}")
      set(CMAKE_EXE_LINKER_FLAGS_RELEASE "${CMAKE_EXE_LINKER_FLAGS_RELEASE} ${OpenMP_EXE_LINKER_FLAGS}")
      add_definitions(-DSHAPEOP_OPENMP)
    else()
      message("OpenMP not found.")
    endif()

endif()

if (NOT FIPS_IMPORT)
    fips_finish()
endif()