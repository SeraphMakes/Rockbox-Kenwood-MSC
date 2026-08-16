# Technical Notes

## Problem

The iPod Video storage implementation uses 2048-byte virtual sectors
internally.

The Kenwood compatibility layer presents USB Mass Storage using 512-byte
host sectors.

The working build translates the relevant disk geometry only in the RAM
copy sent to the USB host.

## Tested host-visible geometry

USB MSC block size:

    512 bytes

Native virtual sector size:

    2048 bytes

Multiplier:

    4

FAT32 partition start:

    257040

FAT32 partition sector count:

    977109996

Host-visible FAT32 BPB:

    bytes/sector       512
    sectors/cluster    32
    reserved sectors   128
    hidden sectors     257040
    FSInfo sector      4
    backup boot sector 24

## MBR breakthrough

Earlier versions translated the original iPod-style partition table.

The successful build instead constructs a host-visible MBR containing:

    MBR slot 0: FAT32 partition
    MBR slot 1: zero
    MBR slot 2: zero
    MBR slot 3: zero

The existing FAT32 partition is located before the table is cleared, its
LBA start/count are scaled for the 512-byte USB representation, and that
single partition entry is copied into slot 0.

This modifies only the outgoing USB-sector buffer.

The physical iPod/iFlash MBR remains unchanged.

## Earlier timing behavior

Before the simplified MBR, small delays inserted after an early READ(10)
changed whether the Kenwood recovered after Read Error.

Some delays played and some did not, suggesting a USB scheduling/phase
interaction.

After the single-partition MBR change, repeated clean connections occurred
across multiple pacing values, including values that had previously failed.

Current interpretation:

The timing behavior was probably affecting recovery from the mount problem
rather than causing the underlying Read Error.

## Other code present in v0.1

The build still contains development instrumentation and earlier
compatibility changes including:

- Generic USB VID/PID
- USB Mass Storage modifications
- 512-byte host-sector translation
- FAT32 BPB translation
- MBR translation
- Kenwood write handling / RAM shadow behavior
- SCSI diagnostics
- USB diagnostics
- pacing experiments
- USB frame/microframe diagnostics
- exclusive-storage/TUR experiments

Not all of these have been proven necessary.

A future cleaned version should remove experimental components one at a
time while retaining verified compatibility.
