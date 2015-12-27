# Bluetooth LE Communication Protocol

This is based on the decompiled Android Sphero app. The results
here match
[mcgrews3's previous work](https://github.com/mcgrews3/bb8-driver).

## Name Prefixes

* Ollie: `2B-`
* BB8: `BB-`
* WeBall: `1C-`

## Services

* Radio: 22bb746f-2bb0-7554-2D6F-726568705327
* Robot: 22bb746f-2ba0-7554-2d6f-726568705327

### Radio Service Characteristics

* TX power: 22bb746f-2bb2-7554-2D6F-726568705327
* RSSI: 22BB746F-2BB6-7554-2D6F-726568705327
* Deep sleep: 22BB746F-2bb7-7554-2D6F-726568705327
* Anti DOS: 22bb746f-2bbd-7554-2D6F-726568705327
* Anti DOS timeout: 22bb746f-2bbe-7554-2D6F-726568705327
* Wakeup: 22bb746f-2bbf-7554-2D6F-726568705327

### Robot Service Characteristics

* Control: 22bb746F-2ba1-7554-2D6F-726568705327
* Response: 22bb746F-2ba6-7554-2D6F-726568705327
* Device information: 0000180a-0000-1000-8000-00805f9b34fb
* Model number: 00002A24-0000-1000-8000-00805f9b34fb
* Serial number: 00002A25-0000-1000-8000-00805f9b34fb
* Radio firmware version: 00002A26-0000-1000-8000-00805f9b34fb

## Wakeup Sequence

1. Write "011i3" to Anti DOS
1. Write 0x07 to TX power
1. Write 0x01 to Wakeup

### Anti DOS Timeout

In developer mode, 0x0A (10 seconds?) is written to the Anti DOS timeout.
In normal mode, 0x00 (0 seconds?) is written.

### Deep Sleep

While in the main app, an API command is used to put the robot in deep sleep.
Outside the main app, "011i3" is written to the Deep sleep characteristic.

## Standard Communication

The serial protocol used over Bluetooth RFCONN is tunnelled over the Control
and Response attributes. Writes (of at most 20 bytes) go to Control. Change
notifications received from Response contain read payloads.
