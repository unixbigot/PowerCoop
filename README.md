# PowerCoop

A Power Coop is like a Power Bank but less suckful

## What is a power bank

A power bank is a battery of rechargeable electrical storage cells and some electronics to charge
and discharge.   Almost always USB connectors are used for charging and discharging.

Some have lights or small displays to report the State of Charge (SoC) and possibly the rate of charge/discharge.

They. All.  Suck.   

* Power banks lie egregiously about their capacity
* Power banks ruin cells through sloppy charge/discharge management
* Power banks do not cope with aging cells
* Power banks do not give accurate predictions of available runtime
* Power banks cannot be monitored to tell devices about their capacity and state
* Power banks are unfixable
* Power banks are typically closely coupled to one cell size and/or count

## What is a Power Coop

My vision for a power bank that does not suck

* Fast charging from USB power delivery, and raw DC
* Battery and cell health management (control over charge/discharge levels and rates, cell balancing)
* Multiple independent output channels, each with voltage and current measurement
* Programmable current limiting

Conceptually (per [JPM](https://aus.social/@jpm))  

```text
a battery bank with a bunch of high-power outputs
that all have power displays, also displaying
over Bluetooth, so you can run a phone, a laptop,
a soldering iron, a hotplate, and a couple of
devices under test from it.
```

@voltagex needs a power bank that can wirelessly report its state of charge, in approximately the same size as commercially-available 10,000mAh power banks.

@jpm wants a power bank that you don't need to screw around with, just plug in a USB-C cable and receive power. It needs to be a UPS for a RasPi.

## 6P process

### Problem - what do users want/need

See <https://github.com/unixbigot/PowerCoop/issues>

#### Must Have

* A battery
* USB-C output capable of providing 100W of USB-C PPS power
* USB-C charging at up to 100W
* Display
  * Per-port voltage and current
  * State of charge
  * Remote display via wireless link

#### Should Have

* Support different battery chemistries and number of cells
  * Li-I, LiFePo4, Lead-Acid
* Advanced battery controls
  * Charge limit (eg 80% limit) to prolong service life of Li-I cells
  * Cell balancing
* Advanced output port controls
  * Fixed 5V/3A output for USB-C devices that have broken USB-C CC implementations (eg Raspberry Pi 4)
  * Prevent port turning off on light load
* Modular
  * Battery board - battery charge controller, tells system board how much power is available
  * System board - main display, monitors available power and distributes among ports
  * Port board - receives allowable power level from system, per-port configuration and display
* Only USB-C ports, which reduces design complexity and BOM

#### Nice To Have

* Port board variants with configurable DC input/output or USB-A output
* Any-port charging - I just want to plug the charger in anywhere and it charges
* Able to use power tool battery packs
  * Effectively able to CHARGE power tool battery packs from USB-C!
* Power bank friend on the screen

### Proposal - how are we gonna solve the problem

We gonna make our own power bank that doesn't suck. Hopefully it won't be too much more expensive than power banks that do suck.

### Players - What partial solutions are out there

I (kit, @unixbigot) have or have ordered all of these:

* slightly less sucky power banks
  * <https://aliexpress.com/item/1005004645663172.html>
  * <https://aliexpress.com/item/1005007295821577.html>
  * <https://aliexpress.com/item/1005006988280869.html>
* Off the shelf battery management
  * <https://aliexpress.com/item/1005006071626722.html>
* Microcontroller with PD support
  * <https://aliexpress.com/item/1005005793197807.html>
  * <https://aliexpress.com/item/1005005793197807.html>
* Various DC<->PD modules
  * <https://aliexpress.com/item/1005006463687480.html>
  * <https://aliexpress.com/item/1005006113132068.html>
  * <https://aliexpress.com/item/1005006738365927.html>
  * <https://aliexpress.com/item/1005006572686976.html>

@jpm also has a bunch of similar things, including the CH32X035 dev board and power banks of various suckage.

### Proof of concept - Validate feasibility, select Parts

Splitting the PowerCoop into parts allows us to develop and test different pieces in parallel.  There are 4 main parts to the PowerCoop - the system control, the (main) display, the power ports, and the battery control.

#### System Control

Use the display control SOC? Or should we have a dedicated processor for system (power status and allocation) functions?

#### Main Display

Probably [nRF52840](https://www.nordicsemi.com/Products/nRF52840), maybe other Bluetooth-enabled SOC. TBD what the UI screen/buttons/knobs/etc should be, definitely something that @voltagex would have opinions on.

#### Power Ports

Likely [CH32X035](https://www.wch-ic.com/products/CH32X035.html) as the brains and [TPS55288](https://www.ti.com/product/TPS55288) for the power delivery. TBD if each port should have a separate screen and control.

#### Battery Control

Maybe [LTC4015](https://www.analog.com/en/products/ltc4015.html) as a single unified charge controller, other possibility is to use a cheap SOC (CH32V003?) that abstracts the battery interface so each battery board can have something new and spicy.

### Prototype - Iterate a lot

### Product - deliver on promises
