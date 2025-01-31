cmake_minimum_required(VERSION 3.16)

project(FigmaQML LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)


if(LET_QT5)
    find_package(Qt5 QUIET COMPONENTS Core Quick Network Widgets Concurrent)
endif()

find_package(Qt6 QUIET COMPONENTS Core Quick Network Widgets Concurrent Core5Compat)

if(NOT Qt5_FOUND AND NOT Qt6_FOUND)
    message(FATAL_ERROR "Qt5 or Qt6 is not found!")
endif()

if(Qt5_FOUND)
    message("Project is Qt5")
endif()

if(Qt6_FOUND)
    message("Project is Qt6")
endif()


set(VERSION_NUMBER "1.0.1")

add_compile_definitions(VERSION_NUMBER=${VERSION_NUMBER})

add_executable(FigmaQML
  src/main.cpp
  qml/qml.qrc
  src/figmaget.cpp
  include/figmaget.h
  src/figmaqml.cpp
  include/figmaqml.h
  include/clipboard.h
  include/figmaparser.h
  include/downloads.h
  src/downloads.cpp
  include/figmadata.h
  include/figmadocument.h
  include/fontcache.h
)

if(WIN32)
  #  Windows does not support dual mode, we pick cmd mode
  #  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /SUBSYSTEM:WINDOWS")
  #  set (EXTRA PUBLIC Qt5::WinMain)
endif()

if(MINGW)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated-declarations")
endif()

if(UNIX)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated-declarations")
endif()

#set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXXFLAGS_DEBUG} -DNON_CONCURRENT")

if (APPLE)
    set_target_properties(FigmaQML PROPERTIES
            MACOSX_BUNDLE ON
            )
endif()

include_directories(include)

target_compile_definitions(FigmaQML
  PRIVATE $<$<OR:$<CONFIG:Debug>,$<CONFIG:RelWithDebInfo>>:QT_QML_DEBUG>)

if(Qt5_FOUND)
    target_compile_definitions(FigmaQML PUBLIC -DQT5)
    target_link_libraries(FigmaQML
        PRIVATE Qt5::Core Qt5::Quick Qt5::Network Qt5::Widgets Qt5::Concurrent ${EXTRA})
else()
    target_link_libraries(FigmaQML
      PRIVATE Qt6::Core Qt6::Quick Qt6::Network Qt6::Widgets Qt6::Concurrent Qt6::Core5Compat ${EXTRA})
endif()
