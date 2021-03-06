if(NOT USE_MODULES)
    add_library(defaultmodule STATIC
        "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../src/libout123/modules/$<$<STREQUAL:${DEFAULT_OUTPUT_MODULE},dummy>:dummy.c>"
        "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../src/libout123/modules/$<$<STREQUAL:${DEFAULT_OUTPUT_MODULE},coreaudio>:coreaudio.c>"
        "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../src/libout123/modules/$<$<STREQUAL:${DEFAULT_OUTPUT_MODULE},alsa>:alsa.c>"
        "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../src/libout123/modules/$<$<STREQUAL:${DEFAULT_OUTPUT_MODULE},pulse>:pulse.c>"
        "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../src/libout123/modules/$<$<STREQUAL:${DEFAULT_OUTPUT_MODULE},win32>:win32.c>")
    target_link_libraries(defaultmodule PUBLIC
        $<$<STREQUAL:${DEFAULT_OUTPUT_MODULE},alsa>:ALSA::ALSA>
        $<$<STREQUAL:${DEFAULT_OUTPUT_MODULE},coreaudio>:${AUDIO_TOOLBOX}>
        $<$<STREQUAL:${DEFAULT_OUTPUT_MODULE},pulse>:${PULSE_LIBRARIES}>)
    if(${DEFAULT_OUTPUT_MODULE} STREQUAL "pulse")
        target_compile_definitions(defaultmodule PRIVATE ${PULSE_CFLAGS})
    endif()
else()
    set(CMAKE_SHARED_MODULE_PREFIX "")
    list(FIND OUTPUT_MODULES coreaudio INDEX)
    if(NOT INDEX EQUAL -1)
        add_library(output_coreaudio MODULE "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../src/libout123/modules/coreaudio.c")
        target_link_libraries(output_coreaudio PRIVATE ${AUDIO_TOOLBOX})
        install(TARGETS output_coreaudio
            ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}"
            LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}"
            RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}/${PROJECT_NAME}")
    endif()
    list(FIND OUTPUT_MODULES alsa INDEX)
    if(NOT INDEX EQUAL -1)
        add_library(output_alsa MODULE "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../src/libout123/modules/alsa.c")
        target_link_libraries(output_alsa PRIVATE ALSA::ALSA)
        install(TARGETS output_alsa
            ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}"
            LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}"
            RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}/${PROJECT_NAME}")
    endif()
    list(FIND OUTPUT_MODULES pulse INDEX)
    if(NOT INDEX EQUAL -1)
        add_library(output_pulse MODULE "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../src/libout123/modules/pulse.c")
        target_compile_definitions(output_pulse PRIVATE ${PULSE_CFLAGS})
        target_link_libraries(output_pulse PRIVATE ${PULSE_LIBRARIES})
        install(TARGETS output_pulse
            ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}"
            LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}"
            RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}/${PROJECT_NAME}")
    endif()
    list(FIND OUTPUT_MODULES win32 INDEX)
    if(NOT INDEX EQUAL -1)
        add_library(output_win32 MODULE "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../src/libout123/modules/win32.c")
        install(TARGETS output_win32
            ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}"
            LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}"
            RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}/${PROJECT_NAME}")
    endif()
endif()
