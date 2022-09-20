# CMakeList.txt : CMake project for XPlane-Plugin-Template, include source and define
# project specific logic here.
#
cmake_minimum_required (VERSION 3.8)

# Add source to this project's executable - Can use *.cpp/*.h and other expressions for multiple files.
FILE(GLOB PLUGIN_SRC "${CMAKE_CURRENT_SOURCE_DIR}/*.cpp")
FILE(GLOB PLUGIN_HDR "${CMAKE_CURRENT_SOURCE_DIR}/*.h")
set(SOURCE_FILES ${PLUGIN_SRC} ${PLUGIN_HDR})

message(STATUS "XPLANESDK = $ENV{XPLANESDK}")
message(STATUS "SOURCE_FILES = ${SOURCE_FILES}")

# Add the library with its source files
# Must do it first other later target_() calls won't know about the library
if(APPLE)
    add_executable(${PRODUCT_NAME} ${SOURCE_FILES})
    set_target_properties(${PRODUCT_NAME} PROPERTIES LINK_FLAGS "${CMAKE_SHARED_LINKER_FLAGS}  -fPIC -fvisibility=hidden -bundle -framework OpenGL -framework Cocoa")
elseif(UNIX)
    add_library(${PRODUCT_NAME} SHARED ${SOURCE_FILES})
    set_target_properties(${PRODUCT_NAME} PROPERTIES LINK_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -rdynamic -nodefaultlibs -undefined_warning -fPIC -fvisibility=hidden")
elseif(WIN32)
    add_library(${PRODUCT_NAME} MODULE ${SOURCE_FILES})
	target_compile_definitions(${PRODUCT_NAME} PUBLIC -D_CRT_SECURE_NO_WARNINGS /bigobj)
endif()

# Configure X-Plane SDK min version definitions
IF(LIBRARY_VERSION EQUAL 200)
    TARGET_COMPILE_DEFINITIONS(${PRODUCT_NAME} PUBLIC -DXPLM200=1)
ELSEIF(LIBRARY_VERSION EQUAL 210)
    TARGET_COMPILE_DEFINITIONS(${PRODUCT_NAME} PUBLIC -DXPLM200=1 -DXPLM210=1)
ELSEIF(LIBRARY_VERSION EQUAL 300)
    TARGET_COMPILE_DEFINITIONS(${PRODUCT_NAME} PUBLIC -DXPLM200=1 -DXPLM210=1 -DXPLM300=1)
ELSEIF(LIBRARY_VERSION EQUAL 301)
    TARGET_COMPILE_DEFINITIONS(${PRODUCT_NAME} PUBLIC -DXPLM200=1 -DXPLM210=1 -DXPLM300=1 -DXPLM301=1)
ELSEIF(LIBRARY_VERSION EQUAL 302)
    TARGET_COMPILE_DEFINITIONS(${PRODUCT_NAME} PUBLIC -DXPLM200=1 -DXPLM210=1 -DXPLM300=1 -DXPLM301=1 -DXPLM302=1)
ELSEIF(LIBRARY_VERSION EQUAL 303)
    TARGET_COMPILE_DEFINITIONS(${PRODUCT_NAME} PUBLIC -DXPLM200=1 -DXPLM210=1 -DXPLM300=1 -DXPLM301=1 -DXPLM302=1 -DXPLM303=1)
ELSEIF(LIBRARY_VERSION EQUAL 400)
    TARGET_COMPILE_DEFINITIONS(${PRODUCT_NAME} PUBLIC -DXPLM200=1 -DXPLM210=1 -DXPLM300=1 -DXPLM301=1 -DXPLM302=1 -DXPLM303=1 -DXPLM400=1)
ELSE()
    MESSAGE(FATAL_ERROR "Library version one of: 200, 210, 300, 301, 302, 303, 400")
ENDIF()

# Add the X-Plane SDK header paths.
target_include_directories(${PRODUCT_NAME} PUBLIC $ENV{XPLANESDK}/CHeaders/XPLM)
target_include_directories(${PRODUCT_NAME} PUBLIC $ENV{XPLANESDK}/CHeaders/Widgets)
target_include_directories(${PRODUCT_NAME} PUBLIC $ENV{XPLANESDK}/CHeaders/Wrappers)

# Add the X-Plane SDK .libs
if(APPLE)
    target_link_directories(${PRODUCT_NAME} PUBLIC $ENV{XPLANESDK}/Libraries/Mac)
    target_link_libraries(${PRODUCT_NAME} PUBLIC XPLM_64 XPWidgets_64)
elseif(WIN32)
    target_link_directories(${PRODUCT_NAME} PUBLIC $ENV{XPLANESDK}/Libraries/Win)
    target_link_libraries(${PRODUCT_NAME} PUBLIC XPLM_64 XPWidgets_64)
endif()
if(UNIX)
    target_include_directories(${PRODUCT_NAME} PUBLIC "/usr/include")
endif()

# Add Boost Libraries
set(Boost_USE_STATIC_LIBS ON)
set(Boost_USE_MULTITHREADED ON) 
find_package(Boost REQUIRED COMPONENTS filesystem)
include_directories(${Boost_INCLUDE_DIR})
target_link_libraries(${library_name} Boost::filesystem)

# Set the Output Name
SET_TARGET_PROPERTIES(${PRODUCT_NAME} PROPERTIES OUTPUT_NAME "${PRODUCT_NAME}.xpl")
SET_TARGET_PROPERTIES(${PRODUCT_NAME} PROPERTIES SUFFIX "")

# Set the output locations
IF(APPLE)
    SET_TARGET_PROPERTIES(${PRODUCT_NAME} PROPERTIES LIBRARY_OUTPUT_DIRECTORY_DEBUG "$ENV{XPLANEDIR}/Resources/plugins/${PRODUCT_NAME}/mac_x64")
ELSEIF(UNIX)
    SET_TARGET_PROPERTIES(${PRODUCT_NAME} PROPERTIES LIBRARY_OUTPUT_DIRECTORY_DEBUG "$ENV{XPLANEDIR}/Resources/plugins/${PRODUCT_NAME}/lin_x64")
ELSEIF(WIN32)
    SET_TARGET_PROPERTIES(${PRODUCT_NAME} PROPERTIES LIBRARY_OUTPUT_DIRECTORY_DEBUG "$ENV{XPLANEDIR}/Resources/plugins/${PRODUCT_NAME}/win_x64")
ENDIF()

#set_target_properties(${PRODUCT_NAME} PROPERTIES RUNTIME_OUTPUT_DIRECTORY_DEBUG <release path>

# TODO: Add tests and install targets if needed.
