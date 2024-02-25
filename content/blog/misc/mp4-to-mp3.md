---
title: "Mp4 to Mp3"
date: 2024-02-25T12:13:46+05:30
---

Install ffmpeg

```powershell
choco install ffmpeg-full
```

Convert mp4 video to mp3. -vn means no video

```powershell
ffmpeg.exe -i input.mp4 -vn out.mp3
```

Tada!