set(SOURCES
    ../common/integrity.c
    statusindicator.c
    rng.c
    ../common/optional/module_posixUserMode.c
    ../common/callbacks_pthread.c)

include_directories(${SYMCRYPT_SOURCE_DIR}/inc ../common)

add_library(symcrypt_oe SHARED ${SOURCES})

set(TARGET_NAME symcrypt_oe)
set(OUTPUT_DIR ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/oe_full)

include(../common/ModuleCommon.cmake)