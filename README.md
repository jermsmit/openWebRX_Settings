# OpenWebRX Settings

Exported settings for my [OpenWebRX](https://www.openwebrx.de/) / [OpenWebRX+](https://github.com/luarvique/openwebrx) installation. Shared here as a reference for others setting up similar configurations.

## Hardware

- **SDR Dongles:** Multiple RTL-SDR receivers
- **Bandplan Region:** ITU Region 2 (Americas)

## SDR Configurations

This setup uses four RTL-SDR dongles, each dedicated to a different frequency range:

### RTL-SDR — VHF/UHF General
A general-purpose SDR covering the VHF and UHF bands. Profiles include:

- **Amateur Radio:** 2m (144–148 MHz in two segments), 70cm repeaters (438.8 MHz), 1.25m (223.5 MHz)
- **Aviation (AirBand):** Four profiles covering 118–137 MHz in AM
- **HF Amateur:** 6m, 10m, 12m, 15m, 17m, 20m, 30m, 40m, 60m, 80m, 160m
- **Shortwave Broadcast:** 49m, 41m, 31m, 25m, 22m shortwave broadcast bands
- **CB Radio:** 11m CB (27 MHz, AM) and a general 27 MHz profile
- **AM Broadcast:** ~1.1 MHz center coverage
- **Digital/Data Modes:** APRS (144.390 MHz), ISM 433 MHz digital devices, ADS-B (1090 MHz), Pagers (930 MHz), WSPR (50.010 MHz)
- **Local/Regional:** Old Barney repeater (146.835 MHz), W2NJR Repeater (445.075 MHz), Police Dispatch, Police Channel 9, EMS 6, School Buses, Public Works, Utilities
- **Misc:** FRS channels (462/467 MHz), Marine Radio (151.8 MHz), NOAA Weather (162.463 MHz), Baby Monitors (426 MHz), ISM 902 MHz, TTN LoRa (124.5 MHz)

### RADIO- — FM Broadcast
Dedicated FM broadcast band coverage from 88–107 MHz, with each MHz as a separate profile using wide FM (WFM) demodulation.

### 0–30 MHz — HF Sweeper
Covers the full 1–30 MHz HF spectrum with 1 MHz step profiles in AM, plus dedicated ham band profiles:
- **80 Meters** (3.6 MHz, LSB)
- **40 Meters** (7.1 MHz, LSB)
- **30 Meters** (10.12 MHz, USB)
- **20 Meters** (14.15 MHz, USB)
- **15 Meters** (21 MHz, USB)

### 300–1000 MHz — UHF Scanner
Systematic 2 MHz segment coverage of the 70cm amateur band (420–450 MHz) with squelch set for the band noise floor.

## Display & Waterfall Settings

- **Waterfall scheme:** Google Turbo
- **FFT size:** 4096 points
- **FFT FPS:** 9
- **Auto level mode:** Enabled by default
- **Audio compression:** ADPCM
- **Tuning precision:** Rounded (0)

## Digital Mode Settings

- **WSJT decoding depth:** 3 (JT65 at depth 1)
- **JS8Call profiles:** Normal, Slow
- **FST4 intervals:** 15s, 30s
- **FST4W intervals:** 120s, 300s
- **Q65 combinations:** A30, C60, E120
- **DMR/NXDN ID lookup:** Enabled
- **RDS/RBDS:** Enabled
- **ADS-B TTL:** 900s
- **Paging charset:** US

## Usage

1. Copy `settings.json` to your OpenWebRX config directory (typically `/etc/openwebrx/` or via the admin UI export/import).
2. Update the placeholder values:
   - `receiver_name` — your station name
   - `receiver_admin` — your contact email
   - `receiver_location` — your location description
   - `receiver_gps` — your latitude/longitude
   - `magic_key` — set a secure value
   - `google_maps_api_key` / `openweathermap_api_key` — add your API keys if desired
3. Adjust SDR device IDs and profiles to match your hardware and local frequencies.

## Notes

- Local profiles (police, EMS, school buses, public works, utilities, repeaters) are tuned for the **Ocean County, NJ** area and will need adjustment for other locations.
- The `direct_sampling` setting on some HF profiles may need to be enabled depending on your RTL-SDR hardware variant.
- ADS-B is configured on both the general RTL-SDR (NFM profile) and a dedicated ADSB profile — use whichever suits your setup.
