PROJECT(a7x_utils)

ADD_SUBDIRECTORY(se05x)
ADD_SUBDIRECTORY(libCommon)

FILE(GLOB a7x_utils_files
    libCommon/infra/sm_app_boot.c
    libCommon/infra/app_boot_nfc.c
    libCommon/infra/sm_connect.*
    libCommon/infra/global_platf.*
    libCommon/infra/cm_commands.*
    libCommon/scp/scp.*
)


ADD_LIBRARY(${PROJECT_NAME}
    ${a7x_utils_files}
)

TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME}
    PUBLIC ${PLUGTRUST_SOURCE_DIR}/sss/inc
    PRIVATE ${PLUGTRUST_SOURCE_DIR} # for fsl_sss_ftr.h
)


TARGET_LINK_LIBRARIES(${PROJECT_NAME} smCom)

IF(WithHostCrypto_MBEDTLS)
    TARGET_LINK_LIBRARIES(${PROJECT_NAME} mbedtls)
    TARGET_COMPILE_DEFINITIONS(${PROJECT_NAME} PUBLIC MBEDTLS)
ENDIF()
