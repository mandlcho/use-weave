---
task: Create a morph/transform transition between two frames
date_saved: 2026-04-26
models_used: [Kling 3]
total_credits: 60-440
---

## Task
Generate a smooth morph-in or transform-in video transition between a first and last frame using Kling's first/last frame feature.

## Node Graph
```
                   +--> [Extract Frame (first)] --+
[Import Video] -->|                                |--> [Kling First/Last Frame] --> [Export]
                   +--> [Extract Frame (last)]  --+             |
                                                           [Prompt: "..."]
```

## Models & Settings
- **Kling 3** — First/Last Frame mode, duration 3-5s for transitions, 16:9 or match source

## Prompts
- **Kling prompt:** "Smooth cinematic transformation, fluid motion, seamless morph transition"
- **Negative prompt:** "jerky, stuttering, glitch, artifact, jump cut"

## Notes
- Budget alternative: Seedance V1.5 Pro (25 cr) supports first/last frame too
- Shorter durations (3-5s) work better for transitions than longer ones
- Ensure first and last frames have compatible compositions for smooth morphing
