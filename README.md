# Smart Home

## Hardware

- Deye SUN-20K-SG05LP3-EU-SM2
- Biom BALFP V1 300 Ah
- Biom BALFP V2 300 Ah
- Könner & Söhnen KS 10000E-3 (bad choice, the inverter generators are out of stock across the country)

## Materials

### Home Assistant

- [Temperature-based battery charging current](home-assistant/temp-based-battery-charging-current)

## FAQ

### Automatic Voltage Regulators (aka Voltage Stabilizers)

#### Which AVR is inverter-compatible?

- [Quant](https://quant.ua)
- [Volter](https://volter.ua)

They're not cheap. Buying a different one will likely result in extra spending and headache.

#### Where do I place the AVR in the chain - before or after the inverter?

An inverter outputs what it gets. It works with low/high voltage, and you can configure the boundaries. The AVR, of course, can be applied after the inverter, but generally it's a bad idea.

Placing the AVR(s) before the inverter assumes it sees the perfect grid that's never get dropped.

#### Do I need a 3-phase AVR?

It's an option, and sometimes may be cheaper than having 3 single-phase regulators.

Personally I run 3 single-phase units.

#### How to choose the rated power of an AVR?

The more, the better. Never get the AVR that is rated the same or less power than your input. A good rule of thumb is +30%.

My rated power per phase is 7 kWh, but I run 11 kWh AVRs.

#### Is AVR compatible with the "Green Tariff"?

Some of them. Having a "Green Tariff" assumes an inverter. Use the [inverter-compatible AVR](#which-avr-is-inverter-compatible).

#### How does the AVR work in general?

Think of electricity like water: when the push (voltage) gets weaker, the regulator squeezes the pipe so the push goes back up and more water (current) flows, and when the push is too strong, it relaxes the pipe so less current flows — keeping things safe.

For example, your consumption is 3 kWh. The voltage is 170. The current will be 17.65 (13.64A if 220V, 13A if 230V). Use `I=P/V` to compute.

Due to outages in Ukraine, the voltage often deviates significantly from 220–230V. In this case, your AVR boosts the voltage, which increases current in the line. If many AVRs are operating in the same neighborhood, this extra load can stress and potentially damage the distribution current transformer.

The lower the voltage, the closer you are to hit the limit of your circuit breaker, and the less power you get.

> [!TIP]
> While the AVR solves the problem for your home temporarily, it is strongly recommended to contact your electricity supplier and request an inspection or modernization of their equipment. The collective requests work better.
