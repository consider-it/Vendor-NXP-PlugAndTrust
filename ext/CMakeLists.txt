PROJECT(mbedtls)

FILE(GLOB mbedtls_sources
    mbedtls.cmake
    mbedtls/library/*.c
    mbedtls/library/*.h
    mbedtls/include/mbedtls/*.h
)

IF(WithmbedTLS_ALT_SSS)
    FILE(GLOB mbedtls_alt
        ${PLUGTRUST_SOURCE_DIR}/sss/plugin/mbedtls/ecdh_alt_ax.c
        ${PLUGTRUST_SOURCE_DIR}/sss/plugin/mbedtls/ecp_alt.c
        ${PLUGTRUST_SOURCE_DIR}/sss/plugin/mbedtls/sss_mbedtls.c
        ${PLUGTRUST_SOURCE_DIR}/sss/plugin/mbedtls/sss_mbedtls_rsa.c
        ${PLUGTRUST_SOURCE_DIR}/sss/plugin/mbedtls/ecdsa_verify_alt.c
        ${PLUGTRUST_SOURCE_DIR}/hostlib/hostLib/mbedtls/src/*_alt.c
        ${PLUGTRUST_SOURCE_DIR}/sss/plugin/mbedtls/port/ksdk/ecp_curves_alt.c
        ${PLUGTRUST_SOURCE_DIR}/sss/plugin/mbedtls/port/ksdk/ecp_alt.c
    )
ENDIF()


ADD_LIBRARY(${PROJECT_NAME}
    ${mbedtls_sources}
    ${mbedtls_alt}
)

TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME}
    PUBLIC mbedtls/include
    PRIVATE ${PLUGTRUST_SOURCE_DIR} # for fsl_sss_ftr.h
    PRIVATE ${PLUGTRUST_SOURCE_DIR}/sss/port/default # for fsl_sss_types.h
)


IF(WithmbedTLS_ALT_SSS)
    TARGET_COMPILE_DEFINITIONS(${PROJECT_NAME} PUBLIC MBEDTLS_CONFIG_FILE=\"sss_mbedtls_x86_config.h\")

    TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME}
        PUBLIC
        ${PLUGTRUST_SOURCE_DIR}/sss/plugin/mbedtls
        ${PLUGTRUST_SOURCE_DIR}/sss/inc
        ${PLUGTRUST_SOURCE_DIR}/hostlib/hostLib/libCommon/infra
        ${PLUGTRUST_SOURCE_DIR}/hostlib/hostLib/inc
    )

    TARGET_LINK_LIBRARIES(${PROJECT_NAME} mwlog)
ENDIF()

IF(
    "${CMAKE_C_COMPILER}" MATCHES ".*clang"
    OR "${CMAKE_CXX_COMPILER_ID}" STREQUAL "AppleClang"
)
    TARGET_COMPILE_OPTIONS(${PROJECT_NAME}
        PRIVATE -Wno-unused-function
        PRIVATE -Wno-error=pointer-sign
        PRIVATE -Wno-error=format
        PRIVATE -Wno-format
        PRIVATE -Wno-error=unused-const-variable
        PRIVATE -Wno-unused-const-variable
    )
ENDIF()

IF("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    TARGET_COMPILE_OPTIONS(${PROJECT_NAME}
        PRIVATE -Wno-unused-function
        PRIVATE -Wno-error=pointer-sign
        PRIVATE -Wno-error=format
        PRIVATE -Wno-format
    )

    SET(GCC_VERSION_WITH_UNUSED_CONST 6.3.0)
    IF(GCC_VERSION_WITH_UNUSED_CONST VERSION_LESS CMAKE_CXX_COMPILER_VERSION)
        TARGET_COMPILE_OPTIONS(${PROJECT_NAME}
            PRIVATE -Wno-implicit-function-declaration
            PRIVATE -Wno-error=unused-const-variable
            PRIVATE -Wno-unused-const-variable
        )
    ENDIF()
ENDIF()
