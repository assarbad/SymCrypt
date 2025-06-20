set(SOURCES
    module.c
    rng.c)

include_directories(${SYMCRYPT_SOURCE_DIR}/inc)

add_library(symcrypt_module_posix_common STATIC ${SOURCES})

if(SYMCRYPT_FIPS_BUILD)
  set(jitter_cflags "${CMAKE_C_FLAGS} -fwrapv -fvisibility=hidden -DCONFIG_CRYPTO_CPU_JITTERENTROPY_SECURE_MEMORY")
  set(jitter_ldflags "")

  if(SYMCRYPT_TARGET_ARCH MATCHES "X86")
    set(jitter_cflags "${jitter_cflags} -m32")
    set(jitter_ldflags "-m32")
  endif()

  if (CMAKE_C_COMPILER_ID MATCHES "Clang" AND DEFINED CMAKE_C_COMPILER_TARGET)
    set(jitter_cflags "--target=${CMAKE_C_COMPILER_TARGET} --sysroot=${CMAKE_SYSROOT_COMPILE} ${jitter_cflags}")
    set(jitter_ldflags "--target=${CMAKE_C_COMPILER_TARGET}")
  endif()

  if(NOT IS_DIRECTORY "${SYMCRYPT_SOURCE_DIR}/3rdparty/jitterentropy-library" OR NOT EXISTS "${SYMCRYPT_SOURCE_DIR}/3rdparty/jitterentropy-library/Makefile")
    file(MAKE_DIRECTORY ${SYMCRYPT_SOURCE_DIR}/3rdparty)
    execute_process(
      COMMAND git submodule update --init -- 3rdparty/jitterentropy-library
      WORKING_DIRECTORY ${SYMCRYPT_SOURCE_DIR})
  endif()

  add_custom_target(jitterentropy_lib ALL
    COMMAND make clean
    COMMAND ${CMAKE_COMMAND} -E
      env "CC=${CMAKE_C_COMPILER}"
      env "CFLAGS=${jitter_cflags}"
      env "LDFLAGS=${jitter_ldflags}"
      make
    WORKING_DIRECTORY ${SYMCRYPT_SOURCE_DIR}/3rdparty/jitterentropy-library
  )
endif()