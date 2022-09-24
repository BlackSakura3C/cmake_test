cmake_minimum_required(VERSION 3.10)

# set the project name
project(Tutorial VERSION 1.0)

# specify the c++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)

option(USE_MYMATH "Use tutorial provided math implementation" ON)

if(USE_MYMATH)
    add_subdirectory(MathFunctions)
    list(APPEND EXTRA_LIBS MathFunctions)
    list(APPEND EXTRA_INCLUDE "${PROJECT_SOURCE_DIR}/MathFunctions")
endif()

# configure a header file to pass some of the CMake settings to the source code
configure_file(TutorialConfig.h.in TutorialConfig.h)

# add the executable
add_executable(Tutorial tutorial.cxx)

target_link_libraries(Tutorial PUBLIC ${EXTRA_LIBS})

target_include_directories(Tutorial PUBLIC
    "${PROJECT_BINARY_DIR}"
    ${EXTRA_INCLUDE})

install(TARGETS Tutorial DESTINATION bin)
install(FILES ${PROJECT_BINARY_DIR}/TutorialConfig.h DESTINATION include)