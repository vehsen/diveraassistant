# diveraassistant

A way to show your DIVERA247 vehicle statuses in Home Assistant.

Increases the visibility and awareness of the vehicle statuses.

Pulls the API, deserializes the json and mirrors the vehicle state to your dashboard.

The sorting is the same as in DIVERA [see localmanagement/sorting](https://www.divera247.com/localmanagement/sorting.html)

## Data source
### DIVERA 24/7 RESTful Webservice
[https://api.divera247.com](https://api.divera247.com/?urls.primaryName=api%2Fv2%2Fpull#/Daten%2F%20Informationen/get_api_v2_pull_vehicle_status)

![DIVERA247 Logo](https://www.divera247.com/images/divera247.svg)

---

## Preview: entity card

![Preview of entity dashboard](home-assistant-preview.png)

```
type: entities
title: Fahrzeuge Stab
entities:
  - entity: sensor.divera_vehicle_0
    type: 'custom:secondaryinfo-entity-row'
    secondary_info: '[[ sensor.divera_vehicle_0.attributes.Notiz ]]'
  - entity: sensor.divera_vehicle_1
    type: 'custom:secondaryinfo-entity-row'
    secondary_info: '[[ sensor.divera_vehicle_1.attributes.Notiz ]]'
  - entity: sensor.divera_vehicle_2
    type: 'custom:secondaryinfo-entity-row'
    secondary_info: '[[ sensor.divera_vehicle_2.attributes.Notiz ]]'
```
#### Used for additional note
- [secondaryinfo-entity-row](https://github.com/custom-cards/secondaryinfo-entity-row)
## Preview: entities

![Preview of entities](home-assistant-preview_entities.png)

## Things **you** need to change
### [configuration.yaml](configuration.yaml)
Add a command_line sensor and set your [**DIVERA247 Access-Key**](https://www.divera247.com/localmanagement/index-settings-api.html).
Adapt the [**scan_interval**](https://github.com/vehsen/diveraassistant/blob/6c40884c6af4c579969e6d6c141c7b12d4f48795/configuration.yaml#L5) [s] to your needs.
```
sensor:
  - platform: command_line
    name: divera_vehicle
    command:  curl -X GET https://www.divera247.com/api/v2/pull/vehicle-status?accesskey=YOUR-ACCESS-KEY-HERE
    scan_interval: 6000
    json_attributes:
      - data
    value_template: '{{ value_json["success"] }}'
```


:heavy_exclamation_mark: **Add the same amount of template sensors as vehicles listed in DIVERA247.com.**

:heavy_exclamation_mark: But **increment** the sensor **name** and array **count** for every template sensor.

### first vehicle
divera_vehicle_:zero: and state_attr('sensor.divera_vehicle', 'data')[:zero:]
```
sensor:
  - platform: template
    sensors:
      divera_vehicle_0:
        entity_id: sensor.divera_vehicle
        friendly_name_template: "{{ state_attr('sensor.divera_vehicle', 'data')[0]['shortname'] }}"
        value_template: >-
          {% if state_attr('sensor.divera_vehicle', 'data')[0]['fmsstatus'] == 1 %}
            funkfrei
          {% elif state_attr('sensor.divera_vehicle', 'data')[0]['fmsstatus'] == 2 %}
            auf Wache
          {% elif state_attr('sensor.divera_vehicle', 'data')[0]['fmsstatus'] == 3 %}
            Einsatz übernommen
          {% elif state_attr('sensor.divera_vehicle', 'data')[0]['fmsstatus'] == 4 %}
            Einsatzstelle an
          {% elif state_attr('sensor.divera_vehicle', 'data')[0]['fmsstatus'] == 5 %}
            Sprechwunsch
          {% elif state_attr('sensor.divera_vehicle', 'data')[0]['fmsstatus'] == 6 %}
            nicht einsatzbereit
          {% else %}
            undefiniert
          {% endif %}
        attribute_templates:
          id: "{{ state_attr('sensor.divera_vehicle', 'data')[0]['id'] }}"
          FuRn: "{{ state_attr('sensor.divera_vehicle', 'data')[0]['name'] }}"
          Fahrzeug: "{{ state_attr('sensor.divera_vehicle', 'data')[0]['fullname'] }}"
          Datum: "{{ state_attr('sensor.divera_vehicle', 'data')[0]['fmsstatus_ts'] | timestamp_custom('%d.%m.%Y %H:%M:%S') }}"
          Notiz: "{{ state_attr('sensor.divera_vehicle', 'data')[0]['fmsstatus_note'] }}"
```
### second vehicle
divera_vehicle_:one: and state_attr('sensor.divera_vehicle', 'data')[:one:]
```
sensor:
  - platform: template
    sensors:
      divera_vehicle_1:
        entity_id: sensor.divera_vehicle
        friendly_name_template: "{{ state_attr('sensor.divera_vehicle', 'data')[1]['shortname'] }}"
        value_template: >-
          {% if state_attr('sensor.divera_vehicle', 'data')[1]['fmsstatus'] == 1 %}
            funkfrei
          {% elif state_attr('sensor.divera_vehicle', 'data')[1]['fmsstatus'] == 2 %}
            auf Wache
          {% elif state_attr('sensor.divera_vehicle', 'data')[1]['fmsstatus'] == 3 %}
            Einsatz übernommen
          {% elif state_attr('sensor.divera_vehicle', 'data')[1]['fmsstatus'] == 4 %}
            Einsatzstelle an
          {% elif state_attr('sensor.divera_vehicle', 'data')[1]['fmsstatus'] == 5 %}
            Sprechwunsch
          {% elif state_attr('sensor.divera_vehicle', 'data')[1]['fmsstatus'] == 6 %}
            nicht einsatzbereit
          {% else %}
            undefiniert
          {% endif %}
        attribute_templates:
          id: "{{ state_attr('sensor.divera_vehicle', 'data')[1]['id'] }}"
          FuRn: "{{ state_attr('sensor.divera_vehicle', 'data')[1]['name'] }}"
          Fahrzeug: "{{ state_attr('sensor.divera_vehicle', 'data')[1]['fullname'] }}"
          Datum: "{{ state_attr('sensor.divera_vehicle', 'data')[1]['fmsstatus_ts'] | timestamp_custom('%d.%m.%Y %H:%M:%S') }}"
          Notiz: "{{ state_attr('sensor.divera_vehicle', 'data')[1]['fmsstatus_note'] }}"
```

### [customize.yaml](customize.yaml)
Customize by your needs

```
sensor.divera_vehicle_0:
  icon: mdi:car-estate

sensor.divera_vehicle_1:
  icon: mdi:van-passenger

sensor.divera_vehicle_2:
  icon: mdi:truck
```
[Transportation + Road @ materialdesignicons.com](https://materialdesignicons.com/tag/transportation-road)
like
- mdi:car-estate
- mdi:van-passenger
- mdi:fire-truck
- mdi:truck
- mdi:dump-truck
- mdi:truck-trailer
- mdi:excavator
- ...


