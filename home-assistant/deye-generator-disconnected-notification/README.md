# Notification: Generator AC input rejected by Deye

**Logic**: generator voltage > `210 V` (or > `630 V` for a three-phase system), yet the inverter rejects the GEN input; the usual reason is generator frequency outside `49-51 Hz`.

## Why?

Generator–inverter coupling is often fragile because the inverter is highly sensitive. A sudden load spike or a mechanical disturbance can push the generator frequency out of range, causing the Deye to reject the GEN input. The generator keeps running but delivers no usable power. Unless you are standing next to it, this usually goes unnoticed until the fuel is exhausted.

## Dependencies

- A generator
- A Deye inverter
- Home Assistant's [Solarman Integration](https://github.com/davidrapan/ha-solarman)

## Create an automation

> [!TIP]
> Settings -> Automations & scenes -> Create automation

Before proceeding, ensure your entity IDs first:
- Inverter:
  - `binary_sensor.inverter_generator` - the Solarman's sensor indicating the actual use of a generator.
  - `number.inverter_generator_l*_voltage` - the Solarman's voltage sensors for a generator, one per phase.

  Generally, you only change the `inverter` part according to the prefix you used when adding the Solarman integration.

- Notification:
  - `notify.notify_admins` - the group/device the notification should land on. In my setup, in `configuration.yaml`, it looks this way:
    ```yaml
    notify:
      - name: "Notify admins"
        platform: group
        services:
          - action: mobile_app_br0ken
          - action: mobile_app_homie
    ```

> [!IMPORTANT]
> In case of having a single-phase system, adjust the logic:
> ```yaml
> states('sensor.inverter_generator_l1_voltage') | float(0) > 210
> ```

```yaml
mode: single
alias: Generator disconnected
description: ""
triggers:
  - alias: Generator is OFF while voltage is detected
    trigger: template
    value_template: |-
      {{
        is_state('binary_sensor.inverter_generator', 'off') and
        (
          states('sensor.inverter_generator_l1_voltage') | float(0) +
          states('sensor.inverter_generator_l2_voltage') | float(0) +
          states('sensor.inverter_generator_l3_voltage') | float(0)
        ) > 630
      }}
    for:
      hours: 0
      minutes: 0
      # Avoid short spikes.
      seconds: 20
actions:
  - variables:
      id: generator_disconnected
      payload:
        title: 🚨 Generator
        message: Disconnected!
  - action: persistent_notification.create
    metadata: {}
    data: |-
      {{
        payload | combine({
          'notification_id': id,
        })
      }}
  - action: notify.notify_admins
    metadata: {}
    enabled: true
    data: |-
      {{
        payload | combine({
          'data': {
            'priority': 'high',
            'tag': id,
          },
        })
      }}
```
