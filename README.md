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

## Latest release — v1.1.5

Download: **[iq-rx-bridge-1.1.5-setup.exe](https://github.com/n6nu/iq-rx-bridge/releases/latest/download/iq-rx-bridge-1.1.5-setup.exe)**

What's new in v1.1.5 (2026-05-08) -- Quality release. New Help menu
with User Guide (F1) opening the bundled beta-tester PDF, and
About Qt for LGPLv3 attribution. LGPL compliance polish: full
LGPL license texts now ship in the install dir, and
THIRD_PARTY_LICENSES.md got an explicit source-availability
section. No RF / decode / wire-format changes. Drop-in upgrade.

---
### Previous release — v1.1.4

Download: **[iq-rx-bridge-1.1.4-setup.exe](https://github.com/n6nu/iq-rx-bridge/releases/latest/download/iq-rx-bridge-1.1.4-setup.exe)**

What's new in v1.1.4 (2026-05-07) -- DC blocker on RF-direct receivers
is now user-enableable from Settings. Default state unchanged (OFF
on RF-direct, ON on sound-card sources); previously the checkbox
was locked greyed-out on RF-direct radios since v1.1.3. Drop-in
upgrade.

---
### Previous release — v1.1.3

Download: **[iq-rx-bridge-1.1.3-setup.exe](https://github.com/n6nu/iq-rx-bridge/releases/latest/download/iq-rx-bridge-1.1.3-setup.exe)**

What's new in v1.1.3 (2026-05-06) -- DC blocker default-off for
RF-direct receivers (HackRF / RTL-SDR / SDRplay / Pluto / AirSpy)
since their SDR API already does hardware DC correction. Settings
checkbox is grayed out for those radios. Sound-card-IQ sources
(FunCube etc.) keep DC blocker default-on. Drop-in upgrade.

---
### Previous release — v1.1.2

Download: **[iq-rx-bridge-1.1.2-setup.exe](https://github.com/n6nu/iq-rx-bridge/releases/latest/download/iq-rx-bridge-1.1.2-setup.exe)**

What's new in v1.1.2 (2026-05-05) -- DC blocker for zero-IF
receivers. New "DC blocker (zero-IF spike removal)" checkbox in
Settings (default ON) chews through the LO-leakage spike at the
centre of the on-screen waterfall and the QMAP wideband display.
100 Hz notch, irrelevant for Q65 / FT8 audio. Drop-in upgrade.
### Previous release — v1.1.1

Download: **[iq-rx-bridge-1.1.1-setup.exe](https://github.com/n6nu/iq-rx-bridge/releases/latest/download/iq-rx-bridge-1.1.1-setup.exe)**

What's new in v1.1.1 (2026-05-05) -- the IQ-rate combo now hides
rates above the sound card's actual negotiated sample rate. On a
FunCube Pro+ V2 (192 kHz hardware), 256 kHz is no longer selectable
because asking for it would force the bridge to upsample 192 -> 256
with a fractional ratio, mismatching QMAP's frequency display.
Drop-in upgrade.
### Previous release — v1.1.0

Download: **[iq-rx-bridge-1.1.0-setup.exe](https://github.com/n6nu/iq-rx-bridge/releases/latest/download/iq-rx-bridge-1.1.0-setup.exe)**

What's new in v1.1.0 (2026-05-05) -- UI refresh. Main window now
fixed-size 400x640. Settings moved from a button to a top-level
**Settings** menu (`Ctrl+,`). New **Linrad rate** readout in the
State grid. Settings dialog reflowed side-by-side, with a new
**Linrad IQ rate** combo (defaults to "96 kHz (QMAP Default)").
Drop-in upgrade; 96 kHz wire format unchanged.
### Previous release — v1.0.4 (stable)

Download: **[iq-rx-bridge-1.0.4-setup.exe](iq-rx-bridge-1.0.4-setup.exe)**

Finishes the cosmetic rebrand. **Windows Task Manager → Details**
shows a process's `FileDescription`, not its filename — and that
field was still `"Malachite RX Bridge"` (a leftover from the
`malachite-rx-bridge` era), even after v1.0.3 fixed the GUI
label. Plus `InternalName`, `OriginalFilename`, and
`ProductName`. All four updated to **`IQ RX Bridge`** /
**`iq-rx-bridge`** / **`iq-rx-bridge.exe`**. CompanyName stays
`N6NU`. Right-click → Properties → Details, and Task Manager,
now show the bridge consistently.

Rolls up v1.0.3's GUI label rebrand: default **Hardware label**
is now **"N6NU IQ RX Bridge"** with a one-shot INI migration
that upgrades existing installs from the old `"Malachite SDR"`
default while preserving any user-customised value.

Rolls up the v1.0.2 audio-path fix: **IQ→SSB demodulation
happens inside the bridge** so WSJT-X gets demodulated audio
directly via VB-Cable — no second sound device needed for
FunCube Pro+ / FlexRadio DAX-IQ / generic IF taps. Demod mode
(USB / LSB) follows WSJT-X mode and CAT `\set_mode`. Toggle in
Settings if your radio already provides its own SSB audio.

Plus the v1.0.1 **opt-in rigctld CAT server** on TCP 4540 —
point WSJT-X at *Hamlib NET rigctl, 127.0.0.1:4540* to get
Doppler-corrected dial freq driving the bridge directly.

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

### What's new in v1.0.4 (2026-05-04) — rebrand the Win32 version-resource strings

Cosmetic, finishes the v1.0.3 rebrand. The Windows Task Manager
"Description" column and the Right-click → Properties → Details
strings come from the embedded Win32 version resource, not from
the exe filename. The bridge's resource block still had:

- `FileDescription = "Malachite RX Bridge"`
- `InternalName = "malachite-rx-bridge"`
- `OriginalFilename = "malachite-rx-bridge.exe"`
- `ProductName = "Malachite RX Bridge"`

All four updated to `IQ RX Bridge` / `iq-rx-bridge` /
`iq-rx-bridge.exe`. `CompanyName` stays `N6NU`. INI compatible
with v1.0.3.

### What's new in v1.0.3 (2026-05-04) — drop the Malachite-era default label

Cosmetic only. The **Hardware label** default (shown on the GUI
status panel + in the QMAP / Linrad header) was `"Malachite SDR"`
— a leftover from when this app was called `malachite-rx-bridge`.
Confusing for FunCube / FlexRadio / IF-tap users. Now defaults to
`"N6NU IQ RX Bridge"`.

A one-shot INI migration runs on first launch: if your existing
`soundcard/hw_label` is still the old default, it's rewritten.
Any user-customised label (e.g. `"FunCube Pro+"`) is preserved
untouched. INI compatible with v1.0.2.

### What's new in v1.0.2 (2026-05-04) — IQ→SSB demod inside the bridge

The bridge now demodulates the IQ stream and pushes 48 kHz mono
SSB audio to the configured RX audio device (typically VB-Cable
Line 1) so WSJT-X can decode FT8/FT4/JT9 directly. Closes a gap
discovered during FunCube Pro+ bench-test (v1.0.1 had no audio
output, assuming the radio produced its own demodulated audio —
true for Malachite-DSP, false for FCD).

- New Settings checkbox: **"Demodulate IQ → audio (feed VB-Cable
  for WSJT-X)"** — default ON.
- New CLI flags `--no-demod` (Malachite users with their own
  audio device) and `--rx-device <name>` (audio output picker).
- Demod mode auto-follows WSJT-X mode (USB / LSB / PKTUSB / PKTLSB).

### What's new in v1.0.1 (2026-05-04) — opt-in rigctld CAT server

Adds the same opt-in CAT pattern as the rest of the family.
**This was a transitional internal release — v1.0.2 supersedes it
end-to-end and includes everything below**:

- Rigctld TCP server on port **4540**, default OFF. Toggle in
  Settings → CAT server (or `--cat`). Restart bridge.
- WSJT-X with `Rig = Hamlib NET rigctl` at `127.0.0.1:4540`
  drives Doppler-corrected dial freq directly into the bridge.
- Auto-detect UDP mute when a CAT client is connected.
- Live source indicator in window title.
- Note: the Settings panel now has TWO "CAT" rows — the existing
  **CAT controller** (radio-side: FunCube HID, tunes the dongle)
  and the new **CAT server** (WSJT-X-side: rigctld TCP). They
  cooperate: WSJT-X → CAT server → bridge → CAT controller → FCD.

### What's new in v1.0.0 (2026-05-02) — first stable

Promoted out of beta. Sound-card IQ feeder for QMAP wideband Q65,
with optional FunCube Dongle Pro+ V1/V2 USB-HID CAT control. The
FCD path was bench-verified end-to-end against WWV (10 MHz tone)
for frequency calibration; 2 m EME signals exercised the full
gain chain (LNA / LNA enhance / mixer / IF stages 1-3 / IF
filter / bias-T) as designed. No code changes vs v0.99.5 — label
promotion plus a rebuild against the latest bridge-core.

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
