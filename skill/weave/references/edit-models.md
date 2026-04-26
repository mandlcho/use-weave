# Edit Image Models (Inpaint, Outpaint, Remove/Replace BG, Relight)

| Model | Credits | Required Inputs | Optional Inputs | Output Format | Best For | Limitations |
|-------|---------|-----------------|-----------------|---------------|----------|-------------|
| Gemini 3.1 Flash | 0.1 | Prompt, Image | — | PNG | Cheapest edit option, quick fixes | Basic quality |
| Gemini 3 Pro | 0.5 | Prompt, Image | — | PNG | Better quality than Flash, still cheap | — |
| Gemini 2.0 Flash | 0.1 | Prompt, Image | — | PNG | Cheapest edit, web search integration | Basic quality |
| GPT Image 1.5 Edit | 10 | Prompt, Image | Mask | PNG | Best semantic understanding, complex edits | Expensive |
| GPT Image 1 Edit | 8 | Prompt, Image | Mask | PNG | Strong semantic edits, scene understanding | — |
| Flux 2 Max | 10 | Prompt, Image | Mask | PNG | High quality inpainting | Expensive |
| Flux 2 Inpaint Klein 9B | 4 | Prompt, Image | Mask | PNG | Good value inpainting | — |
| Flux Dev LoRA Inpaint | 6 | Prompt, Image | LoRA, Mask | PNG | Custom style inpainting via LoRA | Requires LoRA |
| Flux Fill Pro | 6 | Prompt, Image | Mask | PNG | Clean inpainting, solid all-rounder | — |
| Flux Pro Outpaint | 6 | Prompt, Image | — | PNG | Extending image boundaries | Outpaint only |
| Runway Gen-4 Image | 8 | Prompt, Image | — | PNG | Runway quality editing | — |
| Seedream V5 Edit | varies | Prompt, Image | Mask | PNG | Latest Seedream editing | — |
| Seedream V4.5 Edit | varies | Prompt, Image | Mask | PNG | Proven editing quality, user-preferred | — |
| Seedream V4 Edit | varies | Prompt, Image | Mask | PNG | Older but stable | — |
| SeedEdit 3.0 | varies | Prompt, Image | Mask | PNG | Seed-based editing | — |
| Qwen Image Edit 2511 | varies | Prompt, Image | — | PNG | Multi-angle editing | — |
| Qwen Edit Image Plus | varies | Prompt, Image | — | PNG | Enhanced Qwen editing | — |
| Reve Edit | varies | Prompt, Image | — | PNG | Reference-based editing | — |
| Ideogram V3 Inpaint | varies | Image, Mask | Prompt | PNG | Text-aware inpainting | — |
| Ideogram V2 Inpaint | varies | Image, Mask | Prompt | PNG | Older Ideogram inpaint | — |
| SD3 Remove Background | varies | Image | — | PNG | Background removal | No prompt control |
| Bria Remove Background | varies | Image | — | PNG | Clean edge background removal | No prompt control |
| SD3 Content-Aware Fill | varies | Image, Mask | — | PNG | Automatic content-aware fill | No prompt guidance |
| Bria Content Aware-Fill | varies | Image, Mask | — | PNG | Bria's content fill | No prompt guidance |
| Replace Background | varies | Image | Prompt | PNG | Replace with prompted background | — |
| Bria Replace Background | varies | Image | Prompt | PNG | Bria's bg replacement | — |
| Relight 2.0 | varies | Image | — | PNG | Scene relighting | — |
| Kolors Virtual Try On | varies | Person Image, Garment Image | — | PNG | Clothing visualization on person | Specific input requirements |

## Quick Picks

- **Cheapest edits:** Gemini 2.0 Flash or 3.1 Flash (0.1 cr)
- **Best inpainting:** Flux Fill Pro (6 cr) or GPT Image 1 Edit (8 cr)
- **Best semantic edits:** GPT Image 1.5 Edit (10 cr)
- **Best outpainting:** Flux Pro Outpaint (6 cr)
- **Background removal:** Bria Remove Background (cleanest edges)
- **Relighting:** Relight 2.0
