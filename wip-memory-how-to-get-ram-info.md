# How to Get System RAM Modules Information

This note describes how to create mount unit that is able to mount specified filesystem.

## Initial Cofig

First, try to load `at24` or `eeprom` or `ee1004`. Also try to load `i2c-i801` or `i2c-amd-mp2-pci`.

    $ sudo modprobe eeprom

Now check info about DIMMs:

    $ decode-dimms

If not working, load other module:

    $ sudo modprobe -r eeprom
    $ sudo modprobe ee1004

## Find SMBus Adapter and Memory Module Address

Search for all SMBus adapters available in the system:

    $ i2cdetect -l

The output will look like this:

    i2c-3   i2c         i915 gmbus dpd                          I2C adapter
    i2c-1   i2c         i915 gmbus dpc                          I2C adapter
    i2c-4   i2c         DPDDC-E                                 I2C adapter
    i2c-2   i2c         i915 gmbus dpb                          I2C adapter
    i2c-0   smbus       SMBus I801 adapter at f040              SMBus adapter

Display information about SMBus adapter `i2c-0`:

    $ i2cdetect -y 0

The output will look like this:

         0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
    00:          -- -- -- -- -- 08 -- -- -- -- -- -- --
    10: -- -- -- -- 14 15 -- -- -- -- -- -- -- -- -- --
    20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
    30: 30 31 -- -- 34 35 -- -- -- -- -- -- -- -- -- --
    40: 40 -- -- -- 44 -- -- -- -- -- -- -- -- -- -- --
    50: 50 51 52 53 -- -- -- -- -- -- -- -- -- -- -- --
    60: -- -- -- -- -- -- -- -- -- -- 6a -- -- -- -- --
    70: -- -- -- -- -- -- -- --

SPD for each memory device will be in range of 0x50 to 0x57.

## Add Memory Module Device

Found first memory device on address 0x50. Create new device:

    $ echo ee1004 0x50 | sudo tee /sys/bus/i2c/devices/i2c-0/new-device

This is better way of running complex command as root:

    $ sudo -s "echo 'ee1004 0x50' > /sys/bus/i2c/devices/i2c-0/new-device"

Remove memory device:

    $ sudo -s "echo 0x50 > /sys/bus/i2c/devices/i2c-0/delete-device"

    $ decode-dimms
    $ decode-dimms --side-by-side

