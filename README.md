# Marstek B2500-D HA integration

Thanks to this forum

https://www.photovoltaikforum.com/thread/242415-b2500-marstek-bluepalm-etc-mqtt-lokal-mit-homeassistant/


1. Create automation

alias: Marstek B2500-D Battery Status Request
description: Request configuration status of battery
triggers:
  - trigger: time_pattern
    minutes: /2
conditions: []
actions:
  - metadata: {}
    data:
      qos: 0
      retain: false
      topic: hame_energy/HMA-1/App/<MAC-ID>/ctrl
      payload: cd=01
    action: mqtt.publish
mode: single

2. Create sensors (configuration.yaml)

# Solar battery sensors
mqtt:
  binary_sensor:
    - name: "Input1 state"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      payload_on: "1"
      payload_off: "0"
      value_template: "{{ value.split(',')[0].split('=')[1] }}"
      unique_id: "BP2500-1_solar_input_status_1"

    - name: "Input2 state"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      payload_on: "1"
      payload_off: "0"
      value_template: "{{ value.split(',')[1].split('=')[1] }}"
      unique_id: "BP2500-1_solar_input_status_2"

    - name: "Output state"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      payload_on: "1"
      payload_off: "0"
      value_template: "{{ value.split(',')[11].split('=')[1] }}"
      unique_id: "BP2500-1_output_state_2"

  sensor:
    - name: "Panel 1"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      unit_of_measurement: "W"
      value_template: "{{ value.split(',')[2].split('=')[1] }}"
      unique_id: "BP2500-1_solar_1_input_power"

    - name: "Panel 2"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      unit_of_measurement: "W"
      value_template: "{{ value.split(',')[3].split('=')[1] }}"
      unique_id: "BP2500-1_solar_2_input_power"

    - name: "Charge Percentage"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      unit_of_measurement: "%"
      value_template: "{{ value.split(',')[4].split('=')[1] }}"
      unique_id: "BP2500-1_battery_percentage"

    - name: "Device Version"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      value_template: "{{ value.split(',')[5].split('=')[1] }}"
      unique_id: "BP2500-1_device_version_number"

    - name: "Charge mode"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      value_template: "{{ value.split(',')[7].split('=')[1] }}"
      unique_id: "BP2500-1_charging_settings"

    - name: "Discharge mode"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      value_template: "{{ value.split(',')[8].split('=')[1] }}"
      unique_id: "BP2500-1_discharge_settings"

    - name: "DOD"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      unit_of_measurement: "%"
      value_template: "{{ value.split(',')[12].split('=')[1] }}"
      unique_id: "BP2500-1_dod_discharge_depth"

    - name: "Discharge threshold"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      unit_of_measurement: "W"
      value_template: "{{ value.split(',')[13].split('=')[1] }}"
      unique_id: "BP2500-1_battery_output_threshold"

    - name: "Charge"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      unit_of_measurement: "Wh"
      value_template: "{{ value.split(',')[15].split('=')[1] }}"
      unique_id: "BP2500-1_battery_capacity"

    - name: "Output power"
      state_topic: "hame_energy/HMA-1/device/<MAC-ID>/ctrl"
      unit_of_measurement: "W"
      value_template: "{{ value.split(',')[17].split('=')[1] }}"
      unique_id: "BP2500-1_output_power_2"   

3. Restart HA

4. Create UI card

type: entities
entities:
  - entity: sensor.charge
    icon: mdi:battery-high
  - entity: sensor.charge_percentage
    icon: mdi:battery-high
    name: Charge %
  - entity: sensor.charge_mode
  - entity: sensor.discharge_mode
  - entity: sensor.discharge_threshold
  - entity: sensor.dod
    icon: mdi:battery-10
  - entity: binary_sensor.input1_state
  - entity: binary_sensor.input2_state
  - entity: sensor.output_power
    icon: mdi:home-lightning-bolt-outline
  - entity: binary_sensor.output_state
  - entity: sensor.panel_1
    icon: mdi:solar-power-variant-outline
  - entity: sensor.panel_2
    icon: mdi:solar-power-variant-outline
  - entity: sensor.device_version
title: Marstek B2500-D


         
