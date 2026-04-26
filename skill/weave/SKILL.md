---
name: weave
description: "Use when the user mentions Weave, Weavy, Figma Weave, or asks about node-based AI image/video generation workflows. Helps with node selection, model comparison, workflow design, prompt writing, and building node graphs."
---

# Weave Skill

## Platform Overview

Weave (formerly WeavyAI, now Figma Weave) is a web-based node-based AI creative tool for building image and video generation workflows.

### Node System
- Nodes have **inputs on the left** and **outputs on the right**
- Connect nodes by dragging from output to input
- **Generative nodes cost credits**; non-generative nodes are free
- Right-click anywhere on the canvas or press **Tab** to add a node
- Node parameters appear in the **right panel** when a node is selected

### Color System
| Color | Meaning |
|-------|---------|
| Green | Image |
| Purple | Text / LoRA |
| Red | Video |
| Blue | Array / List / 3D |
| White | Multiple input types |
| Lime | Mask |

---

## How to Respond

When a user asks about Weave, follow this workflow:

1. **Check saved workflows first** — look in `~/.claude/skills/weave/references/workflows/` for any previously saved workflow that matches the task
2. **Identify the task** — understand what the user is trying to accomplish (remove object, generate b-roll, morph transition, etc.)
3. **Look up the task-to-node mapping** — use the table below to identify the correct nodes
4. **Recommend models with budget vs quality options** — load the relevant reference file for that model category (see Model Selection Logic section)
5. **Draw an ASCII node graph** — visually show the workflow
6. **Write a suggested prompt** — optimized for the chosen model

---

## Task-to-Node Mapping

| Task | Nodes | Recommended Model(s) | Notes |
|------|-------|----------------------|-------|
| Remove object from scene | Painter (mask) + Edit model | Budget: Gemini 2.0 Flash Edit (0.1 cr), Quality: Flux Fill Pro (6 cr), Semantic: GPT Image 1 Edit (8 cr) | Paint mask over object, prompt what to replace with |
| Restage a video frame ("After" image) | Extract Video Frame + Image Edit model | Seedream V4.5 Edit | Pull frame, generate restaged version |
| Morph/transform transition | Extract Frame (x2) + Video model | Kling 3 First/Last Frame (60-440 cr), Budget: Seedance V1.5 Pro (25 cr) | Extract first and last frames as inputs |
| Generate b-roll / detail shots | Image generation model | Seedream V4.5 | Prompt with scene details for alternating shots |
| Crop out character (alpha) | Merge Alpha | — | Outputs image with transparency |
| Upscale image | Enhance image model | Budget: Recraft Crisp (5 cr), Quality: Magnific Upscale (12 cr), Premium: Topaz (19 cr) | Magnific supports 2x/4x/8x |
| Upscale video | Enhance video model | Budget: Topaz Video (12 cr), Quality: Real-ESRGAN (71 cr) | Topaz outputs 4K |
| Smooth video / change FPS | Video Smoother | — (5 cr) | Cheapest video enhancement |
| Remove background | SD3 Remove Background or Bria Remove Background | Bria for clean edges | — |
| Replace background | Replace Background or Bria Replace Background | — | Accepts new background input |
| Relight a scene | Relight 2.0 | — | Adjusts lighting on existing image |
| Virtual try-on (clothing) | Kolors Virtual Try On | — | Needs person image + garment image |
| Batch process multiple prompts | Text Iterator + any generative model | — | Supports CSV import |
| Batch process multiple images | Image Iterator + any generative model | — | Keeps each input separated |
| Batch process multiple videos | Video Iterator + any generative model | — | Same as image iterator for video |
| Composite/layer images | Compositor | — (free) | Blend modes, layer ordering, rotation |
| Create hand-painted mask | Painter | — (free) | Outputs both image and mask |
| Describe an image as prompt | Image Describer | Choose LLM in settings | Generates prompt from image analysis |
| Describe a video as prompt | Video Describer | Choose LLM in settings | Generates prompt from video analysis |
| Enhance prompt quality | Prompt Enhancer | Choose LLM in settings | Makes prompts clearer for models |
| Combine multiple prompts | Prompt Concatenator | — (free) | Use + button to add inputs |
| Ask an LLM a question | Run Any LLM | — | Text and image inputs, text output |
| Compare two results | Compare Node | — (free) | Side-by-side slider comparison |
| Generate 3D model from image | 3D generation model | Budget: Trellis (2 cr), Quality: Rodin V2 (36 cr) | — |
| Extract single frame from video | Extract Video Frame | — (free) | Timeline or frame number selection |
| Adjust brightness/contrast | Levels | — (free) | Shadows, midtones, highlights |
| Separate color channels | Channels | — (free) | R, G, B, Alpha output |
| Sketch to image | Sketch To Image | — (0.1 cr) | Draw rough sketch, get generated image |
| Generate from edge/depth map | Flux Canny Pro / Flux Depth Pro | — | Control-based generation |

---

## Common Workflows

### Video Restaging
```
[Import Video] --> [Extract Video Frame] --> [Seedream V4.5 Edit] --> [Export]
                                                     |
                                                [Prompt: "..."]
```

### First/Last Frame Morph Transition
```
                   +--> [Extract Frame (first)] --+
[Import Video] -->|                                |--> [Kling First/Last Frame] --> [Export]
                   +--> [Extract Frame (last)]  --+             |
                                                           [Prompt: "..."]
```

### B-Roll Generation
```
[Import Video] --> [Extract Video Frame] --> [Seedream V4.5] --> [Export]
                                                    |
                                          [Prompt: "Close-up of..."]
```

### Object Removal
```
[Import Image] --> [Painter] --+--(image)--> [Flux Fill Pro / GPT Image 1 Edit] --> [Export]
                               +--(mask)--/              |
                                                    [Prompt: "Clean background"]
```

### Character Extraction
```
[Import Image] --> [Merge Alpha] --> [Preview]
                                 --> [Export]
```

---

## Model Selection Logic

When recommending a model, consider:
- **Credit cost** — balance quality against budget
- **Input compatibility** — does the model accept the required input type (image, video, mask, etc.)?
- **Output format** — image vs video, resolution, aspect ratio support
- **Aspect ratio** — some models have fixed ratios or limits
- **Strengths** — photorealism, stylization, coherence, motion quality
- **Limitations** — max resolution, generation time, credit cost at scale

Always format recommendations as **budget / quality / alternative** picks.

### Reference Files
Load only the reference file(s) relevant to the current question. Do NOT load all reference files at once. Only load what is relevant to the question.

| Category | Reference File |
|----------|---------------|
| Image generation models | `~/.claude/skills/weave/references/image-models.md` |
| Video generation models | `~/.claude/skills/weave/references/video-models.md` |
| Edit models (inpainting, outpainting) | `~/.claude/skills/weave/references/edit-models.md` |
| Generate from image models | `~/.claude/skills/weave/references/generate-from-image.md` |
| Enhance image models | `~/.claude/skills/weave/references/enhance-image.md` |
| Enhance video models | `~/.claude/skills/weave/references/enhance-video.md` |
| 3D generation models | `~/.claude/skills/weave/references/3d-models.md` |
| Editing tools (non-generative) | `~/.claude/skills/weave/references/editing-tools.md` |
| Text tools | `~/.claude/skills/weave/references/text-tools.md` |
| Helper nodes | `~/.claude/skills/weave/references/helpers.md` |

---

## Prompt Generation

When writing prompts for the user, follow these rules:

1. **Optimize for the model** — different models respond differently to prompt style (e.g., Flux prefers natural language, SDXL prefers comma-separated tags)
2. **Be specific** — include subject, setting, lighting, style, mood, and camera angle where relevant
3. **Match the task** — for inpainting/edit prompts, describe what should appear in the masked area, not what to remove
4. **Include a negative prompt** when the model supports it — common negatives: `blurry, low quality, distorted, artifacts, watermark, text, extra limbs`
5. **Format output as:**

```
Suggested prompt:
"[prompt text here]"

Negative prompt:
"[negative prompt text here]"
```

---

## Node Graph Rendering

Rules for drawing ASCII node graphs:

- Wrap each node name in square brackets: `[Node Name]`
- Use `-->` for directed flow between nodes
- Use `|` for vertical connections where needed
- Label ambiguous connections (e.g., `--(mask)-->`, `--(image)-->`)
- Use `+-->` or branch lines for nodes with multiple outputs feeding into one node
- Keep graphs to a maximum of ~6 nodes wide for readability
- Always show where the prompt connects to a generative node

---

## Self-Learning: Saving Workflows

When the user confirms a workflow worked, offer to save it. Save to:

`~/.claude/skills/weave/references/workflows/[kebab-case-task-name].md`

**File naming:** Use kebab-case describing the task (e.g., `object-removal-flux-fill.md`, `video-restage-seedream.md`)

**File format:**

```markdown
---
task: [short task description]
date_saved: [YYYY-MM-DD]
models_used: [comma-separated list]
total_credits: [estimated credits per run]
---

## Task
[What this workflow accomplishes]

## Node Graph
[ASCII node graph]

## Models & Settings
[Model names, key parameter values used]

## Prompts
[Prompts that worked well]

## Notes
[Any quirks, tips, or caveats discovered]
```

---

## Self-Learning: Updating References

When the user says "update Weave models", fetch the following URLs and diff against the existing reference files. Update only what has changed.

| Reference File | Source URL |
|---------------|------------|
| `~/.claude/skills/weave/references/image-models.md` | https://help.weavy.ai/en/articles/12284752-image-models-comparison |
| `~/.claude/skills/weave/references/video-models.md` | https://help.weavy.ai/en/articles/12344226-video-models-comparison |
| `~/.claude/skills/weave/references/edit-models.md` | https://help.weavy.ai/en/articles/12343904-edit-image-models-comparison |
| `~/.claude/skills/weave/references/generate-from-image.md` | https://help.weavy.ai/en/articles/12344174-generate-from-image-models-comparison |
| `~/.claude/skills/weave/references/enhance-image.md` | https://help.weavy.ai/en/articles/12344205-enhance-images-models-comparison |
| `~/.claude/skills/weave/references/enhance-video.md` | https://help.weavy.ai/en/articles/12344342-enhance-video-models-comparison |
| `~/.claude/skills/weave/references/3d-models.md` | https://help.weavy.ai/en/articles/12344357-3d-models-comparison |
