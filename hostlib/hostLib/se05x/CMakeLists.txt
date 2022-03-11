PROJECT(se05x)

SET(Applet_Ver_dir "../se05x_03_xx_xx")

FILE(GLOB sources
    src/*.c
    src/*.h
    ../inc/Applet_SE050_Ver.h
    ${Applet_Ver_dir}/*.py
    ${Applet_Ver_dir}/*.c
    ${Applet_Ver_dir}/*.h
    ../inc/se05x*.h
)


ADD_LIBRARY(${PROJECT_NAME}
    ${sources}
)

TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME}
    PUBLIC ${Applet_Ver_dir}
    PUBLIC ${PLUGTRUST_SOURCE_DIR}/sss/ex/inc
    PUBLIC ${PLUGTRUST_SOURCE_DIR}/sss/inc
    PRIVATE ${PLUGTRUST_SOURCE_DIR} # for fsl_sss_ftr.h
    PRIVATE ${PLUGTRUST_SOURCE_DIR}/sss/port/default # for fsl_sss_types.h
)


TARGET_LINK_LIBRARIES(${PROJECT_NAME} smCom)

IF(WithHostCrypto_MBEDTLS)
    TARGET_LINK_LIBRARIES(${PROJECT_NAME} mbedtls)
ENDIF()
