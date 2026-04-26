---
task: Remove unwanted objects from a scene (buttons, UI, elements)
date_saved: 2026-04-26
models_used: [Flux Fill Pro]
total_credits: 6
---

## Task
Remove unwanted objects (like buttons, UI elements, or other distractions) from an image using masking and inpainting.

## Node Graph
```
[Import Image] --> [Painter] --+--(image)--> [Flux Fill Pro] --> [Export]
                               +--(mask)--/        |
                                              [Prompt: "Clean background, seamless"]
```

## Models & Settings
- **Flux Fill Pro** (6 cr) — solid all-round inpainting
- Alternative budget: Gemini 2.0 Flash Edit (0.1 cr) for simple removals
- Alternative quality: GPT Image 1 Edit (8 cr) for complex scene understanding

## Prompts
- **Flux Fill Pro prompt:** "Clean, seamless background matching surrounding area"
- For specific replacements: describe what should fill the space instead

## Notes
- Paint the mask carefully over the object to remove — the mask output of Painter feeds into the edit model's mask input
- For small objects (buttons, icons), a rough mask is usually sufficient
- For larger objects, use a more precise mask and consider GPT Image 1 Edit for better scene understanding
