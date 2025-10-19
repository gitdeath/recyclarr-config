# Recyclarr Configuration Overview

This repository contains a `recyclarr.yml` configuration file designed to automate the management of quality settings and custom formats for Sonarr and Radarr instances. It leverages the community-curated TRaSH Guides to ensure optimal media acquisition based on specific, fine-grained preferences.

The configuration is split into three main instances:
1.  **Sonarr (`sonarr_tv_anime`):** Manages both regular TV shows and Anime, with distinct profiles for each.
2.  **Radarr (`radarr_1080p`):** Manages a library of 1080p movies, with a focus on specific audio codecs.
3.  **Radarr (`radarr_4k`):** Manages a high-quality 4K HDR movie library.

## Quick Start

You can download this configuration file directly to your current directory in a Linux environment using `curl`.

```bash
curl -O https://raw.githubusercontent.com/gitdeath/recyclarr-config/main/recyclarr.yml
```

---

## Sonarr Instance: `sonarr_tv_anime`

This instance handles both TV shows and Anime, directing them to separate quality profiles with unique scoring logic.

### Quality Profile: `TV`

This profile is for standard television series.

-   **Quality Priority:** It prioritizes `WEB 1080p` releases first, followed by other 1080p sources, then 720p, and finally older/SD formats. This is designed to get good quality releases while being mindful of storage space.
-   **Upgrades:** It allows upgrades until a release reaches `WEB 1080p` quality or a custom format score of `1000`.
-   **Score Cleaning:** `reset_unmatched_scores` is enabled, which means any custom format not explicitly defined for this profile in the config will have its score reset to `0`. This prevents unwanted formats from influencing download choices.

#### Custom Format Scoring for `TV`

-   **Penalized (-1500 score):**
    -   `Language Not Original`: Strongly rejects any release that is not in the show's original language.
-   **Default Scores (from TRaSH Guides):**
    -   **Unwanted:** Rejects BR-DISK, low-quality (LQ) releases, extras, AV1/x265(HD) codecs, and releases from bad dual-audio groups.
    -   **Release Quality:** Scores `Repack/Proper` releases to get fixes.
    -   **Source Preference:** Scores releases from high-quality WEB sources (Tiers 01-03) and Scene groups.
    -   **Streaming Services:** Scores releases from various streaming services (e.g., Netflix, HBO Max, DSNP).
-   **Informational (0 score):**
    -   A wide range of audio formats (DTS, Dolby Digital, Atmos, etc.) are tagged for visibility in the Sonarr UI but do not affect scoring.

### Quality Profile: `Anime`

This profile is specifically tuned for Anime series.

-   **Quality Priority:** Similar to the TV profile, it prioritizes `WEB 1080p` releases over Blu-ray to save space, followed by other resolutions.
-   **Upgrades:** Allows upgrades until a release is `WEB 1080p` or has a score of `1000`.
-   **Scoring:** It requires a minimum format score of `100`, meaning it will not grab releases unless they match a preferred custom format. It uses the `anime-sonarr` score set from the TRaSH guides.

#### Custom Format Scoring for `Anime`

-   **Default Scores (from `score_set: anime-sonarr`):**
    -   **Source Preference:** Heavily scores and prefers releases from well-regarded Anime Blu-ray and WEB release groups (e.g., SeaDex Muxers, Top FanSubs).
    -   **Unwanted:** Penalizes low-quality groups, raws (no subtitles), and dubs-only releases.
    -   **Release Versions:** Prefers newer revisions (`v1`, `v2`, etc.) over initial releases (`v0`).
    -   **Subtitles:** Penalizes `VOSTFR` (French subtitles).
    -   **Codecs:** Penalizes `AV1`.
-   **Informational (0 score):**
    -   Audio formats are tagged for visibility without affecting scores.

---

## Radarr Instance: `radarr_1080p`

This instance is configured for a general-purpose 1080p movie library.

### Quality Profile: `Movies 1080p`

-   **Quality Priority:** Groups all 1080p sources (WEB, Blu-ray, HDTV) together, allowing the custom format score to be the primary decision-maker for the best release.
-   **Upgrades:** Allows upgrades until a score of `3400` is reached within the `1080p` quality group.
-   **Audio Preference:** This profile is explicitly configured to **reject** high-resolution audio formats like DTS-HD MA, TrueHD, and DTS:X. This is ideal for setups that do not support these formats (e.g., direct playback on a TV without a receiver) and saves significant storage space.

#### Custom Format Scoring for `Movies 1080p`

-   **Penalized (-1500 score):**
    -   `DTS`, `DTS:X`, `DTS-HD MA`, `TrueHD`, `TrueHD ATMOS`: Strongly rejects releases with these audio codecs.
-   **Default Scores (from TRaSH Guides):**
    -   **Unwanted:** Rejects BR-DISK, 3D, upscaled, LQ, and AV1/x265(HD) releases.
    -   **Source Preference:** Prefers releases from high-quality Blu-ray and WEB groups (Tiers 01-03).
    -   **Audio Preference:** Prefers releases with `5.1 Surround` and `DD` audio.
-   **Informational (0 score):**
    -   Tags for special movie versions (`Criterion Collection`, `IMAX`, etc.) and certain audio codecs (`FLAC`, `AAC`) are applied for visibility without affecting scores.

---

## Radarr Instance: `radarr_4k`

This instance is dedicated to acquiring high-quality 4K HDR movies.

### Quality Profile: `Movies 4k`

-   **Quality Priority:** Only seeks out `2160p` releases.
-   **Upgrades:** Allows upgrades until a custom format score of `13400` is reached.
-   **Audio Preference:** In contrast to the 1080p profile, this one **rejects** lossless audio formats to save space while still getting a 4K picture.

#### Custom Format Scoring for `Movies 4k`

-   **Penalized (-1500 score):**
    -   `DTS`, `DTS:X`, `DTS-HD MA`, `TrueHD`, `TrueHD ATMOS`: Strongly rejects releases with these high-resolution audio codecs.
-   **Default Scores (from TRaSH Guides):**
    -   **HDR Formats:** Strongly prefers releases with any form of HDR, including `HDR10+` and `Dolby Vision (DV)`.
    -   **Source Preference:** Prefers releases from top-tier UHD Blu-ray and WEB groups.
    -   **Unwanted:** Rejects BR-DISK, 3D, upscaled, LQ, and AV1/x265(HD) releases.
    -   **Audio Preference:** Prefers releases with `5.1 Surround` and `DD` audio.
-   **Informational (0 score):**
    -   Tags for special movie versions (`IMAX`, `Remaster`) and certain audio codecs (`FLAC`, `AAC`) are applied for visibility.
