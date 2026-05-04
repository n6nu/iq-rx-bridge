# IQ RX Bridge — Release Notes

## v1.0.3 — drop the Malachite-era default label (2026-05-04)

Cosmetic-only change. The default value of the **Hardware label**
(shown on the GUI status panel + in the QMAP / Linrad header) was
"Malachite SDR" — a leftover from when this app was called
`malachite-rx-bridge`. Confusing for FunCube / FlexRadio / generic
IF-tap users.

- Default is now **"N6NU IQ RX Bridge"**.
- One-shot INI migration on launch: if your existing
  `soundcard/hw_label` is still the literal string `"Malachite
  SDR"`, the bridge rewrites it to the new default. Any
  user-customised label (e.g. "FunCube Pro+", "Malachite-DSP2") is
  preserved untouched.
- Tooltip updated to suggest naming the IQ source.

INI compatible with v1.0.2. Drop-in upgrade.

## v1.0.2 — IQ→SSB demod path for IQ-only sources (FunCube, IF tap) (2026-05-04)

Closes a gap exposed during FunCube Dongle Pro+ bench-test: the FCD
streams IQ only, with no separate demodulated-audio sound device. The
bridge previously had no audio output path (assumed the radio did its
own demod, true for Malachite-DSP, false for FCD), so WSJT-X had no
audio to decode.

- New SSB demod stage: same `SsbDemodulator` (Hilbert phaser, USB/LSB
  selectable) as `hackrf-rx-bridge` / `rtlsdr-rx-bridge`, fed the int16
  IQ from the sound card at the actual negotiated rate, output 48 kHz
  mono audio to the existing audio bridge.
- New Settings checkbox: **"Demodulate IQ → audio (feed VB-Cable for
  WSJT-X)"** — INI key `soundcard/demod_to_audio`, default ON.
- New CLI flag: `--no-demod` for Malachite users who already have a
  separate demodulated-audio device.
- New CLI flag: `--rx-device <name>` to pick the audio output device
  (defaults to VB-Cable Line 1 / system default).
- Demod mode follows WSJT-X UDP `modeChanged` and CAT `\set_mode`
  (USB/LSB/PKT*). On startup the saved `radio/mode` is applied.
- Live device swap: changing the audio output device in Settings
  hot-swaps without bridge restart.

INI compatible with v1.0.1. Drop-in upgrade. **For FCD users:** in
WSJT-X set Sound input → VB-Cable Line 1 (or whichever device the
bridge is feeding) and you should see signals decode immediately.

## v1.0.1 — opt-in CAT server for WSJT-X Doppler tracking (2026-05-04)

Adds the same opt-in CAT pattern as the rest of the family. With
WSJT-X **Rig = Hamlib NET rigctl** at `127.0.0.1:4540`, Doppler
tracking commands corrected freq directly to the bridge.

- Rigctld TCP server on port **4540**, default OFF.
- Toggle in Settings → CAT server (or `--cat`). Restart bridge.
- Auto-detect UDP mute when a CAT client is connected.
- Live source indicator in window title.
- PTT signal intentionally unwired — this bridge has no TX path
  and the IQ feed continues during transmit.
- Note: the Settings panel now has TWO "CAT" rows — the existing
  **CAT controller** (radio-side: FunCube HID, tunes the dongle)
  and the new **CAT server** (WSJT-X-side: rigctld TCP, receives
  Doppler-corrected freq). They cooperate: WSJT-X → CAT server →
  bridge → CAT controller → FCD.

INI compatible with v1.0.0. Drop-in upgrade.

## v1.0.0 — stable (2026-05-02)

Promoted out of beta. Sound-card IQ feeder for QMAP wideband Q65,
with optional FunCube Dongle Pro+ V1/V2 USB-HID CAT control. The
FCD path was bench-verified end-to-end against WWV (10 MHz tone)
for frequency calibration; 2 m EME signals exercise the full gain
chain (LNA / LNA enhance / mixer / IF stages 1-3 / IF filter /
bias-T) as designed. The 0.99.x line ends here; future development
opens a 1.x series.

No code changes vs v0.99.5 — v1.0.0 is a label promotion plus a
rebuild against the latest bridge-core (waterfall span match).

Drop-in upgrade from v0.99.5.

## v0.99.5 — finer FCD gain trim (2026-05-02)

Adds three more HID-controlled gain knobs to the FunCube Dongle Pro+
controls panel, for finer EME gain tuning between the LNA on/off and
the IF stage 1 step:

- **LNA enhance** — 0 / +3 / +6 / +9 dB (HID command 111). Sits on
  top of the main LNA on/off; default 0. Useful when the main LNA
  bit is on but you want a touch more front-end gain than the bare
  on/off provides.
- **IF gain stage 2** — 0 / +3 / +6 / +9 dB (HID command 120).
  Stacked after stage 1; default 0. Reach for this when stage 1 is
  already at +21 dB and you still need more gain.
- **IF gain stage 3** — 0 / +3 / +6 / +9 dB (HID command 121).
  Stacked after stage 2; default 0.

Maximum total IF gain across stages 1–3 is now 21 + 9 + 9 = 39 dB
in the bridge UI (firmware can do more via stages 4–6, not currently
exposed). Combined with the LNA / LNA-enhance / mixer paths, this
matches the gain coverage of AMSAT-UK's FCD Control app.

INI keys: `funcube/lna_enhance`, `funcube/if_gain2`, `funcube/if_gain3`.
Default values match the FCD's "as-shipped" state, so existing
v0.99.4 INIs come up unchanged after upgrade.

reapplyGains() now pushes 8 HID round-trips per retune (was 5);
still well under 50 ms total round-trip time, no perceptible delay.

Drop-in upgrade from v0.99.4.

## v0.99.4 — FCD gain & filter controls (2026-05-02)

Surfaces the FunCube Dongle Pro+ gain and filter HID commands in
the Settings dialog. Saves a trip to the AMSAT-UK FCD Control app
for everyday use, and the bridge re-applies them on every retune
(some FCD firmware versions silently reset gain stages when the
band-select filter swings on a freq change).

New **Settings → "FunCube Dongle Pro+ controls"** group, visible
only when CAT controller = "FunCube":

- **LNA gain** (on/off) — front-end ~24 dB. Default ON. INI key
  `funcube/lna_gain`. HID command 110.
- **Mixer gain** (low/high) — ~12 dB. Default high. INI key
  `funcube/mixer_gain`. HID command 114.
- **IF gain stage 1** (0…21 dB in 3 dB steps) — primary post-mixer
  gain knob. Default 12 dB. INI key `funcube/if_gain1`. HID
  command 117. For EME with a good external LNA, start at 6–9 dB
  and only go higher if the QMAP noise floor is too low to see
  weak signals. Higher values risk IF compression on strong
  in-band signals.
- **IF filter bandwidth** — 200 / 300 / 600 / 1500 / 2400 / 2800
  / 4500 kHz. Default 1500 kHz. INI key `funcube/if_filter`. HID
  command 122. Narrowest that still contains the QMAP window plus
  margin is best — rejects out-of-band signals that would push
  the AGC around.
- **Bias-T** (4.5 V on SMA centre) — for an external preamp /
  converter on the antenna line. Default OFF. INI key
  `funcube/bias_tee`. HID command 126.

The bridge calls `reapplyGains()` on FCD attach (`open()`) and
after every successful `setFrequencyHz()` round-trip, so the
operator's chosen gain doesn't quietly revert each time WSJT-X
dials. Cheap — five HID round-trips per retune.

Live Settings → Apply: gain / filter changes take effect on the
next dial event without a bridge restart (same INI re-read
pattern as PPM).

Drop-in upgrade from v0.99.3.

## v0.99.3 — waterfall span matches actual sample rate (2026-05-02)

The built-in spectrum / waterfall display was showing a hardcoded
2 MHz span (suitable for HackRF / RTL-SDR at 2 Msps) regardless of
the IQ source's actual rate. On a FunCube Dongle Pro+ V2 streaming
192 kHz, the displayed centre / edge labels were wildly wrong — the
edges showed 1 MHz off from centre when in reality only 96 kHz off.

**Fix**: `FftEngine` now exposes `sampleRateHz()`. `RxMainWindow`
calls `WaterfallWidget::setSpanHz(fft_->sampleRateHz())` after
constructing the waterfall, so the displayed span matches the
actual IQ rate (192 kHz, 96 kHz, 2 Msps, 2.5 Msps — whatever the
device is running). bridge-core change — every sibling RX bridge
(HackRF / RTL-SDR / SDRplay / AirSpy) inherits the fix at its next
version bump; on the SDRplay/AirSpy paths this also corrects the
display when those run at non-2-Msps rates.

Drop-in upgrade from v0.99.2.

## v0.99.2 — fix QMAP half-scaled freq display (2026-05-02)

Tester report on FunCube Dongle Pro+ V2: a sig-gen fixed at
144.400 MHz showed up at the **midpoint** between the bridge dial
and the sig-gen freq, not at its true freq.

| Dial | Carrier shown in QMAP | Δdial | Δapparent |
|---|---|---|---|
| 144.400 | 144.400 | 0 | 0 |
| 144.390 | 144.395 | −10 kHz | **−5 kHz** |
| 144.380 | 144.390 | −20 kHz | **−10 kHz** |
| 144.360 | 144.380 | −40 kHz | **−20 kHz** |

Apparent shift is consistently half the dial shift. Root cause:
**sample-rate mismatch between the sound-card stream and the
LinradServer header.** The FCD Pro+ V2 outputs IQ at 192 kHz; if
Settings → "Sample rate" was 96 kHz, `SoundCardDevice::open()`
quietly fell back to the device's preferred 192 kHz format, but
`LinradServer` had already been constructed with 96 kHz as the
input rate. QMAP read the (correct) baseband bin position from the
192 kHz IQ stream but used the (wrong) 96 kHz header to label it,
giving a 2:1 frequency-axis scale error.

The bug was inherited from `malachite-rx-bridge` v0.99.x — same
fallback path; just nobody had caught it before because the
Malachite-DSP variant the early testers used happened to honour
the requested rate without falling back.

**Fix**: `SoundCardDevice::actualSampleRate()` accessor added.
`main.cpp` queries the actual rate after `open()` and uses THAT
value to construct `LinradServer` + `FftEngine`. A new log line:

```
[SampleRate] requested 96000 Hz but device opened at 192000 Hz;
using 192000 Hz for LinradServer + FFT (QMAP-correct).
```

flags the divergence at startup. After this fix, the carrier lands
at its true freq regardless of which sample rate the device falls
back to.

Drop-in upgrade from v0.99.1.

## v0.99.1 — FCD PPM correction (2026-05-02)

Adds software PPM frequency correction for the FunCube Dongle Pro+
TCXO. **Critical for EME / weak-signal work** where the carrier needs
to land within a few Hz of the dial.

- New **Settings → "FCD freq correction"** spinbox (range −200 to
  +200 ppm, 0.1 ppm steps). INI key `funcube/ppm`.
- Applied to every CAT tune as `requested_hz × (1 + ppm·1e-6)`. The
  FCD's HID protocol has no hardware PPM register, so this is
  software trim — same approach AMSAT-UK's FCD Control app uses.
- Live-applied without bridge restart: the next WSJT-X dial change
  (or manual-override toggle) re-reads the value from INI and
  applies it on the very next CAT tune.
- Bridge log line `[CAT] FunCube PPM correction: %.2f ppm` confirms
  the value at startup.

**Calibration tip**: feed a GPSDO-locked sig-gen (or use a verified
WSPR / FT8 carrier) into the FCD. Tune the bridge to the carrier
freq via WSJT-X. Look at the WSJT-X waterfall — if the carrier sits
at, say, +47 Hz when you tuned to land it at +1500 Hz audio (i.e. dial
+ 1453 Hz), the FCD is reading 47 Hz low. At 144 MHz: 47 / 144e6 ×
1e6 ≈ +0.33 ppm of correction needed. Type +0.33 into the field, hit
Apply, retune. Iterate until the carrier lands on the dial.

If you've already calibrated this in AMSAT-UK's FCD Control app or
qthid, just copy the same value into this field. Both use software
PPM trim of the same form.

Drop-in upgrade from v0.99.0.

## v0.99.0 — first release (2026-05-02)

**Successor to `malachite-rx-bridge` v0.99.x.** Same DSP path, broader
audience, optional CAT control of FunCube Dongle Pro+ via USB HID.

The legacy `malachite-rx-bridge` v0.99.1 keeps shipping for testers
who already have it installed; new installs should use `iq-rx-bridge`.
The two binaries can install side-by-side (different AppId GUIDs).

### What this app does

Feeds **QMAP** wideband Q65 from any radio whose IQ baseband comes
out as a USB sound card:

| Source | CAT support | Sample rate |
|---|---|---|
| Malachite-DSP / DSP2 | None — operator types freq | 96 / 192 kHz |
| Malachite-DSP3 | (planned) FT-897 emulation, serial CAT | 96 / 192 kHz |
| **FunCube Dongle Pro+ V1/V2** | **USB HID — auto-tune from WSJT-X** | up to 192 kHz |
| FlexRadio + DAX-IQ → virtual cable | None at this layer | 48 / 96 / 192 kHz |
| K3 KXV3A → external mixer → line-in | None | sound-card-dependent |
| SDR Console / SDR# IQ → virtual cable | None | sound-card-dependent |
| Generic IF tap into any sound card | None | sound-card-dependent |

The IQ capture path is identical across all of these (USB Audio Class
stereo, L=I R=Q). The bridge picks the right CAT backend at runtime
via Settings → "CAT controller".

### What's new vs. malachite-rx-bridge v0.99.1

- **CAT controller dropdown** in Settings → "Sound-card IQ input":
  - **Generic / No CAT** (default) — operator types freq into the
    Manual SDR frequency field. Same as the old Malachite bridge.
  - **FunCube Dongle Pro+ V1/V2 (USB HID CAT)** — bridge auto-tunes
    the FCD on every WSJT-X dial change. Both V1 and V2 (same
    VID/PID 0x04D8:0xFB31, same protocol).
- **No driver fuss for the FCD**: the HID interface is a stock USB
  HID device, no Zadig step needed.
- All multi-instance support inherited from bridge-core (`--instance`,
  Linrad TCP/UDP port spinboxes, namespaced INI / window title).

### Setup with FunCube Dongle Pro+

1. Plug the FCD in. Windows mounts it as a USB Audio Class device
   (`USB Audio CODEC` / `FUNcube Dongle V2.0`) and a USB HID device
   (no driver step needed).
2. Launch the bridge. Settings → "Sound-card IQ input":
   - **Input device** = the FCD (whatever Windows shows it as).
   - **Sample rate** = 192 000 Hz (FCD Pro+ max — best for QMAP).
   - **CAT controller** = "FunCube Dongle Pro+ V1/V2 (USB HID CAT)".
   - Tick **"Follow WSJT-X dial via UDP (port 2237)"**.
3. Apply, **restart the bridge** (CAT controller change takes effect
   on next launch).
4. Open WSJT-X. Settings → Reporting → "Accept UDP requests" on,
   port 2237.
5. Spin the WSJT-X dial → the FCD follows. Watch QMAP around the
   dial.
6. Bridge log line `[FCD] Connected: FCDAPP …` confirms HID
   handshake. `[FCD] SET_FREQ` ack lines confirm each retune.

### Setup with generic / no-CAT sources (Malachite-DSP/DSP2 etc.)

Same as the old `malachite-rx-bridge` flow:

1. Pick the radio's IQ output mode + sample rate at the radio.
2. Bridge → Settings → pick the device + matching sample rate.
3. Tick "Manual SDR frequency", type whatever the radio is tuned
   to. Update it whenever you re-tune.
4. CAT controller stays at "Generic / No CAT" (default).

### Multi-instance ops

Same patterns as the SDR siblings: `--instance <name>` namespaces
the INI; Settings → Linrad TCP/UDP ports let two bridges feed two
QMAPs without colliding. See the RTL-SDR v0.99.8 notes for a full
walkthrough.

### Known limitations

- **CAT controller change requires a restart.** The combo writes the
  choice to INI on Apply; the controller is instantiated at startup.
  Live re-instantiation is on the followup list — pushed to keep
  this release small.
- **No FCD gain control yet.** Only `setFrequencyHz()` is wired.
  LNA / mixer / IF gain via additional HID reports (commands 110,
  114, 116) lands in a future v0.99.x. For now, use AMSAT-UK's FCD
  Control app for gain settings if needed (it can run alongside).
- **FCD Pro (original)** — older FCD has VID/PID 0x04D8:0xFB56 and
  a different command set. Not supported in v0.99.0; will land if
  there's tester interest.
- **Malachite-DSP3 serial CAT** — placeholder; implementation lands
  in a future v0.99.x once tested against actual DSP3 hardware.

### License

Copyright (C) 2026 **Andreas Junge, N6NU** &lt;<n6nu@arrl.net>&gt;.
Licensed under the **GNU General Public License v3 or later** —
see [`LICENSE`](LICENSE). Bundled third-party components — Qt 6,
FFTW3, SoXr, libhidapi — are documented in
[`THIRD_PARTY_LICENSES.md`](THIRD_PARTY_LICENSES.md).
**No warranty.** Install and run at your own risk.
