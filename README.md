# gxp-bootblock README
This respository contains the bootblock modules required to run OpenBMC on supported HPE servers.  These modules will not run on unmodified HPE Proliant Gen10 Servers because of the Hewlett Packard Enterprise Silicon Root of Trust technology (see below).  

The bootblock is supplied in binary form.  It consists of essential startup code needed for the HPE BMC system-on-a-chip (SOC), including core functions such as enabling caches and DRAM before transferring  control to U-Boot.

## Terms of Use
© Copyright 2020 Hewlett Packard Enterprise Development LP. This software is HPE proprietary and its use is subject to the terms and conditions of the HPE EULA and Software Licensing Terms, which may be amended from time to time (“HPE EULA”). By downloading, copying or using this software you agree to the terms of the HPE EULA. The HPE EULA may be found on the HPE website, currently at www.hpe.com/us/en/software/licensing.html

## Running Open Source Firmware on HPE Servers
-----------------------
### gxp-bootblock-dl360poc.bin
Bootblock `gxp-bootblock-dl360poc.bin` supports these systems:

#### Modified HPE DL360 Gen10 "proof of concept" servers
This is a small number of modified DL360 servers used for demonstrating OpenBMC.  This bootblock does not perform a signature check of U-Boot before executing it.

#### Open Source Firmware CI
This is an online gateway to run open source firmware on one of the DL360 proof of concept systems.  See [Open Source Firmware CI system](https://osfci.tech/ci/).

## GXP and HPE Silicon Root of Trust
-----------------------
The GXP SOC is a variant of the iLO 5 ASIC used on HPE ProLiant servers.  This variant has been modified to run open source firmware, and it will not run the HPE iLO 5 firmware.  Unmodified HPE ProLiant servers will not run any other firmware than iLO 5.

The Hewlett Packard Enterprise Silicon Root of Trust technology guarantees that HPE ProLiant servers will only run specific firmware.  The SOC verifies the hash of an immutable block of initial code before beginning execution.  Because of this, this block of code is matched to the SOC.  This code then verifies an HPE digital signature of the 2nd stage of code, also contained within the gxp-bootblock.

For a high level overview of the Silicon Root of Trust, see [iLO 5 Chip with Silicon Root of Trust Protects Servers from Attacks](https://www.hpe.com/h22228/video-gallery/us/en/700000787/EN/US/23d04aad-d9e0-43c4-bcf5-b79a175fe1ae/ilo-5-chip-with-silicon-root-of-trust-protects-servers-from-attacks/video?lang=en-US).


## Firmware Image Layout and Boot Flow
-----------------------
The bootblock is placed at the top-most addresses of the firmware image and runs first before transferring control to the next stage (typically U-Boot) at offset 0x0 of the image.  After transferring control it provides no further functionality and it is never executed again until the SOC is rebooted.  I/O is limited to a small amount of serial port log output for debug purposes.

```
[HPE GXP SOC --> bootblock (this code)] --> U-Boot --> Linux --> OpenBMC
```

The gxp-bootblock contains two redundant copies and will execute the first valid copy found.  At this time, transfer of control to U-Boot is a simple jump to offset 0x0 in the firmware image.

## Related Repositories

|||
|---|---|
|HPE's GXP OpenBMC Development Fork|https://github.com/HewlettPackard/openbmc|
|HPE's GXP Linux Development Fork|https://github.com/HewlettPackard/gxp-linux|
|HPE's GXP U-Boot Development Fork|https://github.com/HewlettPackard/gxp-uboot|

These are for rapid development and debug with partners.  Hewlett Packard Enterprise intends to upstream each of these to the upstream repositories including https://github.com/openbmc/meta-hpe.
