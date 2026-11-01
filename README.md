# YouTubeSync – Jellyfin Plugin

Watch YouTube channels and playlists directly in Jellyfin — no downloading required.

The plugin syncs metadata and artwork from YouTube into your library and streams videos on demand using [yt-dlp](https://github.com/yt-dlp/yt-dlp).

> **Personal-use fork:** This fork is maintained for personal use only and is offered without support.

## Installation

Add the plugin repository in Jellyfin under **Dashboard → Plugins → Repositories → +** :

**Plugin manifest:** [manifest.json](https://raw.githubusercontent.com/alexdurai/jellyfin-youtube-plugin/main/manifest.json)

Then install **YouTubeSync** from the plugin catalogue and restart Jellyfin.

### Requirements

- **Jellyfin 12.x**
- **Latest yt-dlp** available on PATH (or configured in plugin settings); YouTube changes frequently and older versions may return empty channel feeds.
- **ffmpeg** (only needed for Enhanced playback mode)

The release catalog provides a plugin build for Jellyfin 12.x (`net10.0`). Open **Dashboard → YouTubeSync** for the standalone
plugin settings page.

If yt-dlp is installed with pip, update it with `python3 -m pip install -U "yt-dlp[default]"`.

## Getting started

1. Open the standalone **Dashboard → YouTubeSync** page.
2. Set the **Library folder** to a path inside one of your Jellyfin libraries (e.g. `/media/youtube`).
3. Set the **Jellyfin address** to the URL your playback devices use to reach the server.
4. Click **+ Add Channel or Playlist**, paste a YouTube URL, give it a name, and save.
5. The sync task runs automatically every 6 hours. You can also trigger it manually from **Dashboard → Scheduled Tasks**.

After sync, your YouTube content appears in Jellyfin organised by channel, season (year), and episode — complete with artwork and metadata. Videos whose publication date is unavailable are retained; the configured age limit cannot be applied to those entries.

## Playback modes

| Mode | What it does | Needs ffmpeg? |
|---|---|---|
| **Simple** (default) | Hands Jellyfin a direct YouTube stream URL. Lightweight and easy. | No |
| **Enhanced** | Re-streams through a local ffmpeg process for more consistent quality. Falls back to Simple automatically if anything goes wrong. | Yes |

You can switch between modes in the plugin settings at any time.

## Known limitations

- Simple mode may cap at 720p to keep playback stable across different clients.
- Enhanced mode currently outputs a single 1080p HLS profile.
- Age-restricted or members-only videos will not play (no cookie support yet).

## Build from source

```bash
dotnet publish Jellyfin.Plugin.YouTubeSync/Jellyfin.Plugin.YouTubeSync.csproj \
  -c Release --no-self-contained -o publish/
```

Copy both `publish/Jellyfin.Plugin.YouTubeSync.dll` and `publish/meta.json` into the same Jellyfin plugin folder, then restart the server. The project copies the valid Jellyfin 12 metadata into the publish directory; installing only the DLL can cause Jellyfin to create a default, malfunctioned plugin manifest.
