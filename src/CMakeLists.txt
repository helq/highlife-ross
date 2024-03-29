# This is to allow tests to include header files without ugly "../../.."
set(HIGHLIFE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}" PARENT_SCOPE)

# Setting up ROSS and additional utilities (Git stuff is used in
# configuration header file, below)
include_directories(
  "${ROSS_SOURCE_DIR}"
  "${ROSS_BINARY_DIR}"
)
# Uncomment in case of using RIO
#include_directories(${RIO_SOURCE_DIR} ${RIO_BINARY_DIR})
list(APPEND CMAKE_MODULE_PATH "${ROSS_SOURCE_DIR}/cmake/")
include(GetGitRevisionDescription)
get_git_head_revision(GIT_RESPEC_MODEL GIT_SHA1_MODEL)

# Building configuration header file
configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/highlife_config.h.in
  ${CMAKE_CURRENT_BINARY_DIR}/highlife_config.h
  @ONLY
)
set_source_files_properties(
  ${CMAKE_CURRENT_BINARY_DIR}/highlife_config.h
  PROPERTIES GENERATED TRUE
)
include_directories(${CMAKE_CURRENT_BINARY_DIR})

add_library(highlife_lib
  driver.c
  mapping.c
  utils.c
)

# Compiling ROSS highlife model
add_executable(highlife highlife.main.c)
target_link_libraries(highlife
  PRIVATE
    highlife_lib
    m
    ROSS
#   RIO # uncomment if using RIO
)

# Including variables that indicate where the usr/bin is.
# It defines ${CMAKE_INSTALL_FULL_BINDIR}
include(GNUInstallDirs)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/highlife
  DESTINATION ${CMAKE_INSTALL_FULL_BINDIR}
  PERMISSIONS
    OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE
    WORLD_READ WORLD_EXECUTE
)
