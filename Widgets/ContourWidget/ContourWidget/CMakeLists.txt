# CMakeList.txt: ContourWidget 的 CMake 项目，在此处包括源代码并定义
# 项目特定的逻辑。
#
cmake_minimum_required (VERSION 3.8)

project(ContourWidget)

find_package(VTK COMPONENTS 
  vtkCommonColor
  vtkCommonCore
  vtkCommonDataModel
  vtkInteractionStyle
  vtkInteractionWidgets
  vtkRenderingContextOpenGL2
  vtkRenderingCore
  vtkRenderingFreeType
  vtkRenderingGL2PSOpenGL2
  vtkRenderingOpenGL2
  QUIET
)

if (NOT VTK_FOUND)
  message("Skipping ContourWidget: ${VTK_NOT_FOUND_MESSAGE}")
  return()
endif()
message (STATUS "VTK_VERSION: ${VTK_VERSION}")
if (VTK_VERSION VERSION_LESS "8.90.0")
  # old system
  include(${VTK_USE_FILE})
  add_executable (ContourWidget "ContourWidget.cpp" "ContourWidget.h")
  target_link_libraries(ContourWidget PRIVATE ${VTK_LIBRARIES})
else()
  # Prevent a "command line is too long" failure in Windows.
  set(CMAKE_NINJA_FORCE_RESPONSE_FILE "ON" CACHE BOOL "Force Ninja to use response files.")
  add_executable (ContourWidget "ContourWidget.cpp" "ContourWidget.h")
  target_link_libraries(ContourWidget PRIVATE ${VTK_LIBRARIES})
  # vtk_module_autoinit is needed
  vtk_module_autoinit(
    TARGETS ContourWidget
    MODULES ${VTK_LIBRARIES}
    )
endif()
##
## Miscellaneous Microsoft Visual Studio settings
##
if (MSVC)
	 # Set the startup project .
	if (NOT ${CMAKE_VERSION} VERSION_LESS "3.6.0")
		  set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT ${PROJECT_NAME})
	endif ()
endif (MSVC)

# TODO: 如有需要，请添加测试并安装目标。
