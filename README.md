# Malachite RX Bridge — Beta Releases

Windows installer downloads for the **Malachite RX Bridge** — a Qt6
C++ application that pumps the IQ baseband stream from a Malachite
SDR (or any USB-IQ-soundcard SDR — FlexRadio, K3 KXV3A → external
quadrature mixer, SDR Console / SDR# routing IQ to a virtual cable,
etc.) into **QMAP** for wideband Q65 decode.

The "anything but Linrad" bridge: Linrad has been the standard
sound-card IQ → MAP65 / QMAP UDP feeder for decades and is deeply
capable, but its UI is notoriously arcane. This bridge ships the
same LinradServer-compatible wire format with a modern Qt6 GUI —
same look and feel as the sibling SDR bridges (HackRF / RTL-SDR /
SDRplay / AirSpy).

Author: **Andreas Junge, N6NU** &lt;<n6nu@arrl.net>&gt;.

---

## Latest release — v0.99.0 (first beta)

| Variant | Download |
|---|---|
| **Windows 10 / 11** (installer) | **[malachite-rx-bridge-0.99.0-setup.exe](malachite-rx-bridge-0.99.0-setup.exe)** |
| **Windows 10 / 11** (portable zip — recommended) | **[malachite-rx-bridge-0.99.0-win11.zip](malachite-rx-bridge-0.99.0-win11.zip)** |

The portable zip is the recommended download for first-time testers
— self-contained, no installer, no admin rights, no DLL deployment
issues. The installer build is the same binary just packaged with
Inno Setup.

---

## What this app does

- Opens a USB sound card as a **stereo IQ source** (L=I, R=Q).
- Pumps int16 IQ pairs into **LinradServer** at the configured
  sample rate (48 / 96 / 192 kHz selectable).
- LinradServer applies the (-1)^n·conj transform that QMAP requires
  and ships UDP packets to **127.0.0.1:50004**.
- Spectrum / waterfall display with **Ctrl+W** to toggle off if not
  needed.
- Optional **WSJT-X UDP follow** (port 2237) so QMAP labels can
  match the WSJT-X dial — off by default; most operators just type
  the radio's tuned freq into Settings → "Manual SDR frequency".

## What this app does NOT do

- **SSB demodulation.** The Malachite (or whatever the IQ source
  is) already does that on its end. Operators who want narrowband
  WSJT-X decode route the radio's separate audio output to VB-Cable
  themselves.
- **Frequency tuning of the radio.** The Malachite has no CAT
  surface — there is no API path from the bridge to the radio. The
  operator turns the knob / taps the touchscreen and tells the
  bridge what label to display via Settings → "Manual SDR
  frequency".
- **WSJT-X RX audio path.** This bridge is a pure QMAP feeder.

---

## Setup (Malachite → bridge → QMAP)

1. **Configure the Malachite for IQ output.** On the radio, open
   the audio settings and switch the USB output mode from
   "demodulated audio" to **"stereo IQ"**. Pick a sample rate
   (48 / 96 / 192 kHz) — 96 kHz is the cleanest fit for QMAP's
   wideband window.

2. **Plug the Malachite into the PC.** Windows will see it as a
   USB audio device. No special driver / Zadig step required —
   it's a class-compliant USB audio device.

3. **Launch the bridge.** Open Settings → "Sound-card IQ input":
   - **Input device** = the Malachite (look for a name like
     "USB Audio Device" or your radio's product name).
   - **Sample rate** = match what you set on the radio.
   - **Hardware label** = whatever you want; defaults to
     "Malachite SDR".

4. **Set the dial label.** Open Settings → tick "Manual SDR
   frequency", enter the freq the Malachite is tuned to (e.g.
   `144.174` MHz). Apply. Update it whenever you re-tune the
   Malachite.

5. **Launch QMAP.** Make sure it's listening on UDP 50004
   (Settings → Network input enabled, port 50004).

6. **Watch the waterfall.** If signals appear mirrored around
   centre, open Settings and toggle **"Swap I/Q"**.

---

## Frequency control — important

There is no CAT path from this bridge to the Malachite. Operators
type the dial value into Settings → "Manual SDR frequency" so QMAP's
waterfall axis labels match. The freq label only affects display;
the actual signal processing is centre-relative and works correctly
even if the label is wrong.

If you're also tuning WSJT-X to match the radio, you can flip
Settings → "Follow WSJT-X dial via UDP" and the bridge will pick up
the dial automatically.

---

## SmartScreen on first launch

The exe is **not code-signed**. On first launch on a fresh Windows
machine you'll see:

> Windows protected your PC.
> Microsoft Defender SmartScreen prevented an unrecognized app from
> starting.

Click **More info → Run anyway**. One-time per binary.

---

## Reporting

Please include:

- Which radio (Malachite v1 / v2 / v3 / DSP-2 / Discovery Mini, or
  another IQ source).
- The radio's IQ output sample rate setting.
- A `--console` log line: `[SoundCard] opened '<device>': <rate> Hz
  stereo, fmt=int16 ...` confirms the bridge sees the right device.
- A `--console` log line: `[Stats] RX <pairs> in 5s ...` confirms IQ
  is flowing through to QMAP.
- Whether QMAP shows signal in its bottom waterfall.

Reports to: <n6nu@arrl.net>.

License: GPLv3 — see `LICENSE`.
