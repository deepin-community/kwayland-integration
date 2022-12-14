project(kwayland-integration)

set(CMAKE_C_STANDARD 99)

set(PROJECT_VERSION "5.24.3")
set(PROJECT_VERSION_MAJOR 5)

cmake_minimum_required(VERSION 3.16)
set(QT_MIN_VERSION "5.15.0")
set(KF5_MIN_VERSION "5.86")
set(KDE_COMPILERSETTINGS_LEVEL "5.82")

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(ECM ${KF5_MIN_VERSION} REQUIRED NO_MODULE)

include(FeatureSummary)

# where to look first for cmake modules, before ${CMAKE_ROOT}/Modules/ is checked
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${ECM_MODULE_PATH} )


find_package(Qt5 ${QT_MIN_VERSION} CONFIG REQUIRED COMPONENTS
    Core
    Widgets
    WaylandClient
    XkbCommonSupport # Needed by Qt5::WaylandClientPrivate
)

include(KDEInstallDirs)
include(KDECMakeSettings)
include(KDECompilerSettings NO_POLICY_SCOPE)
include(ECMQtDeclareLoggingCategory)
include(KDEClangFormat)
include(KDEGitCommitHooks)

# required frameworks
find_package(KF5 ${KF5_MIN_VERSION} REQUIRED COMPONENTS
    IdleTime
    WindowSystem
    GuiAddons
    Wayland
)

find_package(QtWaylandScanner REQUIRED)
find_package(WaylandProtocols 1.21 REQUIRED)
find_package(Wayland 1.15 COMPONENTS Client)
find_package(PkgConfig REQUIRED)

#add_definitions(-DQT_DISABLE_DEPRECATED_BEFORE=0x060000)

add_subdirectory(src)
if(BUILD_TESTING)
   add_subdirectory(autotests)
endif()

# add clang-format target for all our real source files
file(GLOB_RECURSE ALL_CLANG_FORMAT_SOURCE_FILES *.cpp *.h)
kde_clang_format(${ALL_CLANG_FORMAT_SOURCE_FILES})
kde_configure_git_pre_commit_hook(CHECKS CLANG_FORMAT)

feature_summary(WHAT ALL INCLUDE_QUIET_PACKAGES FATAL_ON_MISSING_REQUIRED_PACKAGES)
