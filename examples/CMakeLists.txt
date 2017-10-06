#
#
#
set(EXAMPLES
    lescan.cc
    blelogger.cc
    bluetooth.cc
    lescan_simple.cc
    temperature.cc)

foreach (example_src ${EXAMPLES})
    get_filename_component(example_name ${example_src} NAME_WE)

    add_executable(${example_name} ${example_src})
    target_link_libraries(${example_name} ${PROJECT_NAME} ${BLUEZ_LIBRARIES})

 
    install(TARGETS ${example_name}
        # IMPORTANT: Add the ${example_name} executable to the "export-set"
        EXPORT LibBleppTargets
        RUNTIME DESTINATION "${INSTALL_BIN_DIR}" COMPONENT bin)
endforeach()

