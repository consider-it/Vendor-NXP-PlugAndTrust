PROJECT(smCom)

FILE(GLOB smCom_platform_sources
    ../platform/linux/i2c_a7.c
    ../platform/imx/se05x_reset.c
)

IF(WithSMCOM_T1oI2C)
    FILE(GLOB smCom_T1oi2c_sources
        smCom/T1oI2C/*.c
        smCom/smComT1oI2C*.h
        smCom/smComT1oI2C*.c
    )
ENDIF()

IF(WithSMCOM_T1oI2C_GP1_0)
    FILE(GLOB smCom_T1oI2C_GP1_0_sources
        smCom/T1oI2C/*.c
        smCom/smComT1oI2C*.h
        smCom/smComT1oI2C*.c
    )
ENDIF()

SET(smCom_timer_sources
    ../platform/generic/sm_timer.c
    ../tstUtil/tst_sm_time.c
)

FILE(GLOB smCom_sources
    infra/sm_apdu.*
    infra/sm_errors.*
    infra/sm_printf.c
    smCom/smCom.*
    ${smCom_timer_sources}
    ${smCom_T1oi2c_sources}
    ${smCom_T1oI2C_GP1_0_sources}
    ${smCom_platform_sources}
)

IF(WithSCP_SCP03_SSS)
    FILE(GLOB nxScp_files
        nxScp/*.c
        ../inc/nxScp03_*.h
    )
ENDIF()


ADD_LIBRARY(${PROJECT_NAME}
    ${smCom_sources}
    ${nxScp_files}
)

TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME}
    PUBLIC smCom
    PUBLIC infra
    PUBLIC ../inc
    PUBLIC ../platform/inc
    PRIVATE ${PLUGTRUST_SOURCE_DIR}/sss/ex/inc
    PRIVATE ${PLUGTRUST_SOURCE_DIR} # for fsl_sss_ftr.h
    PRIVATE ${PLUGTRUST_SOURCE_DIR}/sss/port/default # for fsl_sss_types.h
)

# TARGET_COMPILE_DEFINITIONS(${PROJECT_NAME} PRIVATE FLOW_VERBOSE)

IF(WithSCP_SCP03_SSS)
    INCLUDE_DIRECTORIES(${PLUGTRUST_SOURCE_DIR}/sss/inc)
ELSE()
    TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME} PRIVATE scp)
ENDIF()

IF(WithSMCOM_T1oI2C)
    TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME} PUBLIC smCom/T1oI2C)
ENDIF()

IF(WithSMCOM_T1oI2C_GP1_0)
    TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME} PUBLIC smCom/T1oI2C)
ENDIF()

TARGET_LINK_LIBRARIES(${PROJECT_NAME} mwlog)

IF(WithSMCOM_T1oI2C)
    TARGET_COMPILE_DEFINITIONS(${PROJECT_NAME} PRIVATE T1oI2C_UM11225)
ENDIF()

IF(WithSMCOM_T1oI2C_GP1_0)
    TARGET_COMPILE_DEFINITIONS(${PROJECT_NAME} PRIVATE T1oI2C_GP1_0)
ENDIF()

IF(WithHostCrypto_MBEDTLS)
    TARGET_LINK_LIBRARIES(${PROJECT_NAME} mbedtls)
ENDIF()

IF(WithHost_iMXLinux)
    TARGET_COMPILE_DEFINITIONS(${PROJECT_NAME} PUBLIC PLATFORM_IMX)
ENDIF()

IF(WithHost_Raspbian)
    TARGET_LINK_LIBRARIES(${PROJECT_NAME} pthread)
ENDIF()

IF(CMAKE_COMPILER_IS_GNUCC)
    TARGET_LINK_LIBRARIES(${PROJECT_NAME} -lpthread)
ENDIF()

IF(WithHost_PCLinux)
    TARGET_LINK_LIBRARIES(${PROJECT_NAME} -lrt)
ENDIF()
