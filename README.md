# Marstek B2500-D HA integration

Thanks to this forum

https://www.photovoltaikforum.com/thread/242415-b2500-marstek-bluepalm-etc-mqtt-lokal-mit-homeassistant/

0. Enable local MQTT using web tool

https://tomquist.github.io/hame-relay/b2500.html
(local copy here https://github.com/makserge/hame-relay/blob/main/docs/b2500.html)

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
      value_template: "{{ value.split(',')[56].split('=')[1] }}"
      unique_id: "BP2500-1_output_power_2"   

3. Restart HA

4. Create UI card

type: entities
entities:
  - entity: sensor.charge_percentage
    icon: mdi:battery-high
    name: Charge %
  - entity: sensor.charge
    icon: mdi:battery-high
  - entity: sensor.panel_1
    icon: mdi:solar-power-variant-outline
  - entity: sensor.panel_2
    icon: mdi:solar-power-variant-outline
  - entity: sensor.output_power
    icon: mdi:home-lightning-bolt-outline
  - entity: sensor.discharge_threshold
  - entity: sensor.dod
    icon: mdi:battery-10
  - entity: binary_sensor.input1_state
  - entity: binary_sensor.input2_state
  - entity: binary_sensor.output_state
title: Marstek B2500-D

5. Hichi IR TTL + ESP32-S2

Wiring 
PIN
TX 11
RX 12
3.3V
GND

Script from here

https://tasmota.github.io/docs/Smart-Meter-Interface/#general-description

For Logarex LK13BE (SML) (e.g. LK13BE6067x9)

>D
>B
->sensor53 r
>M 1
+1,12,s,0,9600,LK13BE,1,10,2F3F210D0A,063035310D0A

1,77070100010800ff@1000,Total energy in,kWh,Energy_in,1
1,77070100020800ff@1000,Total energy out,kWh,Energy_out,1
1,77070100100700ff@1,Total power,W,Power_total,0
1,77070100240700ff@1,Power L1,W,Power_L1_curr,0
1,77070100380700ff@1,Power L2,W,Power_L2_curr,0
1,770701004C0700ff@1,Power L3,W,Power_L3_curr,0
#

Install Tasmota here

Press boot and connect to power and after 2 seconds release boot

https://tasmota.github.io/install/

192.168.4.1

Update to firmware with SML support

https://github.com/ottelo9/tasmota-sml-images/releases/download/V14.6.0_250423/tasmota32s2_ottelo_250422.zip

OTA update inside Tasmota using 

tasmota32s2_ottelo.bin

In Tasmota console set parameters:

1. Switch off HA discovery and enable Tasmota discovery

SetOption19 0

2. Set MQTT Update period to 10 sec

TelePeriod 10

3. Send an update when power changes by 2 Watts

PowerDelta 102 

In HA Tasmota integration - add to dashboard 

         
