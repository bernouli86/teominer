cmake_policy(SET CMP0015 NEW)

aux_source_directory(. SRC_LIST)

include_directories(BEFORE ..)
include_directories(PRIVATE ../libethash/include)

set(EXECUTABLE teominer)

file(GLOB HEADERS "*.h")

add_executable(${EXECUTABLE} ${SRC_LIST} ${HEADERS})

hunter_add_package(CLI11)
find_package(CLI11 CONFIG REQUIRED)

target_link_libraries(teominer PRIVATE ethcore poolprotocols devcore ethash teominer-buildinfo CLI11::CLI11 Boost::system)

if(ETHDBUS)
	find_package(PkgConfig)
	set( ENV{PKG_CONFIG_PATH} "$ENV{PKG_CONFIG_PATH}:/usr/lib/x86_64-linux-gnu/pkgconfig" )
	pkg_check_modules(DBUS dbus-1)
	include_directories(${DBUS_INCLUDE_DIRS})
	link_directories(${DBUS_LIBRARY_DIRS})
	target_link_libraries(teominer PRIVATE ${DBUS_LIBRARIES})
endif()

if(APICORE)
	target_link_libraries(teominer PRIVATE apicore)
endif()

include(GNUInstallDirs)
install(TARGETS teominer DESTINATION ${CMAKE_INSTALL_BINDIR})
if(MSVC)
    install(FILES $<TARGET_PDB_FILE:teominer> DESTINATION ${CMAKE_INSTALL_BINDIR} OPTIONAL)
endif()
