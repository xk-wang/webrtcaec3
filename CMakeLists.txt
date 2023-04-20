cmake_minimum_required(VERSION 3.6)

project(test_aec3)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_C_FLAGS "-mavx2 -mfma")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")
set(CURRENT_DIR ${CMAKE_CURRENT_SOURCE_DIR})

add_definitions(-DWEBRTC_POSIX)
add_definitions(-DWEBRTC_LINUX)

add_compile_options(-march=native)

include_directories(${CURRENT_DIR})
include_directories(${CURRENT_DIR}/api)
include_directories(${CURRENT_DIR}/base)
include_directories(${CURRENT_DIR}/audio_processing/include)
include_directories(${CURRENT_DIR}/base/abseil)
include_directories(${CURRENT_DIR}/base/system_wrappers/include)

file(GLOB AUDIOPROCESSING ${CURRENT_DIR}/audio_processing/*.cc
                          ${CURRENT_DIR}/audio_processing/*.c)
file(GLOB AEC3 ${CURRENT_DIR}/audio_processing/aec3/*.cc)
list(FILTER AEC3 EXCLUDE REGEX "unittest")
file(GLOB RESAMPLER ${CURRENT_DIR}/audio_processing/resampler/*.cc)
list(FILTER RESAMPLER EXCLUDE REGEX "_neon.cc$")
file(GLOB RTCBASE ${CURRENT_DIR}/base/rtc_base/*.cc)

add_executable(${PROJECT_NAME}
  ${CURRENT_DIR}/demo.cc
  ${CURRENT_DIR}/wavreader.c
  ${CURRENT_DIR}/wavwriter.c
  ${CURRENT_DIR}/api/echo_canceller3_config.cc
  ${CURRENT_DIR}/api/echo_canceller3_factory.cc

  ${AUDIOPROCESSING}
  ${AEC3}
  ${RESAMPLER}
  ${RTCBASE}

  ${CURRENT_DIR}/audio_processing/utility/cascaded_biquad_filter.cc
  ${CURRENT_DIR}/audio_processing/utility/ooura_fft_sse2.cc
  ${CURRENT_DIR}/audio_processing/utility/ooura_fft.cc
  ${CURRENT_DIR}/audio_processing/logging/apm_data_dumper.cc

  ${CURRENT_DIR}/base/rtc_base/memory/aligned_malloc.cc
  ${CURRENT_DIR}/base/rtc_base/strings/string_builder.cc

  ${CURRENT_DIR}/base/system_wrappers/source/metrics.cc
  ${CURRENT_DIR}/base/system_wrappers/source/field_trial.cc
  ${CURRENT_DIR}/base/system_wrappers/source/cpu_features.cc
)

add_subdirectory(${CURRENT_DIR}/base/abseil)

target_link_libraries(${PROJECT_NAME} absl::strings absl::optional absl::base)