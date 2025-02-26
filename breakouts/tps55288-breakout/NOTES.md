# TPS55288 breakout notes

Vin range: 2.7-36V
Vout range: 0.8-22V
L nominal: 4.7uH
Cin nominal: 22uF
Cout nominal: 100uF

## USB-C PPS

voltage regulation tolerance +/- 5%
voltage step 20mV, minimum 3.3V
current regulation tolerance +/- 150mA when I <= 3A, +/- 5% when I > 3A
current step 50mA, minimum 1A

Max current depends on USB-C connector, most are rated for 5A. G-Switch GT-USB-7010ES01 ([LCSC C7275158](https://www.lcsc.com/product-detail/USB-Connectors_G-Switch-GT-USB-7010ES01_C7275158.html)) is rated for 10A and uses same footprint as GT-USB-7010ASV. Programable Ilim up to 6.35A only?

Useful USB-C PD and PPS info: <https://www.usb.org/sites/default/files/D2T2-1%20-%20USB%20Power%20Delivery.pdf>

Boost mode = 100W from 12V.

## Output power - target 100W

Input voltage: 6V (2S discharged Li-I) - 27.2V (2 car batteries fully-charged)
Output voltage: 3.3V (min PPS supported voltage) - 21V (max 20V PPS + extra 1V for cable drop)
Output current limit: 5A
Output voltage ripple: +/- 165mV max (3.3V +/- 5%)

## Application Implementation

### VCC Power Supply

7.3.1 "To minimize the power dissipation of the internal LDO when both input voltage and output voltage are high, and external 5V power source can be applied at the VCC pin to supply the TPS55288. The external 5V power supply must have at least 100mA output current capability and must be within the 4.75V to 5.5V regulation range. To use an external power supply for Vcc, a resistor with proper resistance must be connected to the MODE pin".

Probably should use this with a small fixed buck converter (to power the CH32X035 MCU as well), as power input will usually be around 12-24V, and output can be 20V. In the breakout, don't use this, but also provide an external 5V power input in case both Vin and Vout are below 5.9V.

### Switching Frequency

7.3.7: "The switching frequency is between 200kHz and 2.2 MHz set by placing a resistor at the FSW pin"

8.1: "To reduce the switching power loss in high power conditions, it is recommended to set the switching frequency below 500kHz"

8.2.2.1: "The switching frequency of the TPS55288 is set by a resistor at the FSW pin. Use Equation 3 to calculate the resistance for the desired frequency"

Equation 3: Fsw(MHz) = (1000) / (0.05 * Rfsw + 20)

|R(Fsw) Ω|F(sw) MHz|
|-|-|
|9.1k (E24)|2.11|
|10k (E12)|1.92|
|22k (E12)|0.893|
|33k (E12)|0.599|
|39k (E12)|0.508|
|47k (E12)|0.422|
|51k (E24)|0.389|
|56k (E12)|0.355|

We probably should use 47k resistor at FSW. Breakout board will use a PTH resistor to allow easy swapping.

7.3.7: "For noise-sensitive applications, the TPS55288 can be synchronized to an external clock signal applied to the DITH/SYNC pin. ... The external clock frequency at the DITH/SYNC pin must have lower than 0.4-V low level voltage and must be within ±30% of the corresponding frequency set by the resistor."

7.3.8: "The TPS55288 provides an optional switching frequency dithering that is enabled by connecting a capacitor from the DITH/SYNC pin to ground. ... Connecting the DITH/SYNC pin below 0.4 V or above 1.2 V disables switching frequency dithering. The dithering function also is disabled when an external synchronous clock is used."

7.3.8: "For the dithering circuit to effectively reduce peak EMI, the modulation rate normally is below 1 kHz. Equation 4 calculates the capacitance required to set the modulation frequency"

Equation 4: Cdith(F) = (1) / (2.8 \* R(Fsw) \* F(mod))

|C(dith) F|R(fsw) Ω|F(mod) Hz|
|-|-|-|
|8.1n|47k|938|
|10n|47k|760|

### Inductor Selection

Assuming R(fsw) = 47k.

8.2.2.3: "There are three important inductor specifications: inductance, saturation current, and DC resistance. The TPS55288 is designed to work with inductor values between 1 μH and 10 μH. The inductor selection is based on consideration of both buck and boost modes of operation."

Equation 9 (buck mode): L = ((Vin(max) - Vout) \* Vout) / (ΔI(l(p-p)) \* F(sw) \* Vin(max))

Where:

* Vin(max) is the maximum input voltage (36V or design limit)
* Vout is the output voltage (3.3 to 22V or design limit)
* ΔI(l(p-p)) is the peak to peak ripple current of the inductor
* F(sw) is the switching frequency

Assuming V(in) = 36V, V(out) = 18V:

|L (uH)|ΔI(l(p-p)) A|
|-|-|
|1|21.33|
|1.2|17.78|
|1.5|14.22|
|1.8|11.85|
|2.2|9.7|
|2.7|7.9|
|3.3|6.46|
|3.9|5.47|
|3.76 (4.7uH min)|5.66|
|4.7|4.54|
|5.64 (4.7uH max)|3.77|
|5.6|3.81|
|6.8|3.14|
|8.2|2.6|
|10|2.13|

Also need to account for 20% tolerance which results in an inductor somewhere between 3.76uH and 5.64uH.

Equation 10 (boost mode): L = (Vin \* (Vout(max) - Vin)) / (ΔI(l(p-p)) \* F(sw) \* Vout(max))

Where:

* Vout(max) is the maximum output voltage (22V or design limit)
* Vin is the input voltage (2.7V to 36V or design limit)
* ΔI(l(p-p)) is the peak to peak ripple current of the inductor
* F(sw) is the switching frequency

Assuming V(in) = 11V, V(out) = 22V:

|L (uH)|ΔI(l(p-p)) A|
|-|-|
|1|13.04|
|1.2|10.86|
|1.5|8.69|
|1.8|7.24|
|2.2|5.93|
|2.7|4.83|
|3.3|3.95|
|3.9|3.34|
|3.76 (4.7uH min)|3.47|
|4.7|2.77|
|5.64 (4.7uH max)|2.31|
|5.6|2.33|
|6.8|1.92|
|8.2|1.59|
|10|1.3|

Also need to account for 20% tolerance which results in an inductor somewhere between 3.76uH and 5.64uH.

Equation 11 (boost mode): I(dc) = (Vout \* Iout) / (Vin \* efficency)

Where:

* Vout is the output voltage (22V or design limit)
* Iout is the output current (5A or design limit)
* Vin is the input voltage (2.7V or design limit)
* efficency is the percentage conversion efficency of the regulator in the range of 0 to 1

Assuming V(out) = 22V, I(out) = 5A, efficency = 90%

|V(in)|I(dc)|
|-|-|
|2.7|45.27|
|3|40.74|
|4|30.56|
|5|24.44|
|6|20.37|
|7|17.46|
|8|15.28|
|9|13.58|
|10|12.22|
|11|11.11|
|12|10.19|
|15|8.15|
|20|6.11|

Equation 12: algebraic transformation of equation 10 to solve for ΔI(l(p-p))

Equation 13: Ipeak = Idc + (ΔI(l(p-p)) / 2)

Assuming worst-case of boost mode, V(in) = 7V, V(out) = 22V, I(out) = 5A, L = 4.7uH - 20%: I(peak) = 19A

Inductor I(sat) must be greater than the calculated I(peak). The biggest contributor to inductor current is boost-mode conversion from Vin(min) to Vout(max). Running the chip's minimum input of 2.7V to maximum output of 22V requires around 45A of input current! We should probably design around a limit of 20A, which specifying a minimum input voltage of 7V will achieve.  Specifying a minimum 7V rules out any 2S Lithium battery configurations, but still permits 3S configurations for Lithium chemistries at minimum discharge cutoff voltage.  A 4.7uH inductor as suggested in the datasheet will work fine, and a 6.8uH inductor will drop the ripple current at the expense of slower transient response.  As the switching frequency increases, the ripple current also decreases, however switching losses increase. A suitable inductor would be a [Coilcraft XAL1510-472](https://www.lcsc.com/product-detail/Power-Inductors_Coilcraft-XAL1510-472MED_C3911452.html), [YJYCOIN YSPIT1510A-4R7M](https://www.lcsc.com/product-detail/Power-Inductors_YJYCOIN-YSPIT1510A-4R7M_C19188711.html), or similar.

The inductor current calculation also informs the current capacity of the external MOSFETs for boost mode.

### Input Capacitor

### Output Capacitor
