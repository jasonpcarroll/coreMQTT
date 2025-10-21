cmake_minimum_required(VERSION 3.15)

project(coreMQTT LANGUAGES C)

# -------------------------------------------------------------------------------
# Includes
# -------------------------------------------------------------------------------

# MQTT_SOURCES, MQTT_SERIALIZER_SOURCES, MQTT_INCLUDE_PUBLIC_DIRS ##

include(${CMAKE_CURRENT_LIST_DIR}/mqttFilePaths.cmake)

# -------------------------------------------------------------------------------
# Library targets
# -------------------------------------------------------------------------------

# coreMQTT serializer library ##

add_library(core_mqtt_serializer ${MQTT_SERIALIZER_SOURCES})

target_include_directories(core_mqtt_serializer
                           PUBLIC ${MQTT_INCLUDE_PUBLIC_DIRS})

# coreMQTT library ##

add_library(core_mqtt ${MQTT_SOURCES})

target_include_directories(core_mqtt PUBLIC ${MQTT_INCLUDE_PUBLIC_DIRS})

target_link_libraries(core_mqtt PUBLIC core_mqtt_serializer)

# -------------------------------------------------------------------------------
# Optional configuration file handling (core_mqtt_config.h)
# -------------------------------------------------------------------------------

if(NOT TARGET core_mqtt_config)

  if(NOT DEFINED CORE_MQTT_CONFIG_FILE_DIRECTORY)

    # Set library targets to not expect a custom configuration.

    message(STATUS "coreMQTT: Set to build with default configuration.")

    target_compile_definitions(core_mqtt_serializer
                               PUBLIC MQTT_DO_NOT_USE_CUSTOM_CONFIG)

    target_compile_definitions(core_mqtt PUBLIC MQTT_DO_NOT_USE_CUSTOM_CONFIG)

  else()

    # Set library targets to find configuration in specified folder

    message(STATUS "coreMQTT: Set to build with configuration header:\n"
                   "${CORE_MQTT_CONFIG_FILE_DIRECTORY}/core_mqtt_config.h")

    target_include_directories(core_mqtt_serializer
                               PUBLIC ${CORE_MQTT_CONFIG_FILE_DIRECTORY})

    target_include_directories(core_mqtt
                               PUBLIC ${CORE_MQTT_CONFIG_FILE_DIRECTORY})

  endif()

else()

  # Link library targets to configuration target.

  message(STATUS "coreMQTT: Set to build with configuration header:\n"
                 "${CORE_MQTT_CONFIG_FILE_DIRECTORY}/core_mqtt_config.h")

  target_link_libraries(core_mqtt_serializer PUBLIC core_mqtt_config)

  target_link_libraries(core_mqtt PUBLIC core_mqtt_config)
endif()
