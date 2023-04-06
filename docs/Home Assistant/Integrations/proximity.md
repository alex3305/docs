# Proximity

The [Proximity integration](https://www.home-assistant.io/integrations/proximity/) can be very helpful for certain situations.

## Near a zone

From [a question on Reddit](https://old.reddit.com/r/homeassistant/comments/12dvg2d/time_spent_in_zone_ignoring_leaves_in_the_middle/) to check whether or not a person is near a zone. Or in this case, when he is on a midday walk, we can use the proximity integration.

First up we'll add a proximity sensor to our `configuration.yaml`:

```yaml
proximity:
  office:
    zone: office
    devices:
      - device_tracker.alex
    tolerance: 100
    unit_of_measurement: m
```

This will add a proximity around the **office** zone. This we can than use to see whether or not we are near this zone with a [template sensor](https://www.home-assistant.io/integrations/template/):


```yaml
template:
  - binary_sensor:
      - name: "Alex in Office"
        state: "{{ (states('proximity.office') | int(0)) < 500 }}"
```

And we can even combine this with the [Workday integration](https://www.home-assistant.io/integrations/workday/):

```yaml
template:
  - binary_sensor:
      - name: "In Office"
        state: >
          {{ ((states('proximity.home') | int(0)) < 1000) and 
             is_state('binary.binary_sensor.workday', 'on') }}
```

??? note "Original answer"

    [Reddit Permalink](https://old.reddit.com/r/homeassistant/comments/12dvg2d/time_spent_in_zone_ignoring_leaves_in_the_middle/jf89660/)
