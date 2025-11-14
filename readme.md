# Recyclarr Configuration Overview

This repository contains a `recyclarr.yml` configuration file designed to automate the management of quality settings and custom formats for Sonarr and Radarr instances. It leverages the community-curated TRaSH Guides to ensure optimal media acquisition based on specific, fine-grained preferences.

The configuration is split into three main instances:
1.  **Sonarr (`sonarr_tv_anime`):** Manages both regular TV shows and Anime, with distinct profiles for each.
2.  **Radarr (`radarr_1080p`):** Manages a library of 1080p movies, with a focus on specific audio codecs.
3.  **Radarr (`radarr_4k`):** Manages a high-quality 4K HDR movie library.

## Is This Configuration For You?

This Recyclarr setup is designed for users who want a highly automated, "set-it-and-forget-it" media server. It's a good fit if you identify with the following:

*   **You value automation over manual searching:** The primary goal is to find and grab the best possible release automatically, even for obscure or older content, minimizing the need for manual intervention.
*   **You have flexible playback options:** Your setup includes clients that can direct play a wide variety of formats, or you use a media server (like Plex or Jellyfin) capable of transcoding when needed.
*   **You prefer quality and compatibility:** The profiles are tuned to prefer widely supported formats to reduce transcoding. It also actively avoids known bad release groups to prevent downloading junk files, which ultimately saves you from manual cleanup.
*   **You manage both TV and Anime:** This configuration uses a single Sonarr instance with separate, optimized profiles for both regular TV shows and Anime.
*   **You want control over movie resolution:** The setup includes separate Radarr instances for 1080p and 4K movies. You can easily use one, the other, or both by simply commenting out the instance you don't need in your `recyclarr.yml` file.

### Prerequisites & Setup

This configuration file is meant to be used with [**Recyclarr**](https://github.com/recyclarr/recyclarr). Please follow the official installation and setup guides.

It is also assumed that you are using a `secrets.yml` file to store your API keys and instance URLs, as this is a security best practice. You can find the official documentation on how to set this up [here](https://recyclarr.dev/wiki/yaml/secrets-reference/).

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

-   **Quality Priority:** It prioritizes releases in named groups: `WEB 1080p` is first, followed by a `1080p` group (for Blu-ray/HDTV), a `720p` group, an `Other` group for SD formats, and finally an `Unknown` group as a catch-all. This is designed to get good quality releases while being mindful of storage space.
-   **Upgrades:** It allows upgrades until a release reaches `WEB 1080p` quality with a custom format score of `1500`.
-   **Score Cleaning:** `reset_unmatched_scores` is enabled, which means any custom format not explicitly defined for this profile in the config will have its score reset to `0`. This prevents unwanted formats from influencing download choices.

#### Custom Format Scoring for `TV`

-   **Penalized (-1500 score):**
    -   `Language Not Original`: Strongly penalizes any release that is not in the show's original language. This makes it highly unlikely to be grabbed unless no other options exist with a better score.
-   **Default Scores (from TRaSH Guides):**
    -   **Codec Preference:** Prefers `x264` releases with a score of `100`.
    -   **Unwanted:** Rejects BR-DISK, low-quality (LQ) releases, extras, AV1/x265(HD) codecs, and releases from bad dual-audio groups.
    -   **Release Quality:** Scores `Repack/Proper` releases to get fixes.
    -   **Source Preference:** Scores releases from high-quality WEB sources (Tiers 01-03) and Scene groups.
    -   **Streaming Services:** Scores releases from various streaming services (e.g., Netflix, HBO Max, DSNP).
-   **Informational (0 score):**
    -   A wide range of audio formats (DTS, Dolby Digital, Atmos, etc.) are tagged for visibility in the Sonarr UI but do not affect scoring.

### Quality Profile: `Anime`

This profile is specifically tuned for Anime series.

-   **Quality Priority:** Similar to the TV profile, it prioritizes `WEB 1080p` releases over Blu-ray to save space, followed by other resolutions.
-   **Upgrades:** Allows upgrades until a release is `WEB 1080p` with a score of `600`.
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
-   **Upgrades:** Allows upgrades until a score of `2350` is reached within the `1080p` quality group.
-   **Audio Preference:** This profile is explicitly configured to heavily penalize DTS and other high-bitrate or lossless audio codecs (e.g., DTS-HD MA, TrueHD). The primary reasons for this are the declining support for DTS formats and to ensure broader hardware compatibility.

#### Custom Format Scoring for `Movies 1080p`

-   **Penalized (-1500 score):**
    -   `DTS`, `DTS:X`, `DTS-HD MA`, `TrueHD`, `TrueHD ATMOS`: Strongly penalizes releases with these audio codecs, making them undesirable unless their other qualities result in a high overall score.
-   **Default Scores (from TRaSH Guides):**
    -   **Codec Preference:** Prefers `x264` with a score of `100`. `x265` is given a neutral score of `0`.
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
-   **Audio Preference:** This profile heavily penalizes high-bitrate and lossless audio formats (e.g., DTS, DTS-HD MA, TrueHD). This decision is driven by the declining support for DTS formats and to ensure broader hardware compatibility. 

#### Custom Format Scoring for `Movies 4k`

-   **Penalized (-1500 score):**
    -   `DTS`, `DTS:X`, `DTS-HD MA`, `TrueHD`, `TrueHD ATMOS`: Strongly penalizes releases with these high-resolution audio codecs, making them undesirable unless their other qualities (like being from a top-tier group) result in a high overall score.
-   **Default Scores (from TRaSH Guides):**
    -   **HDR Formats:** Strongly prefers releases with any form of HDR, including `HDR10+` and `Dolby Vision (DV)`.
    -   **Source Preference:** Prefers releases from top-tier UHD Blu-ray and WEB groups.
    -   **Unwanted:** Rejects BR-DISK, 3D, upscaled, LQ, and AV1/x265(HD) releases.
    -   **Audio Preference:** Prefers releases with `5.1 Surround` and `DD` audio.
-   **Informational (0 score):**
    -   Tags for special movie versions (`IMAX`, `Remaster`) and certain audio codecs (`FLAC`, `AAC`) are applied for visibility.
