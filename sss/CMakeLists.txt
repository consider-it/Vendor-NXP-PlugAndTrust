PROJECT(SSS_APIs LANGUAGES C)

FILE(GLOB api_files
    inc/*.h
    inc/*.h.in
    inc/*.hpp
    src/*.c
    src/sscp/fsl_sss_sscp.c
    src/se05x/*.c
    src/mbedtls/*.c
    src/mbedtls/*.cpp
    src/openssl/*.c
    src/keystore/*.c
    port/default/*.h
    ${PLUGTRUST_SOURCE_DIR}/fsl_sss_ftr.h
)


ADD_LIBRARY(${PROJECT_NAME}
    ${api_files}
)

TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME}
    PUBLIC inc
    PUBLIC port/default
    PUBLIC ${PLUGTRUST_SOURCE_DIR} # for fsl_sss_ftr.h
)


IF(WithHostCrypto_MBEDTLS)
    TARGET_LINK_LIBRARIES(${PROJECT_NAME} mbedtls)
ENDIF()

TARGET_LINK_LIBRARIES(${PROJECT_NAME}
    se05x
    a7x_utils
    smCom
)
