# Dear ImGui CMake file

# ------------------- Project properties -------------------
cmake_minimum_required(VERSION 3.16 FATAL_ERROR)
project(imgui VERSION 1.93.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# ------------------------- Options ------------------------
option(IMGUI_BUILD_DEMO       "Include imgui_demo.cpp"                ON)
option(IMGUI_USE_FREETYPE     "Use freetype font rendering"           OFF)
option(IMGUI_BACKEND_GLFW     "Build the GLFW platform backend"       OFF)
option(IMGUI_BACKEND_WIN32    "Build the Win32 platform backend"      OFF)
option(IMGUI_BACKEND_OPENGL3  "Build the OpenGL3 renderer backend"    OFF)
option(IMGUI_BACKEND_VULKAN   "Build the Vulkan renderer backend"     OFF)
option(IMGUI_BACKEND_DX11     "Build the DX11 renderer backend"       OFF)
option(IMGUI_BACKEND_DX12     "Build the DX12 renderer backend"       OFF)

# --------------------- Configurations ---------------------
set(CMAKE_CONFIGURATION_TYPES "Debug;Release" CACHE STRING "Configurations" FORCE)

# ------------------------- Sources ------------------------
set(IMGUI_SOURCES
    imgui.cpp
    imgui_draw.cpp
    imgui_tables.cpp
    imgui_widgets.cpp
    imconfig.h
    imgui.h
    imgui_internal.h
    imstb_rectpack.h
    imstb_textedit.h
    imstb_truetype.h
)

if (IMGUI_BUILD_DEMO)
    list(APPEND IMGUI_SOURCES imgui_demo.cpp)
endif()

# ------------------------- Target -------------------------
add_library(imgui STATIC ${IMGUI_SOURCES})

target_include_directories(imgui
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR}/backends
)

set_target_properties(imgui
    PROPERTIES
        POSITION_INDEPENDENT_CODE ON
        DEBUG_POSTFIX d
)

# ------------------------- Vendor -------------------------
if (IMGUI_USE_FREETYPE)
    target_sources(imgui
        PRIVATE
            misc/freetype/imgui_freetype.cpp
            misc/freetype/imgui_freetype.h
    )

    target_compile_definitions(imgui PUBLIC IMGUI_ENABLE_FREETYPE)
    target_link_libraries(imgui PUBLIC freetype)
endif()

# ------------------------ Backends ------------------------
if (IMGUI_BACKEND_GLFW)
    target_sources(imgui PRIVATE backends/imgui_impl_glfw.cpp)
    target_link_libraries(imgui PUBLIC glfw)
endif()

if (IMGUI_BACKEND_WIN32)
    target_sources(imgui PRIVATE backends/imgui_impl_win32.cpp)
    target_link_libraries(imgui PUBLIC dwmapi)
endif()

if (IMGUI_BACKEND_OPENGL3)
    find_package(OpenGL REQUIRED)
    target_sources(imgui PRIVATE backends/imgui_impl_opengl3.cpp)
    target_link_libraries(imgui PUBLIC OpenGL::GL)
endif()

if (IMGUI_BACKEND_VULKAN)
    find_package(Vulkan REQUIRED)
    target_sources(imgui PRIVATE backends/imgui_impl_vulkan.cpp)
    target_link_libraries(imgui PUBLIC Vulkan::Vulkan)
endif()

if (IMGUI_BACKEND_DX11)
    target_sources(imgui PRIVATE backends/imgui_impl_dx11.cpp)
    target_link_libraries(imgui PUBLIC d3d11 dxgi d3dcompiler)
endif()

if (IMGUI_BACKEND_DX12)
    target_sources(imgui PRIVATE backends/imgui_impl_dx12.cpp)
    target_link_libraries(imgui PUBLIC d3d12 dxgi d3dcompiler)
endif()
