cmake_minimum_required(VERSION 3.30)
project(tiny_csg LANGUAGES CXX)

option(TINYCSG_BUILD_DEMO "Build tiny_csg demo" ON)
option(TINYCSG_USE_VENDORED_GLM "Use vendored copy of GLM library" ON)

add_library(csg STATIC
	${PROJECT_SOURCE_DIR}/csg.cpp
	${PROJECT_SOURCE_DIR}/csg.hpp
	${PROJECT_SOURCE_DIR}/csg_private.hpp
	${PROJECT_SOURCE_DIR}/query_point.cpp
	${PROJECT_SOURCE_DIR}/query_box.cpp
	${PROJECT_SOURCE_DIR}/query_ray.cpp
	${PROJECT_SOURCE_DIR}/query_frustum.cpp
	${PROJECT_SOURCE_DIR}/rebuild.cpp
)

target_compile_options(csg PRIVATE -Wall -Wextra -Wpedantic)
set_target_properties(csg PROPERTIES CXX_STANDARD_REQUIRED ON CXX_STANDARD 20)

if(${TINYCSG_USE_VENDORED_GLM})
	target_include_directories(csg PUBLIC ${PROJECT_SOURCE_DIR}/3rdp/glm)
else()
	find_package(glm REQUIRED)
	target_link_libraries(csg PUBLIC glm)
endif()

if(${TINYCSG_BUILD_DEMO})
	find_package(SDL2 REQUIRED)
	find_package(GLEW REQUIRED)
	add_executable(demo
		${PROJECT_SOURCE_DIR}/demo.cpp
		${PROJECT_SOURCE_DIR}/demo_flythrough_camera.cpp
		${PROJECT_SOURCE_DIR}/3rdp/shader_loader/src/shader.cpp
		${PROJECT_SOURCE_DIR}/3rdp/shader_loader/src/shader_program.cpp
		${PROJECT_SOURCE_DIR}/3rdp/randomColor-cpp/randomcolor.cpp
	)
	if(WIN32)
		target_link_libraries(demo PRIVATE csg SDL2::SDL2 SDL2::SDL2main GLEW::GLEW)
	elseif(UNIX)
		target_link_libraries(demo PRIVATE csg SDL2::SDL2 SDL2::SDL2main GLEW::GLEW GL)
	endif()
	target_include_directories(demo PRIVATE ${PROJECT_SOURCE_DIR}/3rdp/flythrough_camera)
	target_include_directories(demo PRIVATE ${PROJECT_SOURCE_DIR}/3rdp/shader_loader/include)
	target_include_directories(demo PRIVATE ${PROJECT_SOURCE_DIR}/3rdp/randomColor-cpp)
	target_include_directories(demo PRIVATE ${PROJECT_SOURCE_DIR}/3rdp/defer)
	target_compile_options(demo PRIVATE -Wall -Wextra -Wpedantic)
endif()
