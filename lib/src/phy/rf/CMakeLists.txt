#
# Copyright 2013-2020 Software Radio Systems Limited
#
# This file is part of srsLTE
#
# srsLTE is free software: you can redistribute it and/or modify
# it under the terms of the GNU Affero General Public License as
# published by the Free Software Foundation, either version 3 of
# the License, or (at your option) any later version.
#
# srsLTE is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
# GNU Affero General Public License for more details.
#
# A copy of the GNU Affero General Public License can be found in
# the LICENSE file in the top-level directory of this distribution
# and at http://www.gnu.org/licenses/.
#

if(RF_FOUND)
  # This library is only used by the examples
  add_library(srslte_rf_utils STATIC rf_utils.c)
  target_link_libraries(srslte_rf_utils srslte_phy)

  # Include common RF files
  set(SOURCES_RF "")
  list(APPEND SOURCES_RF rf_imp.c)

  if (UHD_FOUND)
    add_definitions(-DENABLE_UHD)
    list(APPEND SOURCES_RF rf_uhd_imp.cc)

    # If found, add a macro to inform the UHD driver about the available feature
    if (UHD_ENABLE_X300_FW_RESET)
      add_definitions(-DENABLE_UHD_X300_FW_RESET)
    endif(UHD_ENABLE_X300_FW_RESET)
    if (UHD_ENABLE_RFNOC)
      add_definitions(-DUHD_ENABLE_RFNOC)
    endif(UHD_ENABLE_RFNOC)
    if (UHD_ENABLE_CUSTOM_RFNOC)
      add_definitions(-DUHD_ENABLE_CUSTOM_RFNOC)
    endif(UHD_ENABLE_CUSTOM_RFNOC)
  endif (UHD_FOUND)

  if (UHD_FOUND AND UHD_ENABLE_CUSTOM_RFNOC)
    add_executable(rfnoc_test rfnoc_test.cc)
    target_link_libraries(rfnoc_test srslte_rf ${UHD_LIBRARIES} ${Boost_LIBRARIES} /usr/lib/x86_64-linux-gnu/libboost_system.so)
    message(info ${Boost_LIBRARIES})
  endif (UHD_FOUND AND UHD_ENABLE_CUSTOM_RFNOC)

  if (BLADERF_FOUND)
    add_definitions(-DENABLE_BLADERF)
    list(APPEND SOURCES_RF rf_blade_imp.c)
  endif (BLADERF_FOUND)

  if (SOAPYSDR_FOUND AND ENABLE_SOAPYSDR)
    add_definitions(-DENABLE_SOAPYSDR)
    list(APPEND SOURCES_RF rf_soapy_imp.c)
  endif (SOAPYSDR_FOUND AND ENABLE_SOAPYSDR)

  if (LIMESDR_FOUND AND ENABLE_LIMESDR)
    add_definitions(-DENABLE_LIMESDR)
    list(APPEND SOURCES_RF rf_limesdr_impl.c)
  endif (LIMESDR_FOUND AND ENABLE_LIMESDR)

  if (ZEROMQ_FOUND)
    add_definitions(-DENABLE_ZEROMQ)
    list(APPEND SOURCES_RF rf_zmq_imp.c rf_zmq_imp_tx.c rf_zmq_imp_rx.c)
  endif (ZEROMQ_FOUND)

  add_library(srslte_rf SHARED ${SOURCES_RF})
  target_link_libraries(srslte_rf srslte_rf_utils srslte_phy)
  set_target_properties(srslte_rf PROPERTIES VERSION ${SRSLTE_VERSION_STRING} SOVERSION ${SRSLTE_SOVERSION})

  if (UHD_FOUND)
    target_link_libraries(srslte_rf ${UHD_LIBRARIES})
  endif (UHD_FOUND)

  if (BLADERF_FOUND)
    target_link_libraries(srslte_rf ${BLADERF_LIBRARIES})
  endif (BLADERF_FOUND)

  if (SOAPYSDR_FOUND AND ENABLE_SOAPYSDR)
    target_link_libraries(srslte_rf ${SOAPYSDR_LIBRARIES})
  endif (SOAPYSDR_FOUND AND ENABLE_SOAPYSDR)

  if (ZEROMQ_FOUND)
    target_link_libraries(srslte_rf ${ZEROMQ_LIBRARIES})
    add_executable(rf_zmq_test rf_zmq_test.c)
    target_link_libraries(rf_zmq_test srslte_rf)
    #add_test(rf_zmq_test rf_zmq_test)
  endif (ZEROMQ_FOUND)

  if (LIMESDR_FOUND AND ENABLE_LIMESDR)
    target_link_libraries(srsran_rf ${LIMESDR_LIBRARIES})
  endif (LIMESDR_FOUND AND ENABLE_LIMESDR)

  INSTALL(TARGETS srslte_rf DESTINATION ${LIBRARY_DIR})
endif(RF_FOUND)
