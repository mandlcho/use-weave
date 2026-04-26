# Editing Tools (Non-Generative Nodes — Free)

These nodes do NOT cost credits. They transform existing content.

| Node | Input | Output | What It Does | Key Parameters |
|------|-------|--------|--------------|----------------|
| Levels | Image or Video | Image or Video | Adjusts brightness, contrast, tonal range via histogram | Black point, gray point (midtones), white point |
| Compositor | Image, Video, or Text | Image or Video | Merges multiple visual elements together | Blend modes, layer ordering, translation, rotation |
| Painter | Image (optional) | Image + Mask | Hand-paint on inputs or blank canvas, mainly for creating masks | Aspect ratio, brush color, background color |
| Crop | Image or Video | Image or Video | Trims and reshapes inputs | Preset or custom dimensions |
| Resize | Image or Video | Image or Video | Changes aspect ratio by stretching/squashing | Custom dimensions |
| Blur | Image or Video | Image or Video | Applies blur effect | Fast Box Blur or Gaussian Blur, intensity control |
| Invert | Image or Video | Image or Video | Reverses color values, useful for inverting masks | — |
| Channels | Image or Video | 4 separate outputs | Separates into Red, Green, Blue, Alpha channels | — |
| Extract Video Frame | Video | Image | Extracts a single frame from video | Timeline selection or frame number/timecode |

## Common Uses

- **Masking workflow:** Painter → output mask to any edit model's mask input
- **Compositing:** Compositor to layer multiple results together with blend modes
- **Video frame work:** Extract Video Frame to pull stills for image processing
- **Color correction:** Levels for exposure/contrast adjustments
- **Channel isolation:** Channels to extract alpha or individual color channels
