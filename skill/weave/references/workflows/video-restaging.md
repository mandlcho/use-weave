---
task: Restage a video frame to create an "After" image
date_saved: 2026-04-26
models_used: [Seedream V4.5 Edit]
total_credits: varies
---

## Task
Extract a frame from video footage and generate a restaged "After" version of the scene.

## Node Graph
```
[Import Video] --> [Extract Video Frame] --> [Seedream V4.5 Edit] --> [Export]
                                                     |
                                                [Prompt: "..."]
```

## Models & Settings
- **Seedream V4.5 Edit** — use aspect ratio matching source video (typically 16:9)

## Prompts
- **Seedream V4.5 Edit prompt:** Describe the desired restaged scene — e.g., "Modern minimalist office, clean desk, natural daylight through large windows, photorealistic"
- **Negative prompt:** "blurry, low quality, distorted, watermark, artifacts"

## Notes
- Extract the specific frame you want to restage using timeline or frame number
- Match the aspect ratio of the original video for consistency
- The edit model preserves the composition while changing the scene content
