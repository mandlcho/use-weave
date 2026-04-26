# Weave Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a modular Claude Code skill that teaches Claude how to use Figma Weave for node-based AI image/video generation workflows.

**Architecture:** A main skill file (`SKILL.md`) containing trigger logic, platform overview, task-to-node mapping, workflow patterns, prompt generation rules, model selection logic, and self-learning features. Ten reference files provide detailed model/node comparison tables loaded on-demand. A `workflows/` directory stores confirmed user workflows as a personal recipe book.

**Tech Stack:** Claude Code skills (markdown with YAML frontmatter), installed at `~/.claude/skills/weave/`

---

### Task 1: Create Main Skill File

**Files:**
- Create: `~/.claude/skills/weave/SKILL.md`

- [ ] **Step 1: Create the skill directory**

```bash
mkdir -p ~/.claude/skills/weave/references/workflows
```

- [ ] **Step 2: Write the main skill file**

Create `~/.claude/skills/weave/SKILL.md` with the full content below:

```markdown
---
name: weave
description: Use when the user mentions Weave, Weavy, Figma Weave, or asks about node-based AI image/video generation workflows. Helps with node selection, model comparison, workflow design, prompt writing, and building node graphs.
---

# Figma Weave Assistant

## Platform Overview

Figma Weave (formerly WeavyAI) is a web-based node-based AI creative tool at https://app.weavy.ai/. Key concepts:

- **Nodes** are functions with inputs (left) and outputs (right). Connect by dragging output handles to input handles.
- **Generative nodes** have a Run button and cost credits.
- **Non-generative nodes** are free — they transform content (crop, blur, composite, etc.).
- **Node color system:**
  - Green = Image nodes
  - Purple = Text nodes / LoRA
  - Red = Video nodes
  - Blue = Array / List / 3D nodes
  - White = Multiple input options
  - Lime = Mask nodes
- **Adding nodes:** Right-click canvas or press Tab to search.
- **Parameters:** Select a node → settings appear in the right panel.

## How to Respond

When the user asks about Weave, follow this order:

1. **Check saved workflows first.** Read files in `~/.claude/skills/weave/references/workflows/` to see if a matching workflow already exists. If found, present it as "you've done this before — here's what worked" and offer to modify.
2. **Identify the task** — what is the user trying to accomplish?
3. **Look up the task-to-node mapping** below to find the right nodes and models.
4. **Recommend models** with budget vs. quality options (load the relevant reference file for detailed comparison).
5. **Draw the ASCII node graph** showing how to wire everything.
6. **Write a suggested prompt** ready to paste into Weave's Prompt node.

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

## Common Workflows

**Video Restaging:**
```
[Import Video] --> [Extract Video Frame] --> [Seedream V4.5 Edit] --> [Export]
                                                     |
                                                [Prompt: "..."]
```

**First/Last Frame Morph Transition:**
```
                   +--> [Extract Frame (first)] --+
[Import Video] -->|                                |--> [Kling First/Last Frame] --> [Export]
                   +--> [Extract Frame (last)]  --+             |
                                                           [Prompt: "..."]
```

**B-Roll Generation:**
```
[Import Video] --> [Extract Video Frame] --> [Seedream V4.5] --> [Export]
                                                    |
                                          [Prompt: "Close-up of..."]
```

**Object Removal:**
```
[Import Image] --> [Painter] --+--(image)--> [Flux Fill Pro / GPT Image 1 Edit] --> [Export]
                               +--(mask)--/              |
                                                    [Prompt: "Clean background"]
```

**Character Extraction:**
```
[Import Image] --> [Merge Alpha] --> [Preview]
                                 --> [Export]
```

## Model Selection Logic

When recommending a model, consider:

1. **Credit cost** — always present budget and quality options
2. **Input compatibility** — does the model accept the data type being fed?
3. **Output format** — PNG vs JPEG, resolution, video duration
4. **Aspect ratio support** — does it support the target ratio?
5. **Strengths** — check the "Best For" column in the relevant reference file
6. **Limitations** — check "Limitations" column

Format recommendations as:
- **Budget pick:** model name (X credits) — why
- **Best quality:** model name (X credits) — why
- **Alternative:** model name (X credits) — when to use instead

For detailed model comparison, read the relevant reference file:
- Image generation → `~/.claude/skills/weave/references/image-models.md`
- Video generation → `~/.claude/skills/weave/references/video-models.md`
- Image editing (inpaint, outpaint, remove/replace bg) → `~/.claude/skills/weave/references/edit-models.md`
- Image-to-image (ControlNet, Redux, style transfer) → `~/.claude/skills/weave/references/generate-from-image.md`
- Image upscaling/enhancement → `~/.claude/skills/weave/references/enhance-image.md`
- Video upscaling/enhancement → `~/.claude/skills/weave/references/enhance-video.md`
- 3D generation → `~/.claude/skills/weave/references/3d-models.md`
- Non-generative editing nodes → `~/.claude/skills/weave/references/editing-tools.md`
- Prompt/text/LLM nodes → `~/.claude/skills/weave/references/text-tools.md`
- Import, export, router, iterators → `~/.claude/skills/weave/references/helpers.md`

Do NOT load all reference files at once. Only load what is relevant to the question.

## Prompt Generation

Every workflow recommendation involving a generative model MUST include a suggested prompt ready to paste into Weave's Prompt node.

**Rules:**
- Write prompts optimized for the specific model (image vs. video models prompt differently)
- Be descriptive and specific: subject, style, lighting, mood, composition, detail level
- Include negative prompt when the model supports it
- Keep prompts concise but rich — no filler words

**Format:**
```
Suggested prompt: "Description here"

Negative prompt: "blurry, low quality, distorted, watermark"
```

## Node Graph Rendering

Every workflow recommendation MUST include an ASCII node graph.

**Rules:**
- Each node: `[Node Name]`
- Horizontal flow: `-->`
- Vertical connections: `|`
- Label ambiguous connections: `--(image)-->`, `--(mask)-->`, `--(text)-->`
- Show prompt nodes connected to their targets
- Parallel paths: `+-->`
- Max ~6 nodes wide before wrapping

## Self-Learning: Saving Workflows

When the user confirms a workflow worked ("that worked", "perfect", "saving this", or explicitly asks to save), save it to `~/.claude/skills/weave/references/workflows/` using this format:

**File name:** kebab-case task summary, e.g., `object-removal-flux-fill.md`

```markdown
---
task: Short description of what this workflow solves
date_saved: YYYY-MM-DD
models_used: [Model1, Model2]
total_credits: estimated cost per run
---

## Task
What this workflow accomplishes.

## Node Graph
(ASCII node graph here)

## Models & Settings
- **Model Name** — key settings used

## Prompts
- **Model prompt:** "The prompt that worked"
- **Negative prompt:** "If applicable"

## Notes
Tips, gotchas, or variations discovered.
```

## Self-Learning: Updating References

When the user says "update Weave models", "refresh Weave references", or similar:

1. Fetch these pages from help.weavy.ai:
   - https://help.weavy.ai/en/articles/12284752-image-models-comparison
   - https://help.weavy.ai/en/articles/12344226-video-models-comparison
   - https://help.weavy.ai/en/articles/12343904-edit-image-models-comparison
   - https://help.weavy.ai/en/articles/12344174-generate-from-image-models-comparison
   - https://help.weavy.ai/en/articles/12344205-enhance-images-models-comparison
   - https://help.weavy.ai/en/articles/12344342-enhance-video-models-comparison
   - https://help.weavy.ai/en/articles/12344357-3d-models-comparison
2. Diff fetched data against current reference files
3. Report changes: "Found X new models, Y price changes, Z removed"
4. On user confirmation, update the reference files
5. Commit with a message listing what changed
```

- [ ] **Step 3: Verify the file exists and has correct frontmatter**

```bash
head -5 ~/.claude/skills/weave/SKILL.md
```

Expected output:
```
---
name: weave
description: Use when the user mentions Weave, Weavy, Figma Weave, or asks about node-based AI image/video generation workflows. Helps with node selection, model comparison, workflow design, prompt writing, and building node graphs.
---
```

- [ ] **Step 4: Commit**

```bash
git add ~/.claude/skills/weave/SKILL.md
git commit -m "feat: add main Weave skill file with task mapping, workflows, and self-learning"
```

---

### Task 2: Create Image Models Reference

**Files:**
- Create: `~/.claude/skills/weave/references/image-models.md`

- [ ] **Step 1: Write the image models reference file**

Create `~/.claude/skills/weave/references/image-models.md` with this content:

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add ~/.claude/skills/weave/references/image-models.md
git commit -m "feat: add image models reference table"
```

---

### Task 3: Create Video Models Reference

**Files:**
- Create: `~/.claude/skills/weave/references/video-models.md`

- [ ] **Step 1: Write the video models reference file**

Create `~/.claude/skills/weave/references/video-models.md` with this content:

```markdown
# Video Models (Text/Image-to-Video)

| Model | Credits | Required Inputs | Optional Inputs | Duration | Aspect Ratios | Audio | Best For | Limitations |
|-------|---------|-----------------|-----------------|----------|---------------|-------|----------|-------------|
| Kling 3 | 60-440 | Prompt | First/Last Frame, Negative Prompt | 3-15s | 16:9, 9:16, 1:1 | Yes | Best motion quality, first/last frame morphs, longest duration | Expensive at higher tiers |
| Sora 2 | 96-288 | Prompt | First Frame | 4/8/12s | 720x1280, 1280x720 | Yes | Best physics simulation, consistency | Most expensive, fixed resolutions |
| Seedance V1.5 Pro | 25-74 | Prompt | First/Last Frame | 4-12s | 16:9, 9:16, 1:1, 21:9, 3:4 | No | Good value for first/last frame, wide ratio support | No audio |
| Wan 2.5 | 55 | Prompt | Image, Negative Prompt | Flexible | Standard | No | Budget-friendly, flexible duration | No audio, no first/last frame |
| Grok Imagine Video | 36-90 | Prompt | Image | 1-15s | Standard | No | Wide duration range | No negative prompt |
| LTX 2 Video | 50-280 | Prompt | — | 6/8/10s | Standard | Yes | Audio generation, tiered quality/speed | Fixed durations |
| Moonvalley | 165-330 | Prompt | — | 5/10s | 1920x1080, 2560x1440, 3840x2160 | Yes | High resolution up to 4K, audio | Very expensive |
| Runway Gen-4.5 | 70-140 | Prompt | — | 5-10s | Multiple | Yes | Audio generation, multiple ratios | Expensive |
| Runway Gen-4 Turbo | 30-60 | Prompt | First Frame | 5-10s | Multiple | No | Budget Runway option | First frame only, no audio |
| Pixverse V4.5 | 60-90 | Prompt | First/Last Frame, Negative Prompt | 5-10s | Standard | No | First/last frame + negative prompt | No audio |
| Luma Ray 2 | 108-194 | Prompt | — | 5-9s | Standard | No | Camera concepts for dynamic movement | Expensive, no image input |
| Minimax Video 01 | 60 | Prompt | — | 5-10s | Flexible | No | Flat pricing, flexible ratios | No image input |
| Hunyuan | 60 | Prompt | First Frame (required) | Flexible | Standard | No | Flexible duration | First frame required |
| Skyreels | 36 | Prompt | — | 5s | Standard | No | Cheap, simple prompt-based | 5s only |
| Wan Video | 24 | Prompt | — | Flexible | Flexible | No | Cheapest video model | Prompt-only, basic quality |
| Higgsfield Video | 14-62 | Prompt | — | Flexible | Flexible | No | Budget tier, VFX features | Basic quality |

## Quick Picks

- **Cheapest:** Wan Video (24 cr) or Higgsfield Video (14 cr)
- **Best value:** Seedance V1.5 Pro (25 cr) or Skyreels (36 cr)
- **Best for morphs/transitions:** Kling 3 (first/last frame support)
- **Best quality:** Sora 2 (96+ cr) or Moonvalley (165+ cr)
- **Best with audio:** Kling 3, Sora 2, LTX 2, Moonvalley, Runway Gen-4.5
- **Longest duration:** Kling 3 (up to 15s) or Grok (1-15s)
- **Best camera control:** Luma Ray 2
```

- [ ] **Step 2: Commit**

```bash
git add ~/.claude/skills/weave/references/video-models.md
git commit -m "feat: add video models reference table"
```

---

### Task 4: Create Edit Models Reference

**Files:**
- Create: `~/.claude/skills/weave/references/edit-models.md`

- [ ] **Step 1: Write the edit models reference file**

Create `~/.claude/skills/weave/references/edit-models.md` with this content:

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add ~/.claude/skills/weave/references/edit-models.md
git commit -m "feat: add edit models reference table"
```

---

### Task 5: Create Generate-from-Image Reference

**Files:**
- Create: `~/.claude/skills/weave/references/generate-from-image.md`

- [ ] **Step 1: Write the generate-from-image reference file**

Create `~/.claude/skills/weave/references/generate-from-image.md` with this content:

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add ~/.claude/skills/weave/references/generate-from-image.md
git commit -m "feat: add generate-from-image models reference table"
```

---

### Task 6: Create Enhance Image Reference

**Files:**
- Create: `~/.claude/skills/weave/references/enhance-image.md`

- [ ] **Step 1: Write the enhance image reference file**

Create `~/.claude/skills/weave/references/enhance-image.md` with this content:

```markdown
# Enhance Image Models (Upscale, Sharpen, Skin)

| Model | Credits | Required Inputs | Optional Inputs | Output Format | Best For | Limitations |
|-------|---------|-----------------|-----------------|---------------|----------|-------------|
| Topaz Upscale | 19 | Image | — | PNG, JPEG | Professional-grade upscaling | Expensive, 1:1 output ratio |
| Topaz Sharpen | 19 | Image | — | PNG, JPEG, JPG, TIFF, TIF | Professional sharpening | Expensive, 1:1 output ratio |
| Recraft Crisp Upscale | 5 | Image | — | PNG | Budget upscaling, good quality/price ratio | 1:1 output ratio |
| Magnific Skin Enhancer | 18 | Image | Prompt | PNG, JPEG | Skin detail enhancement (portraits) | Expensive, specialized |
| Magnific Upscale | 12 | Image | Prompt | PNG, JPEG | 2x/4x/8x scaling options, prompt-guided | Mid-range pricing |
| Magnific Precision Upscale | 18 | Image | — | PNG | Details and sharpening controls | Expensive |
| Magnific Precision Upscale V2 | 18 | Image | — | PNG | Details and grain adjustments | Expensive |
| Enhancor Image Upscale | 36 | Image | — | PNG | Preserves original aspect ratio | Most expensive |
| Enhancor Realistic Skin | 36 | Image | — | PNG | Enhancement modes, skin detail controls | Most expensive, specialized |

## Quick Picks

- **Cheapest:** Recraft Crisp Upscale (5 cr)
- **Best value:** Magnific Upscale (12 cr) — supports 2x/4x/8x with prompt guidance
- **Best quality:** Topaz Upscale (19 cr) or Enhancor (36 cr)
- **Skin/portraits:** Magnific Skin Enhancer (18 cr) or Enhancor Realistic Skin (36 cr)
- **Sharpening:** Topaz Sharpen (19 cr)
```

- [ ] **Step 2: Commit**

```bash
git add ~/.claude/skills/weave/references/enhance-image.md
git commit -m "feat: add enhance image models reference table"
```

---

### Task 7: Create Enhance Video Reference

**Files:**
- Create: `~/.claude/skills/weave/references/enhance-video.md`

- [ ] **Step 1: Write the enhance video reference file**

Create `~/.claude/skills/weave/references/enhance-video.md` with this content:

```markdown
# Enhance Video Models (Upscale, Smooth)

| Model | Credits | Required Inputs | Output Resolution | Best For | Limitations |
|-------|---------|-----------------|-------------------|----------|-------------|
| Topaz Video Upscaler | 12 | Video | 4K | Best value video upscaling, 4K output | — |
| Real-ESRGAN Video Upscaler | 71 | Video | 2K / 4K | Mid-range with 2K and 4K options | Expensive |
| Bria Video Upscale | 154 | Video | 2K | — | Most expensive, does not accept MOV with Alpha |
| Video Smoother | 5 | Video | As Original | FPS adjustment, smoothing | No upscaling |

## Quick Picks

- **Cheapest:** Video Smoother (5 cr) — for FPS/smoothing only
- **Best upscale value:** Topaz Video Upscaler (12 cr) — 4K output
- **Best quality upscale:** Real-ESRGAN (71 cr) — 2K/4K options
- **Avoid:** Bria Video Upscale (154 cr) — expensive for 2K only
```

- [ ] **Step 2: Commit**

```bash
git add ~/.claude/skills/weave/references/enhance-video.md
git commit -m "feat: add enhance video models reference table"
```

---

### Task 8: Create 3D Models Reference

**Files:**
- Create: `~/.claude/skills/weave/references/3d-models.md`

- [ ] **Step 1: Write the 3D models reference file**

Create `~/.claude/skills/weave/references/3d-models.md` with this content:

```markdown
# 3D Models (Image-to-3D)

| Model | Credits | Required Inputs | Optional Inputs | Best For | Limitations |
|-------|---------|-----------------|-----------------|----------|-------------|
| Trellis | 2 | Image | — | Cheapest 3D, texture size and mesh simplification controls | Basic quality |
| Sam 3D Objects | 3 | Image, Prompt | — | Materials support, T-pose mode | — |
| Hunyuan 3D | 18 | Front Image | Back, Left, Right Images | Multi-view input, target face number control | Needs multiple views for best results |
| Hunyuan 3D V2.1 | 25 | Image | — | Textured mesh output | Single view only |
| Trellis 3D V2 | 30 | Image | — | Topology and remesh controls | — |
| Rodin V2 | 36 | Image | Prompt | Materials, quality mesh options | — |
| Rodin | 48 | Image | Prompt | Materials, quality mesh, prompt guidance | Expensive |
| Hunyuan 3D V3 | 80 | Input Image | Back, Left, Right Images | Polygon type options, latest Hunyuan | Expensive |
| Meshy V6 | 96 | Image | Texture Prompt, Texture Image | Topology, remesh, texture customization | Most expensive |

## Quick Picks

- **Cheapest:** Trellis (2 cr)
- **Best value:** Sam 3D Objects (3 cr) or Hunyuan 3D (18 cr)
- **Best quality:** Rodin V2 (36 cr) or Meshy V6 (96 cr)
- **Multi-view:** Hunyuan 3D (18 cr) or Hunyuan 3D V3 (80 cr)
```

- [ ] **Step 2: Commit**

```bash
git add ~/.claude/skills/weave/references/3d-models.md
git commit -m "feat: add 3D models reference table"
```

---

### Task 9: Create Editing Tools Reference

**Files:**
- Create: `~/.claude/skills/weave/references/editing-tools.md`

- [ ] **Step 1: Write the editing tools reference file**

Create `~/.claude/skills/weave/references/editing-tools.md` with this content:

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add ~/.claude/skills/weave/references/editing-tools.md
git commit -m "feat: add editing tools reference"
```

---

### Task 10: Create Text Tools Reference

**Files:**
- Create: `~/.claude/skills/weave/references/text-tools.md`

- [ ] **Step 1: Write the text tools reference file**

Create `~/.claude/skills/weave/references/text-tools.md` with this content:

```markdown
# Text Tools (Prompt & LLM Nodes)

| Node | Credits | Input | Output | What It Does | Key Parameters |
|------|---------|-------|--------|--------------|----------------|
| Prompt | Free | — | Text | Basic text prompt input, no length constraints | — |
| Prompt Concatenator | Free | Multiple text inputs | Text | Combines multiple text inputs into one output | + button to add inputs, custom text between them |
| Prompt Enhancer | Varies | Text | Text | Refines prompts to be clearer for image/video models | LLM model selection, instructions text box |
| Run Any LLM | Varies | Text, Image | Text | Sends text and image inputs to any LLM | LLM model selection dropdown |
| Image Describer | Varies | Image | Text | Analyzes image and generates a descriptive prompt | LLM model selection, editable guidelines |
| Video Describer | Varies | Video | Text | Analyzes video and generates a descriptive prompt | LLM model selection, Model Instructions text box |

## Common Uses

- **Basic prompting:** Prompt node → connect to any generative model
- **Complex prompts:** Multiple Prompt nodes → Prompt Concatenator → generative model
- **Better results:** Prompt → Prompt Enhancer → generative model
- **Image-based prompting:** Import image → Image Describer → use output as prompt for generation
- **Video-based prompting:** Import video → Video Describer → use output as prompt
- **LLM assistance:** Run Any LLM for text analysis, rewriting, or image understanding
```

- [ ] **Step 2: Commit**

```bash
git add ~/.claude/skills/weave/references/text-tools.md
git commit -m "feat: add text tools reference"
```

---

### Task 11: Create Helpers Reference

**Files:**
- Create: `~/.claude/skills/weave/references/helpers.md`

- [ ] **Step 1: Write the helpers reference file**

Create `~/.claude/skills/weave/references/helpers.md` with this content:

```markdown
# Helper Nodes, Iterators & Datatypes

## Helper Nodes

| Node | What It Does | How to Use |
|------|--------------|------------|
| Import | Upload files into Weave (click, drag-and-drop, or paste links) | Supports JPEG, PNG, HEIC, WEBP, MP4, QUICKTIME, MP3, WAV, OGG, GLB |
| Export | Download files from your workflow | Connect output → Export → choose download folder. Keeps original format |
| Preview | View generated content in isolated preview window | Connect any output to see images/videos |
| Import Model | Import external models from Fal, Replicate, or CivitAI | Paste URL of model to import |
| Import LoRA | Upload custom LoRA files from your system | Direct file upload |
| Import Multiple LoRAs | Batch import multiple LoRA files | Select first LoRA, then add more via LoRA list on right panel |
| Router | Distributes one input to multiple outputs | Double-click an output to auto-create. Keeps workflows clean |
| Output | Enables Design App creation | Connect to nodes you want in your App. App feature requires Output Node |
| Sticky Note | Add labels and descriptions to canvas | Place tags throughout canvas for documentation |
| Compare | Side-by-side image comparison with slider | Connect two image inputs, use slider or toggle to compare |

## Iterators (Batch Processing)

| Iterator | Input Type | What It Does | Input Methods |
|----------|-----------|--------------|---------------|
| Text Iterator | Text | Batch-processes multiple prompts through one model | Manual entry, Array Node, Prompt Node, CSV import |
| Image Iterator | Images | Batch-processes multiple images through one model | Import multiple images |
| Video Iterator | Videos | Batch-processes multiple videos through one model | Import multiple videos |

All iterators keep each input separated and generate individual results.

## Datatypes

| Type | What It Does | Use Case |
|------|--------------|----------|
| Number | Numerical input with min/max and decimal precision | Controlling model parameters |
| Text | Text input usable as prompt for any node | Alternative to Prompt node |
| Toggle | True/False boolean switch | Toggling model attributes |
| List Selector | Dropdown menu from manual, Array, or attribute lists | Selecting between options |
| Seed | Controls generation seed (random or manual) | Reproducible outputs |
| Array | Multiple text inputs separated into groups | Feeding List Selector or Text Iterator |
```

- [ ] **Step 2: Commit**

```bash
git add ~/.claude/skills/weave/references/helpers.md
git commit -m "feat: add helpers, iterators, and datatypes reference"
```

---

### Task 12: Create Workflows Directory and Seed Workflows

**Files:**
- Create: `~/.claude/skills/weave/references/workflows/video-restaging.md`
- Create: `~/.claude/skills/weave/references/workflows/morph-transition-kling.md`
- Create: `~/.claude/skills/weave/references/workflows/object-removal.md`

- [ ] **Step 1: Write the video restaging workflow**

Create `~/.claude/skills/weave/references/workflows/video-restaging.md`:

```markdown
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
```

- [ ] **Step 2: Write the morph transition workflow**

Create `~/.claude/skills/weave/references/workflows/morph-transition-kling.md`:

```markdown
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
```

- [ ] **Step 3: Write the object removal workflow**

Create `~/.claude/skills/weave/references/workflows/object-removal.md`:

```markdown
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
```

- [ ] **Step 4: Commit**

```bash
git add ~/.claude/skills/weave/references/workflows/
git commit -m "feat: seed workflow library with restaging, morph transition, and object removal recipes"
```

---

### Task 13: Create /iwantto Slash Command

**Files:**
- Create: `~/.claude/skills/iwantto/SKILL.md`

- [ ] **Step 1: Create the iwantto skill directory**

```bash
mkdir -p ~/.claude/skills/iwantto
```

- [ ] **Step 2: Write the iwantto skill file**

Create `~/.claude/skills/iwantto/SKILL.md` with this content:

```markdown
---
name: iwantto
description: "Quick way to ask Weave for help. Usage: /iwantto remove an object, /iwantto upscale a video, /iwantto make a morph transition. Triggers the full Weave assistant."
---

# I Want To... (Weave Assistant)

You have been invoked via the `/iwantto` command. The user is describing a task they want to accomplish in Figma Weave.

**Read the full Weave skill now:** Read `~/.claude/skills/weave/SKILL.md` and follow all its instructions to help the user with their request.

The user's intent follows the `/iwantto` command — parse it as the task description and respond with:
1. The right nodes and models (budget vs. quality)
2. An ASCII node graph showing how to wire them
3. A suggested prompt ready to paste into Weave
```

- [ ] **Step 3: Commit**

```bash
git add ~/.claude/skills/iwantto/SKILL.md
git commit -m "feat: add /iwantto slash command as alias for /weave"
```

---

### Task 14: Verify and Push

- [ ] **Step 1: Verify file structure**

```bash
find ~/.claude/skills/weave ~/.claude/skills/iwantto -type f | sort
```

Expected output:
```
/Users/mandl/.claude/skills/iwantto/SKILL.md
/Users/mandl/.claude/skills/weave/SKILL.md
/Users/mandl/.claude/skills/weave/references/3d-models.md
/Users/mandl/.claude/skills/weave/references/edit-models.md
/Users/mandl/.claude/skills/weave/references/editing-tools.md
/Users/mandl/.claude/skills/weave/references/enhance-image.md
/Users/mandl/.claude/skills/weave/references/enhance-video.md
/Users/mandl/.claude/skills/weave/references/generate-from-image.md
/Users/mandl/.claude/skills/weave/references/helpers.md
/Users/mandl/.claude/skills/weave/references/image-models.md
/Users/mandl/.claude/skills/weave/references/text-tools.md
/Users/mandl/.claude/skills/weave/references/video-models.md
/Users/mandl/.claude/skills/weave/references/workflows/morph-transition-kling.md
/Users/mandl/.claude/skills/weave/references/workflows/object-removal.md
/Users/mandl/.claude/skills/weave/references/workflows/video-restaging.md
```

- [ ] **Step 2: Copy skill to repo for version control**

```bash
cp -r ~/.claude/skills/weave/ /Users/mandl/Desktop/projects/use-weave/skill/weave/
cp -r ~/.claude/skills/iwantto/ /Users/mandl/Desktop/projects/use-weave/skill/iwantto/
```

- [ ] **Step 3: Commit the repo copy and push**

```bash
cd /Users/mandl/Desktop/projects/use-weave
git add skill/
git commit -m "feat: add complete Weave skill with references, seed workflows, and /iwantto alias"
git push
```

- [ ] **Step 4: Test the skill triggers**

Test both slash commands in a new Claude Code session:

1. Type: `/weave how do I remove an object from a scene?`
2. Type: `/iwantto remove an object from a scene`

Expected for both: Claude activates the Weave skill, recommends Painter + Edit model, shows ASCII node graph, suggests prompt, and presents budget vs. quality model options.
