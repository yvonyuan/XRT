# SPDX-License-Identifier: Apache-2.0
# Copyright (C) 2022 Advanced Micro Devices, Inc.  All rights reserved.
#

# ====================================================================
# This builds the AIE Profile plugin.  It is currently built
# on both Edge and x86 platforms that support AIE.
# ====================================================================

if (${XRT_NATIVE_BUILD} STREQUAL "yes")
  set(IMPL_DIR "${PROFILE_DIR}/plugin/aie_profile_new/x86")
elseif (DEFINED XRT_AIE_BUILD AND ${XRT_NATIVE_BUILD} STREQUAL "no")
  set(IMPL_DIR "${PROFILE_DIR}/plugin/aie_profile_new/edge")
endif()

file(GLOB AIE_PROFILE_PLUGIN_FILES
  "${PROFILE_DIR}/plugin/aie_profile_new/*.h"
  "${PROFILE_DIR}/plugin/aie_profile_new/*.cpp"
  "${PROFILE_DIR}/writer/aie_profile/*.h"
  "${PROFILE_DIR}/writer/aie_profile/*.cpp"
  "${IMPL_DIR}/*.h"
  "${IMPL_DIR}/*.cpp"
)

if (${XRT_NATIVE_BUILD} STREQUAL "yes")

  add_library(xdp_aie_profile_plugin MODULE ${AIE_PROFILE_PLUGIN_FILES})
  add_dependencies(xdp_aie_profile_plugin xdp_core xrt_coreutil)
  target_link_libraries(xdp_aie_profile_plugin PRIVATE xdp_core xrt_coreutil)
  target_compile_definitions(xdp_aie_profile_plugin PRIVATE XRT_X86_BUILD=1)

  set_target_properties(xdp_aie_profile_plugin PROPERTIES VERSION ${XRT_VERSION_STRING} SOVERSION ${XRT_SOVERSION})

  install (TARGETS xdp_aie_profile_plugin
    LIBRARY DESTINATION ${XDP_PLUGIN_INSTALL_DIR}
  )

elseif (DEFINED XRT_AIE_BUILD AND ${XRT_NATIVE_BUILD} STREQUAL "no")
  add_library(xdp_aie_profile_plugin MODULE ${AIE_PROFILE_PLUGIN_FILES})

  add_dependencies(xdp_aie_profile_plugin xdp_core xrt_coreutil)
  target_link_libraries(xdp_aie_profile_plugin PRIVATE xdp_core xrt_coreutil metal xaiengine)
  set_target_properties(xdp_aie_profile_plugin PROPERTIES VERSION ${XRT_VERSION_STRING} SOVERSION ${XRT_SOVERSION})

  install (TARGETS xdp_aie_profile_plugin
    LIBRARY DESTINATION ${XDP_PLUGIN_INSTALL_DIR}
  )

# Else, on edge-aarch64 don't build at all

endif()
