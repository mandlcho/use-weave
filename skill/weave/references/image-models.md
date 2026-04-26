# Image Models (Text-to-Image)

| Model | Credits | Required Inputs | Optional Inputs | Output Format | Aspect Ratios | Best For | Limitations |
|-------|---------|-----------------|-----------------|---------------|---------------|----------|-------------|
| Reve | 4 | Prompt | Reference Image | PNG | 1:1, 9:16, 16:9, 3:4, 4:3, 2:3, 3:2 | Multiple image references, style matching | — |
| Higgsfield Image | 21 | Prompt | Reference Image | PNG | 1152x2048, 2048x1152 | Different style options | Expensive |
| GPT Image 1 | 8 | Prompt | — | PNG, JPEG | 1024x1024, 1536x1024, 1024x1536 | Style and reference imitation, text in images | Fixed resolutions only |
| Imagen 4 | 6 | Prompt | Negative Prompt | PNG | 1:1, 9:16, 16:9, 3:4, 4:3 | Google's latest, high quality | No reference image input |
| Imagen 3 | 6 | Prompt | Negative Prompt | PNG | 1:1, 9:16, 16:9, 3:4, 4:3 | Solid general-purpose | No reference image input |
| Imagen 3 Fast | 3 | Prompt | Negative Prompt | PNG | 1:1, 9:16, 16:9, 3:4, 4:3 | Quick drafts, half price of Imagen 3 | Lower quality than Imagen 3 |
| Flux 2 Pro | 5 | Prompt | Image | PNG | Standard + Custom | Multiple image references, good prompt adherence | — |
| Flux 2 Flex | 14 | Prompt | Image | PNG | Standard + Custom | Multiple image references, flexible | Expensive |
| Flux 2 Dev LoRA | 4 | Prompt | LoRA, Weight, Image | PNG, JPEG | Standard + Custom | Custom styles via LoRA, multiple references | Requires LoRA file |
| Flux 1.1 Ultra | 7 | Prompt | Image | PNG, JPEG | 1:1, 21:9, 9:21 | Ultra-wide aspect ratios | — |
| Flux Pro 1.1 | 5 | Prompt | Image | PNG, JPEG, WEBP | 1:1, 16:9, 9:16 | Good general-purpose, WEBP support | — |
| Flux Fast | 0.4 | Prompt | — | PNG, JPEG, WEBP | 1:1, 21:9, 9:21 | Cheapest option for quick drafts | No image input, lower quality |
| Flux Dev LoRA | 4 | Prompt | Image, LoRA, Weight | PNG, JPEG | 1:1, 16:9, 9:16 | Multiple LoRA input, custom styles | Requires LoRA file |
| Recraft V3 | 5 | Prompt | Style/Control Images | PNG | 1024x1024+ | Baked-in styles, design-focused | — |
| Mystic | 12 | Prompt | — | PNG | 1K, 2K, 4K | High resolution up to 4K | Expensive, no image input |
| Ideogram V3 | 4 | Prompt | Remix, Negative Prompt | PNG | 1:1, 3:4, 9:16, 16:9, 4:3 | Text rendering in images, different styles | — |
| Ideogram V3 Character | 15 | Prompt | Character Reference, Mask | PNG | 1:1, 3:4, 9:16, 16:9, 4:3 | Character-specific generation, consistent characters | Very expensive |
| Stable Diffusion 3.5 | 8 | Prompt | Image | PNG, JPEG, WEBP | Extensive options | Wide aspect ratio support, image input | Mid-range pricing |
| Minimax Image 01 | 1 | Prompt | — | PNG | 1:1, 16:9, 9:16, 4:3, 3:4 | Cheapest generative model (1 credit) | No optional inputs |
| Bria | 6 | Prompt | Negative Prompt | PNG | 1:1, 16:9, 9:16 | Commercial-safe outputs | — |
| Dalle 3 | 5 | Prompt | — | PNG | 1:1 | OpenAI quality, good prompt understanding | 1:1 only |
| Luma Photon | 2 | Prompt | Reference, Style Reference | PNG | 1:1, 16:9, 9:16, 3:4, 4:3, 9:21, 21:9 | Very cheap, multiple references, wide ratio support | — |
| Nvidia Sana | 0.2 | Prompt | Negative Prompt | PNG | — | Absolute cheapest (0.2 credits) | Lower quality |
| Nvidia Consistory | 5 | Multiple prompts (Subject, Scene 1/2, Style) | Negative, Scene Prompt | PNG | 1:1 | Multi-prompt subject/scene control, subject tokens | 1:1 only |

## Quick Picks

- **Cheapest:** Nvidia Sana (0.2 cr) or Flux Fast (0.4 cr)
- **Best value:** Luma Photon (2 cr) or Ideogram V3 (4 cr)
- **Best quality:** GPT Image 1 (8 cr) or Imagen 4 (6 cr)
- **Best for custom styles:** Flux 2 Dev LoRA (4 cr) or Flux Dev LoRA (4 cr)
- **Best for characters:** Ideogram V3 Character (15 cr)
- **Best for text in images:** Ideogram V3 (4 cr) or GPT Image 1 (8 cr)
