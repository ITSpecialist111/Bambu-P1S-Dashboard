# Bambu-P1S-Dashboard


views:
  - title: 3D Printer Control Center
    icon: mdi:printer-3d-nozzle
    type: sections
    sections:
      - type: grid
        columns: 3
        cards:
          - type: heading
            heading: Printer Status
          - type: custom:mushroom-template-card
            primary: Print Status
            secondary: >
              {% if states('binary_sensor.p1s_01p00c472000402_print_error') ==
              'on' %}
                Error Detected
              {% else %}
                {{ states('sensor.p1s_01p00c472000402_print_status') | title }}
              {% endif %}
            icon: mdi:printer-3d
            icon_color: >
              {% if states('binary_sensor.p1s_01p00c472000402_print_error') ==
              'on' %}red {% elif
              states('sensor.p1s_01p00c472000402_print_status') == 'running'
              %}green {% else %}blue{% endif %}
          - type: custom:mushroom-template-card
            primary: Print Progress
            secondary: '{{ states(''sensor.p1s_01p00c472000402_print_progress'') }}%'
            icon: mdi:progress-clock
            icon_color: >
              {% set progress =
              states('sensor.p1s_01p00c472000402_print_progress')|float %} {% if
              progress > 75 %}green {% elif progress > 25 %}yellow {% else
              %}blue{% endif %}
          - type: custom:mushroom-template-card
            primary: Remaining Time
            secondary: '{{ states(''sensor.p1s_01p00c472000402_remaining_time'') }} minutes'
            icon: mdi:timer-outline
          - type: custom:mushroom-template-card
            primary: Estimated Completion
            secondary: >
              {% set mins =
              states('sensor.p1s_01p00c472000402_remaining_time')|int(0) %} {%
              set hours = (mins / 60)|int %} {% set extra_mins = mins % 60 %} {%
              set total_mins = now().minute + extra_mins %} {% set extra_hours =
              (total_mins / 60)|int %} {% set final_mins = total_mins % 60 %} {%
              set target_hour = (now().hour + hours + extra_hours)|int %} {% if
              target_hour >= 24 %}
                {% set target_hour = target_hour % 24 %}
              {% endif %} {{ target_hour }}:{{ '%02d'|format(final_mins) }}  {%
              if target_hour >= 12 %}PM{% else %}AM{% endif %}
            icon: mdi:clock-end
          - type: heading
            heading: Temperature Control
          - type: custom:mushroom-template-card
            primary: Nozzle Temperature
            secondary: >
              {{ states('sensor.p1s_01p00c472000402_nozzle_temperature') }}°C / 
              {{ states('sensor.p1s_01p00c472000402_nozzle_target_temperature')
              }}°C (Max: 300°C)
            icon: mdi:printer-3d-nozzle-heat
            icon_color: >
              {% set temp =
              states('sensor.p1s_01p00c472000402_nozzle_temperature')|float %}
              {% if temp > 250 %}red{% elif temp > 200 %}orange{% else %}blue{%
              endif %}
          - type: custom:mushroom-template-card
            primary: Bed Temperature
            secondary: >
              {{ states('sensor.p1s_01p00c472000402_bed_temperature') }}°C /  {{
              states('sensor.p1s_01p00c472000402_bed_target_temperature') }}°C
              (Max: 100°C)
            icon: mdi:radiator
            icon_color: >
              {% set temp =
              states('sensor.p1s_01p00c472000402_bed_temperature')|float %} {%
              if temp > 80 %}red{% elif temp > 60 %}orange{% else %}blue{% endif
              %}
          - type: heading
            heading: Fans
          - type: custom:mushroom-fan-card
            entity: fan.p1s_01p00c472000402_cooling_fan
            icon_animation: true
            show_percentage_control: true
            show_oscillating_control: false
            layout: horizontal
            fill_container: true
            name: Cooling Fan
            icon: mdi:fan
            secondary_info: '{{ states(''sensor.p1s_01p00c472000402_cooling_fan_speed'') }}%'
          - type: custom:mushroom-fan-card
            entity: fan.p1s_01p00c472000402_chamber_fan
            icon_animation: true
            show_percentage_control: true
            show_oscillating_control: false
            layout: horizontal
            fill_container: true
            name: Chamber Fan
            icon: mdi:fan
            secondary_info: '{{ states(''sensor.p1s_01p00c472000402_chamber_fan_speed'') }}%'
          - type: custom:mushroom-fan-card
            entity: fan.p1s_01p00c472000402_aux_fan
            icon_animation: true
            show_percentage_control: true
            horizontal: false
            layout: horizontal
            fill_container: true
            name: Auxiliary Fan
            icon: mdi:fan
            secondary_info: '{{ states(''sensor.p1s_01p00c472000402_aux_fan_speed'') }}%'
          - type: heading
            icon: ''
            heading: Technical Limits
            heading_style: subtitle
          - type: custom:mushroom-template-card
            primary: Max Speed
            secondary: 500 mm/s
            icon: mdi:speedometer
            icon_color: >
              {% if states('sensor.p1s_01p00c472000402_printing_speed') ==
              'fast' %}orange {% elif
              states('sensor.p1s_01p00c472000402_printing_speed') == 'silent'
              %}green {% else %}blue{% endif %}
          - type: custom:mushroom-template-card
            primary: Max Flow Rate
            secondary: 32 mm³/s @ABS
            icon: mdi:printer-3d-nozzle
            icon_color: blue
          - type: custom:mushroom-template-card
            primary: Build Volume
            secondary: 256 × 256 × 256 mm³
            icon: mdi:cube-outline
            icon_color: blue
          - type: custom:mushroom-template-card
            primary: Current Nozzle
            secondary: >
              {{
              states('sensor.p1s_01p00c472000402_nozzle_diameter')|default('0.4')
              }} mm
            icon: mdi:circle-small
            icon_color: blue
          - type: heading
            icon: ''
            heading: Build Plate
            heading_style: title
          - type: entities
            entities:
              - type: custom:mushroom-template-card
                primary: Current Plate
                secondary: |
                  PEI Textured
                icon: mdi:rectangle
                layout: horizontal
              - type: custom:mushroom-template-card
                primary: Max Plate Temp
                secondary: 100°C
                icon: mdi:thermometer-high
                layout: horizontal
            grid_options:
              columns: 12
              rows: auto
      - cards:
          - type: heading
            heading: Current Print
          - type: custom:mushroom-template-card
            layout: horizontal
            fill_container: true
            primary: '{{ states(''sensor.p1s_01p00c472000402_task_name'') }}'
            secondary: >-
              Layer {{ states('sensor.p1s_01p00c472000402_current_layer') }} of
              {{ states('sensor.p1s_01p00c472000402_total_layer_count') }} ({{
              (states('sensor.p1s_01p00c472000402_current_layer') | float /
              states('sensor.p1s_01p00c472000402_total_layer_count') | float *
              100) | round(1) }}%)
            icon: mdi:layers-triple
            icon_color: >
              {% if states('sensor.p1s_01p00c472000402_print_status') ==
              'running' %}green{% else %}gray{% endif %}
          - type: custom:mushroom-template-card
            primary: Material Used
            secondary: '{{ states(''sensor.p1s_01p00c472000402_total_usage'') }}g'
            icon: mdi:weight-gram
          - type: custom:mushroom-template-card
            primary: Cost Per Hour
            secondary: >-
              {% set grams =
              states('sensor.p1s_01p00c472000402_total_usage')|float %} {% set
              minutes =
              states('sensor.p1s_01p00c472000402_remaining_time')|float %} {%
              set cost = (grams * 14.0 / 1000) | round(2) %} {% set hours =
              (minutes / 60) | round(1) %} {% if hours > 0 %}
                £{{ (cost / hours) | round(2) }}/hour
              {% else %}
                Calculating...
              {% endif %}
            icon: mdi:clock-outline
            icon_color: blue
          - square: true
            type: grid
            cards:
              - type: custom:mushroom-template-card
                primary: Material Cost
                secondary: >-
                  {% set grams =
                  states('sensor.p1s_01p00c472000402_total_usage')|float %} {%
                  set cost_per_gram = 14.0 / 1000 %} {% set print_cost = (grams
                  * cost_per_gram) | round(2) %} £{{ print_cost }} ({{ grams }}g
                  @ £14/kg)
                icon: mdi:currency-gbp
                icon_color: >-
                  {% set cost =
                  (states('sensor.p1s_01p00c472000402_total_usage')|float * 14.0
                  / 1000) | round(2) %} {% if cost > 10 %}red{% elif cost > 5
                  %}orange{% else %}green{% endif %}
                fill_container: false
            grid_options:
              columns: 36
              rows: 1
            columns: 1
          - type: heading
            heading: Temperature History
          - type: custom:apexcharts-card
            header:
              show: true
              title: Temperature History
              show_states: true
            series:
              - entity: sensor.p1s_01p00c472000402_nozzle_temperature
                name: Nozzle
                type: line
                stroke_width: 2
                curve: smooth
                yaxis_id: nozzle
                color: '#e74c3c'
                show:
                  in_header: true
                  legend_value: true
                max: 300
                unit: °C
              - entity: sensor.p1s_01p00c472000402_bed_temperature
                name: Bed
                type: line
                stroke_width: 2
                curve: smooth
                yaxis_id: bed
                color: '#3498db'
                show:
                  in_header: true
                  legend_value: true
                max: 100
                unit: °C
            chart_type: line
            span:
              start: hour
            apex_config:
              yaxis:
                - id: nozzle
                  min: 0
                  max: 300
                  tickAmount: 5
                  title:
                    text: Nozzle °C
                - id: bed
                  min: 0
                  max: 100
                  opposite: true
                  tickAmount: 5
                  title:
                    text: Bed °C
          - type: heading
            heading: Print Controls
          - type: horizontal-stack
            cards:
              - type: custom:mushroom-template-card
                primary: Pause Print
                icon: mdi:pause-circle
                tap_action:
                  action: call-service
                  service: button.press
                  target:
                    entity_id: button.p1s_01p00c472000402_pause_printing
                fill_container: false
              - type: custom:mushroom-template-card
                primary: Resume Print
                icon: mdi:play-circle
                tap_action:
                  action: call-service
                  service: button.press
                  target:
                    entity_id: button.p1s_01p00c472000402_resume_printing
                icon_color: ''
            grid_options:
              columns: full
              rows: 1
          - type: custom:mushroom-template-card
            primary: Stop Print
            icon: mdi:stop-circle
            icon_color: red
            hold_action:
              action: call-service
              service: button.press
              target:
                entity_id: button.p1s_01p00c472000402_stop_printing
            layout: vertical
            grid_options:
              columns: 6
              rows: 2
          - type: custom:mushroom-template-card
            primary: Refresh Data
            icon: mdi:refresh
            tap_action:
              action: call-service
              service: button.press
              target:
                entity_id: button.p1s_01p00c472000402_force_refresh_data
            icon_color: ''
            layout: vertical
      - type: grid
        columns: 2
        cards:
          - type: heading
            heading: Camera
          - show_state: false
            show_name: false
            camera_view: auto
            type: picture-entity
            entity: camera.p1s_01p00c472000402_camera
          - type: entities
            title: Camera Controls
            entities:
              - entity: switch.p1s_01p00c472000402_enable_camera
                name: Camera Feed
              - entity: light.p1s_01p00c472000402_chamber_light
                name: Chamber Light
              - entity: binary_sensor.p1s_01p00c472000402_recording_timelapse
                name: Recording Timelapse
              - entity: switch.p1s_01p00c472000402_use_image_sensor_camera
                name: Use Image Sensor
          - type: heading
            heading: Settings
          - type: entities
            title: Print Controls
            entities:
              - entity: select.p1s_01p00c472000402_printing_speed
                name: Printing Speed
              - entity: switch.p1s_01p00c472000402_manual_refresh_mode
                name: Manual Refresh Mode
              - entity: button.p1s_01p00c472000402_force_refresh_data
                name: Force Refresh Data
      - type: grid
        columns: 2
        cards:
          - type: heading
            heading: Material Status
          - type: custom:mushroom-template-card
            primary: Active Material
            secondary: >
              {{ states('sensor.p1s_01p00c472000402_active_tray') }} (Slot {{
              states('sensor.p1s_01p00c472000402_active_tray_index') }})
            icon: mdi:spool
            icon_color: green
            grid_options:
              columns: 9
              rows: 1
          - type: custom:mushroom-template-card
            primary: AMS Humidity
            secondary: >
              Index: {{
              states('sensor.p1s_01p00c472000402_ams_1_humidity_index') }} {% if
              states('sensor.p1s_01p00c472000402_ams_1_humidity_index')|float >
              30 %}
                (High - Check Filament)
              {% endif %}
            icon: mdi:water-percent
            icon_color: >
              {% set humidity =
              states('sensor.p1s_01p00c472000402_ams_1_humidity_index')|float %}
              {% if humidity > 30 %}red {% elif humidity > 20 %}yellow {% else
              %}green{% endif %}
          - type: heading
            heading: Filament Trays
          - type: horizontal-stack
            cards:
              - type: custom:button-card
                entity: sensor.p1s_01p00c472000402_ams_1_tray_1
                name: Tray 1
                icon: mdi:palette
                state_display: '[[[ return entity.attributes.color ]]]'
                styles:
                  card:
                    - background-color: '[[[ return entity.attributes.color ]]]'
                    - color: white
              - type: custom:button-card
                entity: sensor.p1s_01p00c472000402_ams_1_tray_2
                name: Tray 2
                icon: mdi:palette
                state_display: '[[[ return entity.attributes.color ]]]'
                styles:
                  card:
                    - background-color: '[[[ return entity.attributes.color ]]]'
                    - color: white
              - type: custom:button-card
                entity: sensor.p1s_01p00c472000402_ams_1_tray_3
                name: Tray 3
                icon: mdi:palette
                state_display: '[[[ return entity.attributes.color ]]]'
                styles:
                  card:
                    - background-color: '[[[ return entity.attributes.color ]]]'
                    - color: black
              - type: custom:button-card
                entity: sensor.p1s_01p00c472000402_ams_1_tray_4
                name: Tray 4
                icon: mdi:palette
                state_display: '[[[ return entity.attributes.color ]]]'
                styles:
                  card:
                    - background-color: '[[[ return entity.attributes.color ]]]'
                    - color: white
          - type: custom:apexcharts-card
            header:
              show: true
              title: Filament Remaining
            chart_type: radialBar
            series:
              - entity: sensor.p1s_01p00c472000402_ams_1_tray_1
                name: Tray 1
                attribute: remain
              - entity: sensor.p1s_01p00c472000402_ams_1_tray_2
                name: Tray 2
                attribute: remain
              - entity: sensor.p1s_01p00c472000402_ams_1_tray_3
                name: Tray 3
                attribute: remain
              - entity: sensor.p1s_01p00c472000402_ams_1_tray_4
                name: Tray 4
                attribute: remain
            apex_config:
              chart:
                height: 250
              plotOptions:
                radialBar:
                  dataLabels:
                    name:
                      show: true
                    value:
                      show: true
                      suffix: '%'
                  hollow:
                    size: 20%
          - type: heading
            icon: ''
            heading: Connectivity
            heading_style: title
          - type: entities
            entities:
              - type: custom:mushroom-template-card
                primary: Network Status
                secondary: >
                  {% if states('binary_sensor.p1s_01p00c472000402_online') ==
                  'on' %}
                    Connected (WiFi)
                  {% else %}
                    Disconnected
                  {% endif %}
                icon: mdi:wifi
                icon_color: >
                  {% if states('binary_sensor.p1s_01p00c472000402_online') ==
                  'on' %}
                    green
                  {% else %}
                    red
                  {% endif %}
              - type: custom:mushroom-template-card
                primary: Camera Status
                secondary: >
                  {% if
                  states('binary_sensor.p1s_01p00c472000402_camera_connected')
                  == 'on' %}
                    Connected (720p)
                  {% else %}
                    Disconnected
                  {% endif %}
                icon: mdi:webcam
                icon_color: >
                  {% if
                  states('binary_sensor.p1s_01p00c472000402_camera_connected')
                  == 'on' %}
                    green
                  {% else %}
                    red
                  {% endif %}
          - type: heading
            icon: ''
            heading: Carbon Filter
            heading_style: title
          - type: grid
            columns: 2
            cards:
              - type: custom:mushroom-template-card
                primary: Filter Age
                secondary: >-
                  {% set days = ((as_timestamp(now()) -
                  states.input_datetime.filter_installation_date.attributes.timestamp)
                  / 86400) | round(0) %} {{ days }} days installed
                icon: mdi:air-filter
                icon_color: >-
                  {% set days = ((as_timestamp(now()) -
                  states.input_datetime.filter_installation_date.attributes.timestamp)
                  / 86400) | round(0) %} {% if days > 180 %}red{% elif days >
                  150 %}orange{% else %}green{% endif %}
              - type: custom:mushroom-template-card
                primary: Next Change Due
                secondary: >-
                  {% set next_change =
                  states.input_datetime.filter_installation_date.attributes.timestamp
                  + (180 * 86400) %} {{ next_change | timestamp_custom("%B %d,
                  %Y") }}
                icon: mdi:calendar-clock
                icon_color: >-
                  {% set days_left =
                  ((states.input_datetime.filter_installation_date.attributes.timestamp
                  + (180 * 86400) - as_timestamp(now())) / 86400) | round(0) %}
                  {% if days_left <= 0 %}red{% elif days_left <= 30 %}orange{%
                  else %}green{% endif %}
            grid_options:
              columns: 36
              rows: 1
      - type: grid
        cards:
          - type: heading
            heading_style: title
            heading: Print Costs
          - square: false
            type: grid
            cards:
              - type: custom:mushroom-template-card
                primary: Material Cost
                secondary: >-
                  {% set grams =
                  states('sensor.p1s_01p00c472000402_total_usage')|float %} {%
                  set cost_per_gram = 14.0 / 1000 %} {% set print_cost = (grams
                  * cost_per_gram) | round(2) %} £{{ print_cost }} ({{ grams }}g
                  @ £14/kg)
                icon: mdi:currency-gbp
                icon_color: >-
                  {% set cost =
                  (states('sensor.p1s_01p00c472000402_total_usage')|float * 14.0
                  / 1000) | round(2) %} {% if cost > 10 %}red{% elif cost > 5
                  %}orange{% else %}green{% endif %}
                fill_container: false
                layout: horizontal
              - type: custom:mushroom-template-card
                primary: Power Usage
                secondary: >-
                  {% set material_type =
                  states('sensor.p1s_01p00c472000402_active_filament_type')|lower
                  %} {% if material_type == 'pla' %}
                    ~105W (PLA Average)
                  {% elif material_type == 'petg' or material_type == 'abs' %}
                    ~140W (PETG/ABS Average)
                  {% elif material_type == 'pc' %}
                    ~135W (PC Average)
                  {% else %}
                    ~105W (Default)
                  {% endif %} Power Factor: 0.45-0.63
                icon: mdi:lightning-bolt
                icon_color: blue
                fill_container: false
              - type: custom:mushroom-template-card
                primary: Electricity Cost
                secondary: >-
                  {% set minutes =
                  states('sensor.p1s_01p00c472000402_remaining_time')|float %}
                  {% set hours = (minutes / 60) %} {% set material_type =
                  states('sensor.p1s_01p00c472000402_active_filament_type')|lower
                  %} {% set power = 105 if material_type == 'pla' else (140 if
                  material_type in ['petg', 'abs'] else 135) %} {% set kwh =
                  (hours * power / 1000) %} {% set cost = (kwh * 0.34) |
                  round(2) %} £{{ cost }} ({{ kwh | round(2) }} kWh @ 34p/kWh)
                icon: mdi:flash
                icon_color: >-
                  {% set cost =
                  ((states('sensor.p1s_01p00c472000402_remaining_time')|float /
                  60) * 0.105 * 0.34) %} {% if cost > 2 %}red{% elif cost > 1
                  %}orange{% else %}green{% endif %}
                fill_container: false
              - type: custom:mushroom-template-card
                primary: Total Print Cost
                secondary: >-
                  {% set grams =
                  states('sensor.p1s_01p00c472000402_total_usage')|float %} {%
                  set material_cost = (grams * 14.0 / 1000) | round(2) %} {% set
                  minutes =
                  states('sensor.p1s_01p00c472000402_remaining_time')|float %}
                  {% set hours = (minutes / 60) %} {% set material_type =
                  states('sensor.p1s_01p00c472000402_active_filament_type')|lower
                  %} {% set power = 105 if material_type == 'pla' else (140 if
                  material_type in ['petg', 'abs'] else 135) %} {% set kwh =
                  (hours * power / 1000) %} {% set electricity_cost = (kwh *
                  0.34) | round(2) %} {% set total_cost = material_cost +
                  electricity_cost %} £{{ total_cost }} (Material: £{{
                  material_cost }}, Power: £{{ electricity_cost }})
                icon: mdi:calculator
                icon_color: >-
                  {% set total_cost =
                  (states('sensor.p1s_01p00c472000402_total_usage')|float * 14.0
                  / 1000) + 
                  ((states('sensor.p1s_01p00c472000402_remaining_time')|float /
                  60) * 0.105 * 0.34) %} {% if total_cost > 15 %}red{% elif
                  total_cost > 7 %}orange{% else %}green{% endif %}
                fill_container: false
            grid_options:
              columns: full
            columns: 1
