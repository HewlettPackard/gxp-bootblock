# gxp2-bootblock
This respository contains the bootblock modules required to run OpenBMC on supported HPE Servers.  These modules will run on HPE Proliant Gen11 Servers that have undergone Transfer of Ownership.  Hewlett Packard Enterprise Silicon Root of Trust technology (see below).  

The bootblock is supplied in binary form.  It consists of essential startup code needed for the HPE BMC system-on-a-chip (SOC), including core functions such as enabling caches and DRAM before transferring control to U-Boot.

## Terms of Use
© Copyright 2020 Hewlett Packard Enterprise Development LP. This software is HPE proprietary and its use is subject to the terms and conditions of the HPE EULA and Software Licensing Terms, which may be amended from time to time (“HPE EULA”). By downloading, copying or using this software you agree to the terms of the HPE EULA. The HPE EULA may be found on the HPE website, currently at www.hpe.com/us/en/software/licensing.html

## Running Open Source Firmware on HPE Servers

### HPE Gen11 "Transfer of Ownership" 
This bootblock is for use with servers that have undergone the Transfer of Ownership process.  The customer public signing key must be stored in the secure element on the server.  If a public key is not present a ASIC specific customer keyblock is fallen back on.  The Transfer of Ownership Process is a secure method of storing customer signing keys and enabling customer firmware to run securely on HPE ProLiant servers.  

### GXP and HPE Silicon Root of Trust
The Hewlett Packard Enterprise Silicon Root of Trust technology guarantees that HPE ProLiant servers will only run specific firmware.  The SOC verifies the hash of an immutable block of initial code before beginning execution.  Because of this, this block of code is matched to the SOC.  This code then verifies an HPE digital signature of the 2nd stage of code, also contained within the gxp-bootblock.

### Determine which iLO ASIC Revision is in a system
When building a firmware image to flash on an HPE server, you need to know which version of the SOC (which fusings for the bootblock) is on the system.  If the wrong bootblock gets flashed, **the system will not be bootable**.  To determine the proper bootblock to use please get in contact with an HPE developer on the [OpenBMC Discord Channel](https://discord.gg/69Km47zH98).

### Open Source Firmware CI
This is an online gateway to run open source firmware on HPE ProLiant Gen11 systems.  See [Open Source Firmware CI system](https://osfci.tech/ci/).

When building images for the OSFCI tool please use the following:

#### RL300_GEN11
GXP2loader-t26x-sgn00.bin 

#### DL325_GEN11 DL385_GEN11
GXP2loader-t277-t280-t285-sgn00.bin

### Firmware Image Layout and Boot Flow
The bootblock is placed at the top-most addresses of the firmware image and runs first before transferring control to the next stage (typically U-Boot) at offset 0x7000 of the image.  After transferring control it provides no further functionality and it is never executed again until the SOC is rebooted.  I/O is limited to a small amount of serial port log output for debug purposes.

```
[HPE GXP SOC --> bootblock (this code)] --> U-Boot --> Linux --> OpenBMC
```

The gxp-bootblock contains two redundant copies and will execute the first valid copy found.  At this time, transfer of control to U-Boot is a simple jump to offset 0x7000 in the firmware image.

## Related Repositories

|||
|---|---|
|HPE's GXP OpenBMC Development Fork|https://github.com/HewlettPackard/openbmc|
|HPE's GXP Linux Development Fork|https://github.com/HewlettPackard/gxp-linux|
|HPE's GXP U-Boot Development Fork|https://github.com/HewlettPackard/gxp-uboot|

These are for rapid development and debug with partners.  Hewlett Packard Enterprise intends to upstream each of these to the upstream repositories including https://github.com/openbmc/meta-hpe.
