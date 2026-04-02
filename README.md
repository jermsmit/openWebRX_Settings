# OpenWebRX RTL-SDR Configuration Files

Ready-to-use `settings.json` configurations for [OpenWebRX](https://www.openwebrx.de/), built and tested for the **RTL-SDR Blog V4** and **RTL-SDR Blog V3** receivers. Each file provides full continuous coverage from **500 kHz to 1.766 GHz**, segmented into logical device groups for clean navigation inside OpenWebRX.

---

## Files

| File | Device | HF Method |
|---|---|---|
| `settings_v4_segmented.json` | RTL-SDR Blog V4 (R828D) | Built-in upconverter — no extra config needed |
| `settings_v3_segmented.json` | RTL-SDR Blog V3 (R820T2) | `direct_sampling: "2"` on all HF profiles |

---

## Device Groups

Both files share the same 9 logical device groups. Each appears as a separate selectable device in OpenWebRX while pointing to the same physical hardware (`device_index: 0`).

| Group | Frequency Range | What's Inside |
|---|---|---|
| **HF Broadcast** | 14 kHz – 108 MHz | VLF Nav, LW, NDB, AM broadcast, all SW bands, FM tiled |
| **Amateur HF** | 1.8 – 54 MHz | 160m through 6m, CB, inter-band scan fills |
| **Amateur VHF/UHF** | 144 – 450 MHz | 2m, APRS, 1.25m, 70cm, FT8 spot frequencies |
| **Aviation** | 108 – 1090 MHz | VOR/ILS, Airband Comm, ACARS, Military, ADS-B, Mode S |
| **VHF Low / TV** | 29.7 – 88 MHz | VHF utility/land mobile, VHF TV Ch2–6 |
| **Public Safety** | 150 – 960 MHz | VHF EMS/Fire, Marine VHF, UHF Fed, Land Mobile, 700/800 MHz trunked |
| **Pagers** | 152 MHz + 929–932 MHz | POCSAG (VHF) and FLEX (UHF) — `page` mode enabled |
| **FRS / GMRS** | 462 – 468 MHz | All 14 FRS channels, 8 GMRS simplex, repeater in/out pairs, panoramic overview |
| **ISM / Satellite** | 433 MHz – 1.766 GHz | ISM 433/915, Inmarsat, GPS L1, GLONASS, Iridium, NOAA HRPT, DECT |

**Total: 788 profiles. Zero gaps between 500 kHz and 1.766 GHz.**

---

## Key Settings

- **Bandwidth:** 2.4 MHz per profile (stable maximum for both V3 and V4)
- **Tuning step:** 10 Hz for SSB modes, 100 Hz for all others, 1 Hz for APRS/ADS-B
- **Squelch:** Pre-set on Public Safety, Marine, FRS/GMRS, and Pager profiles
- **RF Gain:** 35 dB default; 40 dB on weak-signal profiles (VLF, NOAA, pagers); 49 dB at the V4 hardware upper edge

### V3 Specific

All profiles with a `center_freq` below 28.8 MHz have `direct_sampling: "2"` set automatically. This enables Q-branch sampling on the R820T2, which is required for HF reception on the V3 since it has no built-in upconverter. RF gain on those profiles is also bumped +5 dB to compensate. No additional driver configuration is needed beyond what the V3 already requires.

---

## Installation

### Using GitHub Desktop

1. Clone or download this repository to your machine
2. Copy the appropriate file to your OpenWebRX config directory:
   - Docker: `/opt/openwebrx/` or the volume mapped to your container's config path
   - Native install: `/etc/openwebrx/`
3. Rename the file to `settings.json` (replacing any existing file)
4. Restart OpenWebRX

```bash
# Example for native install
cp settings_v4_segmented.json /etc/openwebrx/settings.json
sudo systemctl restart openwebrx
```

> **Backup first.** Replace your existing `settings.json` only after saving a copy.

---

## RTL-SDR Hardware Notes

### V4 (R828D)
- Built-in upconverter handles HF natively — no `direct_sampling` needed
- Triplexed SMA input (HF / VHF / UHF) for better isolation
- Built-in AM/FM/DAB notch filters
- Requires RTL-SDR V4 drivers (`librtlsdr` 0.6.0+)

### V3 (R820T2)
- No upconverter — HF via direct sampling Q-branch
- Standard RTL-SDR drivers work fine
- Slightly lower HF sensitivity than the V4 — consider an external LNA for weak HF signals

---

## Customization

Each profile is a standard OpenWebRX profile object. Common adjustments:

- **`ppm`** — set your calibrated PPM offset (default 0)
- **`rf_gain`** — adjust per your antenna and local noise floor
- **`initial_squelch_level`** — raise if your area has a high noise floor
- **`device_index`** — change if you have multiple RTL-SDR devices connected

---

## Requirements

- [OpenWebRX](https://www.openwebrx.de/) (version supporting `settings.json` v8)
- RTL-SDR Blog V4 or V3 receiver
- Appropriate drivers installed for your device
