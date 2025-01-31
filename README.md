# PowerCoop
A Power Coop is like a Power Bank but less suckful

# What is a power bank

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

# What is a Power Coop

My vision for a power bank that does not suck

* Fast charging from USB power delivery, and raw DC
* Battery and cell health management (control over charge/discharge levels and rates, cell balancing)
* Multiple independent output channels, each with voltage and current measurement
* Programmable current limiting

Conceptually (per [JPM](https://aus.social/@jpm))  

```
a battery bank with a bunch of high-power outputs
that all have power displays, also displaying
over Bluetooth, so you can run a phone, a laptop,
a soldering iron, a hotplate, and a couple of
devices under test from it.
```

# 6P process

## Problem - what do users want/need

See https://github.com/unixbigot/PowerCoop/issues

## Proposal - how are we gonna solve the problem

## Players - What partial solutions are out there

I (kit, @unixbigot) have or have ordered all of these:

* slightly less sucky power banks
  * https://aliexpress.com/item/1005004645663172.html
  * https://aliexpress.com/item/1005007295821577.html
  * https://aliexpress.com/item/1005006988280869.html
* Off the shelf battery management
  * https://aliexpress.com/item/1005006071626722.html
* Microcontroller with PD support
  * https://aliexpress.com/item/1005005793197807.html
  * https://aliexpress.com/item/1005005793197807.html
* Various DC<->PD modules
  *  https://aliexpress.com/item/1005006463687480.html
  *  https://aliexpress.com/item/1005006113132068.html
  *  https://aliexpress.com/item/1005006738365927.html
  *  https://aliexpress.com/item/1005006572686976.html

## Proof of concept - Validate feasibility, select Parts

## Prototype - Iterate a lot

## Product - deliver on promises
