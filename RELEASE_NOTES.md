# Malachite RX Bridge — Release Notes

## v0.99.1 — multi-instance support (multi-band ops) (2026-05-02)

Run two sound-card-IQ bridges side-by-side — different sound cards,
different WSJT-X / QMAP instances, no shared state. Especially
useful when one Malachite-DSP feeds 2 m and a second sound-card IQ
source (FlexRadio virtual cable, FunCube Dongle Pro+, etc.) feeds
70 cm in the same shack.

- New `--instance <name>` CLI flag namespaces the INI file, window
  title, and taskbar entry. `malachite-rx-bridge.exe --instance fcd`
  reads/writes `Malachite RX Bridge - fcd.ini`.
- New **Settings → "Linrad TCP port"** + **"Linrad UDP port"**
  spinboxes (defaults 49812 / 50004). Increment per bridge instance
  for multi-QMAP setups. CLI: `--linrad-tcp-port`,
  `--linrad-udp-port`. Take effect on next launch.

See RTL-SDR v0.99.8 notes for a full multi-instance walkthrough.

Drop-in upgrade from v0.99.0.

## v0.99.0 — first beta (2026-05-02)

First beta release. Primary target: **Malachite SDR** family (the
portable touchscreen receivers that expose IQ baseband as a USB
sound card). Same code path supports any other USB-IQ-soundcard
source — FlexRadio virtual cables, K3 KXV3A → external quadrature
mixer + line-in, SDR Console / SDR# routing IQ to a virtual cable,
etc. The underlying device class (`SoundCardDevice`) is generic;
the Malachite branding is just the most common audience.

**What this app does:**

- Opens a USB sound card as a stereo IQ source (L=I, R=Q).
- Pumps int16 IQ pairs into `LinradServer` at the configured sample
  rate (48 / 96 / 192 kHz).
- LinradServer applies the (-1)^n·conj transform QMAP requires and
  ships UDP packets to `127.0.0.1:50004`.
- Spectrum / waterfall display with the same Ctrl+W toggle as the
  SDR siblings.
- Optional WSJT-X UDP follow (port 2237) so QMAP labels can match
  the WSJT-X dial — off by default, since most operators just type
  the radio's tuned freq into Settings → "Manual SDR frequency".

**What this app does NOT do:**

- SSB demodulation. The radio (Malachite) already does that on its
  own; the operator listens via the radio's headphone jack or routes
  its separate audio output to VB-Cable for WSJT-X narrowband decode.
- Frequency tuning of the radio. The operator turns the knob /
  taps the touchscreen — there is no API path from the bridge to
  the Malachite (or to a generic sound-card source).
- WSJT-X RX audio. There is no demodulated audio path through this
  bridge; it's a pure QMAP feeder.

**The "anything but Linrad" angle:**

Linrad has been the standard "sound-card IQ → MAP65/QMAP UDP" feeder
for nearly two decades, and it's deeply capable — but its UI is
notoriously arcane, especially for operators who just want to point
a Malachite at QMAP and decode. This bridge provides the same
LinradServer-compatible UDP wire format with a modern Qt6 GUI that
matches the SDR siblings (HackRF / RTL-SDR / SDRplay / AirSpy).

**Setup (Malachite → bridge → QMAP):**

1. **Configure the Malachite for IQ output.** On the Malachite, go
   to the audio menu and switch the USB output from "demod audio" to
   "IQ stereo". Set the rate (48 / 96 / 192 kHz). Verify Windows sees
   the Malachite as a stereo input device (`USB Audio Device` or the
   Malachite's product name).
2. **Launch the bridge.** Open Settings → "Sound-card IQ input":
   - **Input device** = the Malachite.
   - **Sample rate** = whatever you set on the radio.
   - **Hardware label** = whatever you want (defaults to "Malachite SDR").
3. **Set the dial label.** Open Settings → tick "Manual SDR
   frequency", enter the freq the Malachite is tuned to (e.g.
   `144.174` MHz). Apply. The bridge labels the QMAP stream with
   that value. Update it whenever you re-tune the Malachite.
4. **Launch QMAP.** Make sure it's listening on UDP 50004
   (`Settings → Network input enabled, port 50004`).
5. **Watch the waterfall.** If signals appear mirrored around centre,
   open Settings and toggle "Swap I/Q".

**Known limitations:**

- **Manual freq labels are operator-driven.** Every time the
  operator re-tunes the Malachite, they need to update the manual
  freq in Settings (or have WSJT-X set to the same dial and tick
  "Follow WSJT-X dial via UDP"). A future v0.99.1 may add a freq
  input field on the main window for one-click updates.
- **First version, untested on actual Malachite hardware.** The
  build was smoke-tested on Windows 11 against a USB audio loopback
  (a sound card that exposes a 96 kHz stereo input). Real Malachite
  IQ verification is pending the requesting operator's report.
- **No automatic sample-rate detection.** If the radio's IQ rate
  doesn't match what's selected in Settings, you'll get aliased
  garbage in QMAP. Match them.
- **No WSJT-X RX audio path.** Use VB-Cable separately if narrowband
  WSJT-X decode is wanted.

**Reporting:** to <n6nu@arrl.net>.
