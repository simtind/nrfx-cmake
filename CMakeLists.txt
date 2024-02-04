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
        ${NRFX_CONFIG_DIR}
        ${nrfx_SOURCE_DIR}
        ${nrfx_SOURCE_DIR}/templates
        ${nrfx_SOURCE_DIR}/hal
        ${nrfx_SOURCE_DIR}/haly)
target_link_libraries(nrfx_hal INTERFACE nrf-mdk::definitions)

add_library(nrfx_soc INTERFACE)
target_sources(nrfx_soc INTERFACE
    ${nrfx_SOURCE_DIR}/soc/nrfx_atomic.c
    ${nrfx_SOURCE_DIR}/soc/interconnect/apb/nrfx_interconnect_apb.c
    ${nrfx_SOURCE_DIR}/soc/interconnect/dppic_ppib/nrfx_interconnect_dppic_ppib.c
    ${nrfx_SOURCE_DIR}/soc/interconnect/ipct/nrfx_interconnect_ipct.c)
target_include_directories(nrfx_soc 
    INTERFACE
        ${nrfx_SOURCE_DIR}/soc)
target_link_libraries(nrfx_soc INTERFACE nrfx_hal)

function(nrfx_make_driver name)
    string(TOUPPER ${name} upper_name)
    add_library(nrfx_drivers_${name} INTERFACE)
    target_sources(nrfx_drivers_${name} INTERFACE ${ARGN})
    add_library(nrfx::drivers::${name} ALIAS nrfx_drivers_${name})
    target_link_libraries(nrfx_drivers_${name} INTERFACE nrfx_soc)
    target_compile_definitions(nrfx_drivers_${name} INTERFACE NRFX_${upper_name}_ENABLED=1)
    target_include_directories(nrfx_drivers_${name}
        INTERFACE
            ${nrfx_SOURCE_DIR}/drivers 
            ${nrfx_SOURCE_DIR}/drivers/include)
endfunction()

nrfx_make_driver(prs          ${nrfx_SOURCE_DIR}/drivers/src/prs/nrfx_prs.c)
nrfx_make_driver(adc          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_adc.c)
nrfx_make_driver(bellboard    ${nrfx_SOURCE_DIR}/drivers/src/nrfx_bellboard.c)
nrfx_make_driver(clock        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_clock.c)
nrfx_make_driver(comp         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_comp.c)
nrfx_make_driver(dppi         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_dppi.c)
nrfx_make_driver(egu          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_egu.c)
nrfx_make_driver(gpiote       ${nrfx_SOURCE_DIR}/drivers/src/nrfx_gpiote.c)
nrfx_make_driver(grtc         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_grtc.c)
nrfx_make_driver(i2s          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_i2s.c)
nrfx_make_driver(ipc          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_ipc.c)
nrfx_make_driver(lpcomp       ${nrfx_SOURCE_DIR}/drivers/src/nrfx_lpcomp.c)
nrfx_make_driver(nfct         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_nfct.c)
nrfx_make_driver(nvmc         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_nvmc.c)
nrfx_make_driver(pdm          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_pdm.c)
nrfx_make_driver(power        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_power.c)
nrfx_make_driver(ppi          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_ppi.c)
nrfx_make_driver(pwm          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_pwm.c)
nrfx_make_driver(qdec         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_qdec.c)
nrfx_make_driver(qspi         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_qspi.c)
nrfx_make_driver(rng          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_rng.c)
nrfx_make_driver(rramc        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_rramc.c)
nrfx_make_driver(rtc          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_rtc.c)
nrfx_make_driver(saadc        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_saadc.c)
nrfx_make_driver(spi          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_spi.c)
nrfx_make_driver(spim         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_spim.c)
nrfx_make_driver(spis         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_spis.c)
nrfx_make_driver(systick      ${nrfx_SOURCE_DIR}/drivers/src/nrfx_systick.c)
nrfx_make_driver(tbm          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_tbm.c)
nrfx_make_driver(temp         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_temp.c)
nrfx_make_driver(timer        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_timer.c)
nrfx_make_driver(twi          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_twi.c ${nrfx_SOURCE_DIR}/drivers/src/nrfx_twi_twim.c)
nrfx_make_driver(twim         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_twim.c ${nrfx_SOURCE_DIR}/drivers/src/nrfx_twi_twim.c)
nrfx_make_driver(twis         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_twis.c)
nrfx_make_driver(uart         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_uart.c)
nrfx_make_driver(uarte        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_uarte.c)
nrfx_make_driver(usbd         ${nrfx_SOURCE_DIR}/drivers/src/nrfx_usbd.c)
nrfx_make_driver(usbreg       ${nrfx_SOURCE_DIR}/drivers/src/nrfx_usbreg.c)
nrfx_make_driver(vevif        ${nrfx_SOURCE_DIR}/drivers/src/nrfx_vevif.c)
nrfx_make_driver(wdt          ${nrfx_SOURCE_DIR}/drivers/src/nrfx_wdt.c)

function(nrfx_make_helper name)
    add_library(nrfx_helpers_${name} INTERFACE)
    target_sources(nrfx_helpers_${name} INTERFACE ${ARGN})
    add_library(nrfx::helpers::${name} ALIAS nrfx_helpers_${name})
    target_link_libraries(nrfx_helpers_${name} INTERFACE nrfx_soc)
    target_include_directories(nrfx_helpers_${name}
    INTERFACE
            ${nrfx_SOURCE_DIR}/helpers)
endfunction()

nrfx_make_helper(flag32_allocator ${nrfx_SOURCE_DIR}/helpers/nrfx_flag32_allocator.c)
nrfx_make_helper(gppi  
    ${nrfx_SOURCE_DIR}/helpers/nrfx_gppi_dppi.c
    ${nrfx_SOURCE_DIR}/helpers/nrfx_gppi_ppi.c)
nrfx_make_helper(ram_ctrl ${nrfx_SOURCE_DIR}/helpers/nrfx_ram_ctrl.c)

if (MDK_TARGET_SOC MATCHES nrf54l.*)
    target_sources(nrfx_helpers_gppi INTERFACE ${nrfx_SOURCE_DIR}/helpers/nrfx_gppi_dppi_ppib_lumos.c)
else()
    target_sources(nrfx_helpers_gppi INTERFACE ${nrfx_SOURCE_DIR}/helpers/nrfx_gppi_dppi_ppib.c)
endif()

add_library(nrfx::hal ALIAS nrfx_hal)
add_library(nrfx::soc ALIAS nrfx_soc)

if (NOT DEFINED NRFX_SKIP_FORCE_ENABLE)
    file(GLOB config_files RELATIVE ${nrfx_SOURCE_DIR}/templates ${nrfx_SOURCE_DIR}/templates/nrfx_config_nrf*.h)
    foreach(file ${config_files})
        file(READ ${nrfx_SOURCE_DIR}/templates/${file} FILE_CONTENTS)
        string(REPLACE "_ENABLED 0" "_ENABLED 1" FILE_CONTENTS ${FILE_CONTENTS})
        file(WRITE ${nrfx_SOURCE_DIR}/templates/${file} ${FILE_CONTENTS})
    endforeach()
endif()