# Generate from Image Models (Image-to-Image)

| Model | Credits | Required Inputs | Optional Inputs | Output Format | Aspect Ratios | Best For | Limitations |
|-------|---------|-----------------|-----------------|---------------|---------------|----------|-------------|
| Flux Dev Redux | varies | Image | Prompt | PNG, JPEG, WEBP | Standard | Base image-to-image generation | — |
| Flux ControlNet & LoRA | 10 | Image, Prompt | LoRA | PNG | Standard + Custom | Control-based generation with custom styles | Expensive, requires LoRA as link |
| Flux Canny Pro | varies | Image, Prompt | — | PNG | Standard | Edge-detection controlled generation | — |
| Flux Depth Pro | varies | Image, Prompt | — | PNG | Standard | Depth-map controlled generation | — |
| Qwen Edit Multiangle | varies | Image, Prompt | — | PNG | Match Input | Multi-angle generation, camera control options | — |
| Image to Image | 10 | Image, Prompt | — | PNG | Standard | Style transfer and variations | Expensive |
| Stable Diffusion ControlNets | varies | Image, Prompt | — | PNG | Standard | Traditional control-based approach | — |
| Sketch To Image | 0.1 | Sketch, Prompt | — | PNG | Standard | Turn rough sketch into full image | Sketch input required |

## Quick Picks

- **Cheapest:** Sketch To Image (0.1 cr)
- **Best for edge control:** Flux Canny Pro
- **Best for depth control:** Flux Depth Pro
- **Best for style transfer:** Image to Image (10 cr) or Flux ControlNet & LoRA (10 cr)
- **Multi-angle:** Qwen Edit Multiangle
