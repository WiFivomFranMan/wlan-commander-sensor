# WLAN Commander Sensor — unified images and documentation

WLAN Commander Sensor has one **unified A/B factory image** for NanoPi Zero2,
Raspberry Pi 4, Raspberry Pi 5, WLAN Pi Pro (CM4) and WLAN Pi Go (CM4).
Radio, display and USB features depend on the installed hardware and the
release's tested coverage. The original Pi 4 R4 display HAT needs an explicit
profile; see the installation guide before flashing.

- [Downloads and current version](https://wlancommander.com/downloads/)
- [Installation, hardware profiles and first-use setup](https://wlancommander.com/guides/unified-sensor-image/)
- [Sensor website guide with screenshots](https://wlancommander.com/guides/sensor-console/)
- [Release notes, signed assets and corresponding sources](https://github.com/WiFivomFranMan/wlan-commander-sensor/releases)
- [Support and troubleshooting](https://wlancommander.com/support/)

The unified image is a **public beta**, not a GA-qualified image. Check the
specific release notes for real hardware results and outstanding checks.
GitHub's “Latest” release may still be an older stable NanoPi image: use the
unified Downloads panel or the `wlan-commander-sensor` entry in
[the updater catalogue](https://wlancommander.com/releases.json).

## Choose the correct artifact

| Artifact | Use |
| --- | --- |
| `wlan-commander-sensor-VERSION-factory.img.xz` | First installation on a card or the Go's exposed eMMC. Erases the target. Data expands on first boot. |
| `wlan-commander-sensor-VERSION-ota.img.xz` | Signed update of an existing `unified-ab-v2` installation. Preserves Data. Do not flash onto blank media or stock WLAN Pi storage. |
| `.img.xz.sig` | Detached signature under the image's pinned WLAN Commander release key. |
| `.img.xz.sha256` | Full compressed-artifact checksum; factory and OTA have different hashes. |
| Source archive and release manifest | Corresponding runtime/build sources, pins, artifact hashes, evidence and limits for that version. |

Fresh unified images require first-use setup. There is **no universal web/SSH
password**. Set your own password; the SSH username is `wlanpi`. On supported
screens, use the device setup QR or the readable one-time code. Supported direct
USB setup can fill the code automatically at `https://198.18.42.1/`.
USB and display exceptions are documented in the installation guide.

The unified image uses a pinned, patched shared **Linux 7.3.0-rc1+** kernel.
It is not the stock Armbian kernel described by the old NanoPi-only page.
Experimental Qualcomm STR/MLO advertisement, FTM and relative spectrum support
have separate limits and are not general Wi-Fi 7 certification claims.

## Repository and documentation roles

This public repository hosts release downloads, this overview, the publishing
runbook and redirects from the original GitHub Pages documents. The maintained
user documentation is at **wlancommander.com**. Release-specific source archives
are attached to releases; this repository's `main` branch is not the image build
tree. Historical board-specific assets and field-note links remain available.

Maintainers: follow [PUBLISHING.md](PUBLISHING.md). Update the website's maintained
Markdown sources, build and validate them, publish immutable signed assets, then
promote the catalogue and verify the actual public bytes. Editing GitHub's README
alone does not publish a guide or offer an update.

## Upstream credit

WLAN Commander integrates software from the [WLAN Pi community](https://github.com/WLAN-Pi)
and other upstream projects. Their authors, licenses and contributions remain
credited. Inclusion does not imply upstream endorsement. Support for inherited
WLAN Pi menus and scripts is limited; the installation guide and
[third-party notices](https://wlancommander.com/notices/) explain the scope.
