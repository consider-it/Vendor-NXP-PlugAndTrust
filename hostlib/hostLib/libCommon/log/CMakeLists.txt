PROJECT(mwlog)

FILE(GLOB mwlog_sources
    *.h
    nxLog.c
)


ADD_LIBRARY(${PROJECT_NAME}
    ${mwlog_sources}
)

TARGET_INCLUDE_DIRECTORIES(${PROJECT_NAME}
    PUBLIC .
    PUBLIC ../../platform/inc
    PUBLIC ../infra
)
