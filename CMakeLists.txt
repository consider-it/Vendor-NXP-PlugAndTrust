CMAKE_MINIMUM_REQUIRED(VERSION 3.5.0)
PROJECT(PlugAndTrustMW VERSION 03.03.00)

SET(PLUGTRUST_SOURCE_DIR "${PROJECT_SOURCE_DIR}")


# CONFIGURATION (has to match the settings in fsl_sss_ftr.h)
ADD_DEFINITIONS(-DSSS_USE_FTR_FILE)

# Plug&Trust Host (only choose one)
SET(WithHost_PCLinux32 OFF)
SET(WithHost_PCLinux64 OFF)
SET(WithHost_iMXLinux ON)
SET(WithHost_Raspbian OFF)

# Plug&Trust HostCrypto
SET(WithHostCrypto_MBEDTLS ON)

# Plug&Trust SCP (only choose one)
SET(WithSCP_SCP03_SSS ON)
SET(WithSCP_SCP03_HostCrypto OFF)

# Plug&Trust SMCOM
SET(WithSMCOM_T1oI2C ON)
SET(WithSMCOM_T1oI2C_GP1_0 OFF)

# Plug&Trust mbedTLS_ALT
SET(WithmbedTLS_ALT_SSS ON)


## CALCULATED VARIABLES
IF(WithHost_PCLinux32 OR WithHost_PCLinux64)
    SET(WithHost_PCLinux ON)
ELSE()
    SET(WithHost_PCLinux OFF)
ENDIF()

IF(WithHost_PCLinux OR WithHost_Raspbian OR WithHost_iMXLinux)
    SET(WithHost_LinuxLike ON)
ELSE()
    SET(WithHost_LinuxLike OFF)
ENDIF()

IF(WithSCP_SCP03_HostCrypto)
    ADD_DEFINITIONS("-DUSE_HOSTCRYPTO_FOR_SCP03")
ENDIF()

IF(WithSMCOM_T1oI2C OR WithSMCOM_T1oI2C_GP1_0)
    ADD_DEFINITIONS(-DT1oI2C )
ENDIF()

IF(WithLog_Silent)
    message(STATUS "FLOW_SILENT is ON!!!")
    add_definitions(-DFLOW_SILENT)
ENDIF()

IF(WithLog_Verbose)
    message(STATUS "FLOW_VERBOSE is ON!!!")
    add_definitions(-DFLOW_VERBOSE)
ENDIF()


## FURTHER CMAKE SPECIFICATIONS
IF(CMAKE_COMPILER_IS_GNUCC)
    SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-unused-result ")
ENDIF()

IF(WithHost_PCLinux OR WithHost_iMXLinux)
    SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
ENDIF()

IF(WithHost_PCLinux32)
    SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -m32")
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -m32")
    LINK_DIRECTORIES("/usr/lib/i386-linux-gnu/")
ENDIF()

IF(WithHost_PCLinux64)
    INCLUDE_DIRECTORIES(/usr/include/x86_64-linux-gnu)
ENDIF()

IF(WithHost_iMXLinux OR WithHost_Raspbian)
    SET(CMAKE_INSTALL_LIBDIR lib)
    SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
ENDIF()


## ADD ACTUAL LIBRARIES
ADD_SUBDIRECTORY(ext)

ADD_SUBDIRECTORY(hostlib/hostLib/libCommon/log)

ADD_SUBDIRECTORY(hostlib)

ADD_SUBDIRECTORY(sss)
