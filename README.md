# OpenWebRX RTL-SDR Configuration Files

Ready-to-use `settings.json` configurations for [OpenWebRX](https://www.openwebrx.de/), built and tested for the **RTL-SDR Blog V4**, **RTL-SDR Blog V3**, and **Nooelec NESDR V5** receivers. Each file provides full continuous coverage from **500 kHz to 1.766 GHz**, segmented into logical device groups for clean navigation inside OpenWebRX.

---

## Changelog

### v2.0 — Bug Fix Release (April 2026)

This release resolves several critical issues that caused the OpenWebRX error:

> *SDR device "[name]" has failed, selecting new device*

All fixes were applied identically to both `settings_v3_segmented.json` and `settings_v4_segmented.json` unless noted.

#### Fix 1 — Section divider profiles missing `rf_gain` (27 profiles per file)

Section header profiles (keys prefixed with `_`, e.g. `_frs`, `_gmrs`, `_vhf`) are used as visual separators in the OpenWebRX profile list. These profiles were missing the `rf_gain` field entirely. When OpenWebRX selected one of these as the active profile — either on initial load or when switching bands — the RTL-SDR driver rejected the incomplete configuration and triggered the device failure error.

**Fix:** Added `rf_gain` to all 27 divider profiles in each file, derived from the median gain of the real profiles within the same device group.

Affected device groups: HF Broadcast, Amateur VHF/UHF, Aviation, Public Safety, Pagers, FRS/GMRS, ISM/Satellite, VHF Low/TV.

#### Fix 2 — FRS/GMRS and Amateur HF: sample rates below RTL-SDR hardware minimum (27 profiles per file)

The RTL-SDR driver (`librtlsdr`) has a hard minimum sample rate of **~225,001 Hz**. Any value below this causes the driver to fail immediately, producing the device failure error.

Two groups of profiles were affected:

**FRS/GMRS individual channel profiles** (`frs_01`–`frs_14`, `gmrs_15`–`gmrs_22`) — set to `samp_rate: 25000` (25 kHz) in an attempt to show a single channel at a time. While logical in intent, this value is far below the hardware floor.

**Amateur HF narrow-band profiles** — five profiles with sample rates that were too low for the driver to accept:

| Profile | Band | Old Rate | Fixed Rate |
|---|---|---|---|
| `ham_160` | 160m | 200,000 Hz | 250,000 Hz |
| `ham_60` | 60m | 75,000 Hz | 250,000 Hz |
| `ham_30` | 30m | 50,000 Hz | 250,000 Hz |
| `ham_17` | 17m | 100,000 Hz | 250,000 Hz |
| `ham_12` | 12m | 100,000 Hz | 250,000 Hz |

**Fix:** All affected profiles raised to `samp_rate: 250,000` Hz — the smallest valid rate. The 250 kHz window still provides a tight, focused view of each channel or band segment.

#### Fix 3 — Aviation Military Airband: sample rate data entry error (7 profiles per file)

Profiles `mil_001` through `mil_007` (Military Airband 225–400 MHz) had `samp_rate: 25000000` (25 MHz). The RTL-SDR maximum is 3.2 MHz. This was a transcription error — a missing zero.

**Fix:** Corrected to `samp_rate: 2500000` (2.5 MHz) across all seven profiles.

#### Fix 4 — ISM/Satellite: two profiles with center frequencies beyond hardware range

| Profile | Name | Old `center_freq` | Fix |
|---|---|---|---|
| `dect` | DECT Cordless | 1,890,000,000 Hz (1890 MHz) | Capped to 1,766,000,000 Hz; name updated to indicate hardware limitation |
| `lb_1734_014` | L-Band 1765–1766 MHz | 1,766,400,000 Hz | Nudged to 1,765,000,000 Hz |

The RTL-SDR V3 and V4 have a hardware ceiling of approximately 1,766 MHz. DECT at 1880 MHz is outside the range of any RTL-SDR device; a HackRF or Airspy R2 would be required to receive it.

#### Fix 5 — V4 only: HF and lower Amateur HF profiles missing `direct_sampling`

The V4 settings file was missing `direct_sampling: "2"` on 26 HF profiles (all VLF/LW/AM/SW broadcast and Amateur HF profiles below 24 MHz). Without this flag, the V4 driver does not engage Q-branch direct sampling mode for these frequencies, resulting in noise instead of signal.

**Note:** The V3 file had these set correctly already. This was a V4-specific omission.

---

## Files

| File | Device | HF Method |
|---|---|---|
| `settings_v4_segmented.json` | RTL-SDR Blog V4 (R828D) | Built-in upconverter — no extra config needed |
| `settings_v3_segmented.json` | RTL-SDR Blog V3 (R820T2), Nooelec NESDR V5 | `direct_sampling: "2"` on all HF profiles |

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

### Nooelec NESDR V5 Compatibility

The `settings_v3_segmented.json` file has been confirmed working on the **Nooelec NESDR V5**. Use it the same way as the V3 file — the direct sampling configuration and gain settings are compatible with the NESDR V5's R820T2 tuner.

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

### Nooelec NESDR V5

- Compatible with `settings_v3_segmented.json`
- Uses the same R820T2 tuner family as the RTL-SDR Blog V3
- Standard RTL-SDR drivers — no special configuration required

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
- RTL-SDR Blog V4, RTL-SDR Blog V3, or Nooelec NESDR V5 receiver
- Appropriate drivers installed for your device
