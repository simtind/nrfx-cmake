cmake_minimum_required(VERSION 3.6)

# Get CPM
file(
  DOWNLOAD
  https://github.com/cpm-cmake/CPM.cmake/releases/download/v0.38.3/CPM.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/cmake/CPM.cmake
  EXPECTED_HASH SHA256=cc155ce02e7945e7b8967ddfaff0b050e958a723ef7aad3766d368940cb15494
)
include(${CMAKE_CURRENT_BINARY_DIR}/cmake/CPM.cmake)

if (NOT DEFINED NRFX_VERSION)
    set(NRFX_VERSION v3.3.0)
endif()

if (NOT DEFINED NRF_MDK_VERSION)
    set(NRF_MDK_VERSION main)
endif()

CPMFindPackage(
    NAME nrf-mdk
    GITHUB_REPOSITORY simtind/nrf-mdk
    GIT_TAG ${NRF_MDK_VERSION})

CPMFindPackage(
    NAME nrfx
    GITHUB_REPOSITORY NordicSemiconductor/nrfx
    GIT_TAG ${NRFX_VERSION}
    DOWNLOAD_ONLY True)

project(nrfx-cmake C CXX ASM)
 
add_library(nrfx_hal INTERFACE)
target_include_directories(nrfx_hal 
    INTERFACE
        ${nrfx_SOURCE_DIR}
        ${nrfx_SOURCE_DIR}/templates
        ${nrfx_SOURCE_DIR}/hal
        ${nrfx_SOURCE_DIR}/haly)
target_link_libraries(nrfx_hal INTERFACE nrf-mdk::definitions)

add_library(nrfx_soc STATIC)
target_include_directories(nrfx_soc 
    PUBLIC
        ${nrfx_SOURCE_DIR}/soc)
target_sources(nrfx_soc 
    PUBLIC
        ${nrfx_SOURCE_DIR}/soc/nrfx_atomic.c
        ${nrfx_SOURCE_DIR}/soc/interconnect/apb/nrfx_interconnect_apb.c
        ${nrfx_SOURCE_DIR}/soc/interconnect/dppic_ppib/nrfx_interconnect_dppic_ppib.c
        ${nrfx_SOURCE_DIR}/soc/interconnect/ipct/nrfx_interconnect_ipct.c)
target_link_libraries(nrfx_soc nrfx_hal)

add_library(nrfx_helpers STATIC)
target_include_directories(nrfx_helpers 
    PUBLIC
        ${nrfx_SOURCE_DIR}/helpers)
target_sources(nrfx_helpers 
    PUBLIC
        ${nrfx_SOURCE_DIR}/helpers/nrfx_flag32_allocator.c
        ${nrfx_SOURCE_DIR}/helpers/nrfx_gppi_dppi.c
        ${nrfx_SOURCE_DIR}/helpers/nrfx_gppi_dppi_ppib.c
        ${nrfx_SOURCE_DIR}/helpers/nrfx_gppi_dppi_ppib_lumos.c
        ${nrfx_SOURCE_DIR}/helpers/nrfx_gppi_ppi.c
        ${nrfx_SOURCE_DIR}/helpers/nrfx_ram_ctrl.c)
target_link_libraries(nrfx_helpers nrfx_hal)

add_library(nrfx_drivers STATIC)
target_include_directories(nrfx_drivers
    PUBLIC
        ${nrfx_SOURCE_DIR}/drivers 
        ${nrfx_SOURCE_DIR}/drivers/include)
target_sources(nrfx_drivers 
    PUBLIC
        ${nrfx_SOURCE_DIR}/drivers/src/prs/nrfx_prs.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_adc.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_bellboard.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_clock.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_comp.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_dppi.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_egu.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_gpiote.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_grtc.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_i2s.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_ipc.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_lpcomp.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_nfct.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_nvmc.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_pdm.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_power.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_ppi.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_pwm.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_qdec.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_qspi.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_rng.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_rramc.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_rtc.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_saadc.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_spi.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_spim.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_spis.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_systick.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_tbm.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_temp.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_timer.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_twi.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_twi_twim.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_twim.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_twis.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_uart.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_uarte.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_usbd.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_usbd_errata.h
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_usbreg.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_vevif.c
        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_wdt.c)
target_link_libraries(nrfx_drivers nrfx_hal)

add_library(nrfx::hal ALIAS nrfx_hal)
add_library(nrfx::soc ALIAS nrfx_soc)
add_library(nrfx::drivers ALIAS nrfx_drivers)
add_library(nrfx::helpers ALIAS nrfx_helpers)
