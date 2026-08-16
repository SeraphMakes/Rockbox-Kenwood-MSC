# Rockbox -> Kenwood USB MSC Compatibility
## v0.1 Experimental

This is an experimental Rockbox build that has been successfully tested
with an Apple iPod Video 5.5G connected by USB to a Kenwood DMX958XR.

The Kenwood recognizes the Rockbox iPod as USB Mass Storage, browses the
music library using the Kenwood interface, and plays music directly from
the iPod storage.

## Confirmed test hardware

- Apple iPod Video 5.5G
- Rockbox
- iFlash storage, approximately 500 GB
- FAT32
- Kenwood Excelon Reference DMX958XR
- Wired USB connection

## Confirmed result

Before the final storage-presentation change, the Kenwood repeatedly
reported:

    Read Error

Playback sometimes recovered and sometimes failed.

With this exact release:

- First connection: clean playback with no Read Error
- Multiple successive reconnects: clean
- Full reboot of both iPod and Kenwood: clean
- More than 10 additional unplug/replug tests: clean

No Read Error was observed during the final stability testing.

## What appears to have fixed it

The major breakthrough was changing the USB-host-visible MBR so the
Kenwood sees a simple flash-drive-style layout containing exactly one
FAT32 partition.

The iPod's physical partition table is NOT rewritten.

Rockbox keeps the iPod's native storage geometry internally while the USB
Mass Storage layer translates what the Kenwood sees.

This build also contains earlier experimental USB/SCSI compatibility work,
so v0.1 should be considered a proven working build rather than a minimal
final patch.

## Current limitation

While USB Mass Storage is active, the normal Rockbox interface is not
available. Music browsing and track selection are performed from the
Kenwood.

## Warning

Do NOT attempt to convert the physical iPod disk itself to the translated
512-byte geometry used by the USB compatibility layer.

During development, physically rewriting that geometry caused Rockbox
mount failure and required recovery from disk backups.

The working solution translates the USB-host-visible data only.

## Status

Experimental / proof-of-concept.

Only the exact hardware combination listed above has been confirmed so far.
Reports from other Kenwood and Rockbox/iPod combinations are welcome.
