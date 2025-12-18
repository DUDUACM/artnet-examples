# Source files for each program
set(ARTNET_DISCOVER_SOURCES artnet-discover.c)
set(ARTNET_FLOOD_RX_SOURCES artnet-flood-rx.c)
set(ARTNET_FLOOD_TX_SOURCES artnet-flood-tx.c)
set(ARTNET_PROFILE_TX_SOURCES artnet-profile-tx.c)
set(ARTNET_PROFILE_RX_SOURCES artnet-profile-rx.c)
set(ARTNET_FIRMWARE_NODE_SOURCES artnet-firmware-node.c)
set(ARTNET_FIRMWARE_SERVER_SOURCES artnet-firmware-server.c)
set(ARTNET_MULTIPORT_SOURCES artnet-multiport.c)
set(ARTNET_RDM_OUTPUT_SOURCES artnet-rdm-output.c)
set(ARTNET_SETDMX_SOURCES artnet-setdmx.c)
set(ARTNET_USB_SOURCES artnet-usb.c)
set(ARTNET_DMXCONSOLE_SOURCES artnet-dmxconsole.c)
set(ARTNET_DMXMONITOR_SOURCES artnet-dmxmonitor.c)

# Common programs (always built)
set(COMMON_PROGRAMS
    artnet_discover
    artnet_flood_rx
    artnet_flood_tx
    artnet_profile_tx
    artnet_profile_rx
    artnet_firmware_node
    artnet_firmware_server
    artnet_multiport
    artnet_rdm_output
    artnet_setdmx
)

# Build common programs
foreach(PROGRAM ${COMMON_PROGRAMS})
    string(TOUPPER ${PROGRAM} PROGRAM_UPPER)
    add_executable(${PROGRAM} ${${PROGRAM_UPPER}_SOURCES})
    
    # Link libartnet (support both target and library path)
    if(TARGET ${LIBARTNET_LIBRARIES})
        target_link_libraries(${PROGRAM} PRIVATE ${LIBARTNET_LIBRARIES})
    else()
        target_link_libraries(${PROGRAM} PRIVATE ${LIBARTNET_LIBRARIES})
        target_include_directories(${PROGRAM} PRIVATE ${LIBARTNET_INCLUDE_DIRS})
    endif()
    
    # Link Windows socket library
    if(WIN32)
        target_link_libraries(${PROGRAM} PRIVATE ws2_32 winmm)
    endif()
    
    install(TARGETS ${PROGRAM} RUNTIME DESTINATION bin)
endforeach()

# Build ncurses programs if available
if(HAVE_NCURSES)
    # artnet_dmxconsole
    add_executable(artnet_dmxconsole ${ARTNET_DMXCONSOLE_SOURCES})
    if(TARGET ${LIBARTNET_LIBRARIES})
        target_link_libraries(artnet_dmxconsole PRIVATE ${LIBARTNET_LIBRARIES} ${CURSES_LIBRARIES})
    else()
        target_link_libraries(artnet_dmxconsole PRIVATE ${LIBARTNET_LIBRARIES} ${CURSES_LIBRARIES})
        target_include_directories(artnet_dmxconsole PRIVATE ${LIBARTNET_INCLUDE_DIRS} ${CURSES_INCLUDE_DIRS})
    endif()
    if(WIN32)
        target_link_libraries(artnet_dmxconsole PRIVATE ws2_32 winmm)
    endif()
    install(TARGETS artnet_dmxconsole RUNTIME DESTINATION bin)
    
    # artnet_dmxmonitor
    add_executable(artnet_dmxmonitor ${ARTNET_DMXMONITOR_SOURCES})
    if(TARGET ${LIBARTNET_LIBRARIES})
        target_link_libraries(artnet_dmxmonitor PRIVATE ${LIBARTNET_LIBRARIES} ${CURSES_LIBRARIES})
    else()
        target_link_libraries(artnet_dmxmonitor PRIVATE ${LIBARTNET_LIBRARIES} ${CURSES_LIBRARIES})
        target_include_directories(artnet_dmxmonitor PRIVATE ${LIBARTNET_INCLUDE_DIRS} ${CURSES_INCLUDE_DIRS})
    endif()
    if(WIN32)
        target_link_libraries(artnet_dmxmonitor PRIVATE ws2_32 winmm)
    endif()
    install(TARGETS artnet_dmxmonitor RUNTIME DESTINATION bin)
endif()

# Build artnet_usb on Linux only
if(OS_LINUX)
    add_executable(artnet_usb ${ARTNET_USB_SOURCES})
    if(TARGET ${LIBARTNET_LIBRARIES})
        target_link_libraries(artnet_usb PRIVATE ${LIBARTNET_LIBRARIES} Threads::Threads)
    else()
        target_link_libraries(artnet_usb PRIVATE ${LIBARTNET_LIBRARIES} Threads::Threads)
        target_include_directories(artnet_usb PRIVATE ${LIBARTNET_INCLUDE_DIRS})
    endif()
    install(TARGETS artnet_usb RUNTIME DESTINATION bin)
endif()
