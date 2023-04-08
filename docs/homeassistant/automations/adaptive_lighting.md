# Adaptive Lighting

I want to have several of my lights adapt to the outside brightness. For Home Assistant Bas Nijholt created the fantastic [Adaptive Lighting custom component](https://github.com/basnijholt/adaptive-lighting). However there is one downside, this integration doesn't support brightness based on external sensors. Although there is apparently [a workaround](https://github.com/basnijholt/adaptive-lighting/issues/443). That's why I choose to roll my own using an automation.

## Prerequisites

* An illuminance entity, I use a max average over an hour for a smoother experience
* The [Sun integration](https://www.home-assistant.io/integrations/sun/) _(for color temperature)_
* Knowledge of your:
  * Minimum and maximum illuminance
  * Minimum and maximum light brightness
  * Minimum and maximum color temperature (optional)

## Automation

!!! note

    This automation can be pretty resource intensive. Because of the loop and the constant `light.turn_on` service calls. Normally it shouldn't be a problem. Mostly because of the built-in delays. But you should monitor your resource usage when first trying this out.

```yaml
- id: office_adaptive_lighting
  alias: Office - adaptive lighting
  mode: restart
  variables:
    # Which illuminance entity to use
    illuminance_entity: 'sensor.light_sensors_illuminance_max_average_1_hour'
    # Whether or not to continue adjust the brightness
    continuous_adjustment: true

    # Minimal outside illuminance (in lux)
    minimal_illuminance: 5
    # Maximal outside illuminance (in lux)
    maximal_illuminance: 4000

    # Minimal light brightness, will be mapped to minimal illuminance
    minimal_brightness: 10
    # Maximal light brightness, will be mapped to maximal illuminance
    maximal_brightness: 128

    # Whether color temperature adjustment is also enabled
    color_temp_adjustment: true
    # Minimal color temp (higher value = warmer)
    minimal_color_temp: 350
    # Maximal color temp (lower value = cooler)
    maximal_color_temp: 250
  trigger:
      # Trigger when our light turns on
    - platform: state
      entity_id: light.office
      from: 'off'
      to: 'on'
      # Trigger on Home Assistant (re)start
    - platform: homeassistant
      event: start
  condition:
      # Always check if the light is on before continuing.
    - condition: state
      entity_id: light.office
      state: 'on'
  action:
    # Repeat for continuously adjustment
    repeat:
      sequence:
        - service: light.turn_on
          target:
            entity_id: light.office
          data:
            # The brightness is mapped from the input values to the output values
            brightness: >-
              {% set illuminance  = states(illuminance_entity) | int(0) %}
              {% set input_start  = minimal_illuminance | int(0) %}
              {% set input_end    = maximal_illuminance | int(0) %}
              {% set output_start = minimal_brightness | int(0) %}
              {% set output_end   = maximal_brightness | int(0) %}

              {{ [[(illuminance - input_start) / (input_end - input_start) * (output_end - output_start) + output_start, output_end] | min, output_start] | max }}
            # Add a transition for a smoother experience (optional)
            transition: "{{ (iif(repeat.first, 1, 5) | int(0)) }}"
        - delay:
            seconds: "{{ (iif(repeat.first, 1, 5) | int(0)) }}"
        - service: light.turn_on
          target:
            entity_id: light.office
          data:
            # Inspired from: https://community.home-assistant.io/t/template-entity-to-guess-the-outside-colour-temperature/402510
            color_temp: >-
              {% if (color_temp_adjustment | default(false)) %}
                {{ [ [ (1000000/(4791.67 - 3290.66/(1 + 0.222 * [ [ 0, state_attr('sun.sun', 'elevation') ] | max, 90 ] | min**0.81)))|int, (maximal_color_temp|int(0)) ] | max, (minimal_color_temp|int(0)) ] | min }}
              {% else %}
                {{ state_attr('light.office', color_temp) }}
              {% endif %}
            # Add a transition for a smoother experience (optional)
            transition: "{{ (iif(repeat.first, 1, 3) | int(0)) }}"
        - wait_for_trigger:
              # Wait for a illuminance change
            - platform: state
              entity_id: sensor.light_sensors_illuminance_max_average_1_hour
              # Wait for the light to turn off
            - platform: state
              entity_id: light.office
              to: 'off'
          timeout:
            minutes: 15
          continue_on_timeout: true
          # Add a delay to lower resource usage
        - delay:
            minutes: 1
      until:
        or:
            # Continue until continuous_adjustment = off
          - condition: template
            value_template: "{{ (continuous_adjustment | default(false)) == false }}"
            # Continue until it is below our minimal illuminance (too dark)
          - condition: template
            value_template: "{{ (states(illuminance_entity) | int(0)) <= (minimal_illuminance | int(0)) }}"
            # Continue until the light is off
          - condition: state
            entity_id: light.office
            state: 'off'
            # Continue until the light is unavailable
          - condition: state
            entity_id: light.office
            state: 'unavailable'
            for:
              minutes: 1
```
