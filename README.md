# YoStream

Record once. Live to YouTube, Twitch, Kick, Facebook, and any RTMP destination.

You encode **once** (OBS). YoStream records the feed and fans it out.

```
OBS or encoder
        │  RTMP
        ▼
   MediaMTX (Docker)
        ├─ record → ./recordings
        ├─ HLS preview
        ├─ YouTube
        ├─ Twitch
        └─ Kick / Facebook / custom
```

## Run

You need Docker Desktop.

```bash
git clone https://github.com/herrinwill/yostream.git
cd yostream
docker compose up -d
```

### OBS

1. Settings → Stream
2. Service: **Custom**
3. Server: `rtmp://localhost:1935`
4. Stream key: `live`
5. Add an **audio** source. YouTube silently rejects video-only streams.
6. Start Streaming

HLS preview: `http://localhost:8888/live/index.m3u8`

## Destinations

Edit `mediamtx.yml` under `paths.live.forward` after you have keys. Do not commit stream keys.

```yaml
paths:
  live:
    record: yes
    forward:
      - dest: rtmps://a.rtmps.youtube.com:443/live2#YOUR_YT_KEY
      - dest: rtmp://live.twitch.tv/app#YOUR_TWITCH_KEY
```

Kick: paste the ingest URL from your Kick dashboard (host is unique per account).

MediaMTX hot-reloads the config file.

## Destination cheatsheet

| Platform | Ingest URL | Key from |
|---|---|---|
| YouTube | `rtmps://a.rtmps.youtube.com:443/live2` | Studio → Create → Go live → Streaming software |
| Twitch | `rtmp://live.twitch.tv/app` | Creator Dashboard → Settings → Stream |
| Kick | paste the URL Kick gives you | Kick dashboard → Stream |
| Facebook | `rtmps://live-api-s.facebook.com:443/rtmp/` | Live Producer |
| Custom | any `rtmp://` or `rtmps://` | that platform |

YouTube: create the live event in YouTube Studio (or use a stream key with auto-start) before or as you go live.

## Recording

`record: yes` on `paths.live`. Files land in `./recordings` as fragmented MP4.

## Ports

| Port | What |
|---|---|
| 1935 | OBS RTMP ingest |
| 8888 | HLS preview |
| 8889 | WHIP / WebRTC |
| 9997 | Control API |

## Safety

Stream keys are passwords. Keep them out of git.
