# IQ RX Bridge — Beta Releases

> **Formerly known as `malachite-rx-bridge`.** Renamed to reflect the
> broader scope: this app feeds QMAP from any radio whose IQ baseband
> comes out as a USB sound card — Malachite-DSP/DSP2/DSP3, FunCube
> Dongle Pro+, FlexRadio (DAX-IQ → virtual cable), generic IF tap, and
> so on. The legacy `malachite-rx-bridge-0.99.1-*` binaries are still
> available below if you need to roll back, but new installs should
> use the latest `iq-rx-bridge-*-setup.exe`.

Windows installer downloads for the **IQ RX Bridge** — a Qt6 C++
application that pumps stereo IQ from a USB sound card into QMAP for
wideband Q65 / FT8 reception. Sibling to the HackRF / RTL-SDR /
SDRplay / AirSpy native-USB RX bridges, but for radios that expose IQ
on a USB Audio Class device instead of a vendor-specific USB driver.

Author: **Andreas Junge, N6NU** &lt;<n6nu@arrl.net>&gt;.

---

## Latest release — v1.0.0 (first stable)

Download: **[iq-rx-bridge-1.0.0-setup.exe](iq-rx-bridge-1.0.0-setup.exe)**

Promoted out of beta. Sound-card IQ feeder for QMAP wideband Q65,
with optional FunCube Dongle Pro+ V1/V2 USB-HID CAT control. The
FCD path was bench-verified end-to-end against WWV (10 MHz tone)
for frequency calibration; 2 m EME signals exercised the full
gain chain (LNA / LNA enhance / mixer / IF stages 1-3 / IF
filter / bias-T) as designed. The 0.99.x line ends here.

No code changes vs v0.99.5 — v1.0.0 is a label promotion plus a
rebuild against the latest bridge-core.

Supported IQ sources:

| Source | CAT support | Sample rate |
|---|---|---|
| Malachite-DSP / DSP2 | None — operator types freq | 96 / 192 kHz |
| Malachite-DSP3 | (planned) FT-897 emulation, serial CAT | 96 / 192 kHz |
| **FunCube Dongle Pro+ V1/V2** | **USB HID — auto-tune from WSJT-X** | up to 192 kHz |
| FlexRadio + DAX-IQ → virtual cable | None at this layer | 48 / 96 / 192 kHz |
| K3 KXV3A → external mixer → line-in | None | sound-card-dependent |
| SDR Console / SDR# IQ → virtual cable | None | sound-card-dependent |
| Generic IF tap into any sound card | None | sound-card-dependent |

### What's new in v0.99.5 (2026-05-02) — finer FCD gain trim

Three more HID-controlled gain knobs to round out the FCD control
surface:

- **LNA enhance** — 0 / +3 / +6 / +9 dB on top of the LNA on/off
- **IF gain stage 2** — 0 / +3 / +6 / +9 dB after stage 1
- **IF gain stage 3** — 0 / +3 / +6 / +9 dB after stage 2

Total IF gain across stages 1–3 now reaches 39 dB in the bridge
UI. Combined with the LNA / LNA-enhance / mixer paths this matches
the gain coverage of AMSAT-UK's FCD Control app.

### What's new in v0.99.4 (2026-05-02) — FCD gain & filter controls

Settings → "FunCube Dongle Pro+ controls" group exposes the FCD's
HID gain stages and IF filter directly:

- **LNA gain** on/off (~24 dB front-end)
- **Mixer gain** low/high (~12 dB)
- **IF gain stage 1** 0…21 dB in 3 dB steps
- **IF filter** 200 / 300 / 600 / 1500 / 2400 / 2800 / 4500 kHz
- **Bias-T** 4.5 V on SMA centre conductor

Re-applied on every retune (some FCD firmware versions reset gain
stages on band-select swings). No more hopping back to AMSAT-UK's
FCD Control app for everyday tuning.

### What's new in v0.99.3 (2026-05-02) — waterfall span fix

Built-in spectrum / waterfall display now shows the correct frequency
span for the actual IQ rate (192 kHz on FCD Pro+ V2 instead of the
hardcoded 2 MHz that suited HackRF / RTL-SDR).

### What's new in v0.99.2 (2026-05-02) — half-scaled QMAP fix

Sample-rate mismatch between SoundCardDevice and LinradServer was
making QMAP show signals at half their true offset from the dial.
Root cause: SoundCardDevice silently fell back to the device's
preferred format (192 kHz on FCD Pro+ V2) when 96 kHz wasn't
supported, but LinradServer was still configured for 96 kHz.
Critical fix for FCD users — get this version or later.

### What's new in v0.99.1 (2026-05-02) — FCD PPM correction

Software PPM frequency correction for the FCD's TCXO drift.
Critical for EME / weak-signal where the carrier needs to land
within a few Hz of the dial.

### What's new in v0.99.0 (2026-05-02) — first iq-rx-bridge release

Successor to malachite-rx-bridge v0.99.x. Adds USB-HID CAT control
of the FunCube Dongle Pro+ V1/V2 (auto-tune from WSJT-X dial) plus
all the multi-instance support inherited from bridge-core
(`--instance`, Linrad TCP/UDP port spinboxes, namespaced INI).

---

## Legacy: `malachite-rx-bridge`

The pre-rename builds remain available for testers who haven't migrated
yet:

- **[malachite-rx-bridge-0.99.1-setup.exe](malachite-rx-bridge-0.99.1-setup.exe)** — final malachite-branded installer
- **[malachite-rx-bridge-0.99.1-win11.zip](malachite-rx-bridge-0.99.1-win11.zip)** — portable zip variant

The two installers use different AppId GUIDs so they install side-by-side
without conflict. Once you've verified iq-rx-bridge works for your setup,
uninstall the malachite version via Windows Settings → Apps.

---

## Setup with FunCube Dongle Pro+

1. Plug the FCD in. Windows mounts it as a USB Audio Class device
   (`USB Audio CODEC` / `FUNcube Dongle V2.0`) and a USB HID device.
   No driver step needed.
2. Launch the bridge. Settings → "Sound-card IQ input":
   - **Input device** = the FCD
   - **Sample rate** = 192 000 Hz (FCD Pro+ max — best for QMAP)
   - **CAT controller** = "FunCube Dongle Pro+ V1/V2 (USB HID CAT)"
   - Tick **"Follow WSJT-X dial via UDP (port 2237)"**
   - Adjust **"FCD freq correction"** ppm if you've calibrated
3. Apply, **restart the bridge** (CAT controller change takes effect
   on next launch).
4. Open WSJT-X. Settings → Reporting → "Accept UDP requests" on,
   port 2237.
5. Spin the WSJT-X dial → the FCD follows.

## Reporting

Bug reports / decode logs to **<n6nu@arrl.net>**. Please include:
- Radio model (FCD Pro+ V1/V2, Malachite variant, FlexRadio model, etc.)
- Bridge log: relaunch with `iq-rx-bridge.exe --console`, reproduce,
  copy/paste console output
- For QMAP issues: `qmap.ini` + a wideband-waterfall screenshot

## License

Copyright (C) 2026 **Andreas Junge, N6NU**.
Licensed under the **GNU General Public License v3 or later** —
see [`LICENSE`](LICENSE). Bundled third-party components — Qt 6,
FFTW3, SoXr, libhidapi — are documented in
[`THIRD_PARTY_LICENSES.md`](THIRD_PARTY_LICENSES.md).
**No warranty.** Install and run at your own risk.
