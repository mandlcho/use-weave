# Weave Skill Design Spec

## Overview

A modular Claude Code skill that teaches Claude how to use Figma Weave (formerly WeavyAI) — a web-based, node-based AI image/video generation platform. The skill provides task-oriented node/model recommendations, model selection guidance, and ASCII node graph visualizations.

## Goals

1. **Task-to-node lookup** — "I want to do X" → Claude recommends the right nodes and models
2. **Model selection** — Claude recommends the best model for a task, with budget vs. quality reasoning
3. **Node graph visualization** — every workflow recommendation includes an ASCII diagram showing how to wire nodes
4. **Reference accuracy** — Claude only recommends nodes/models that exist in the reference files, never hallucinated

## Non-Goals

- No API integration (Figma Weave has no public API yet)
- No browser automation
- No full workflow architecture from a creative brief (focus is task-oriented and model comparison)

## Target User

Primary user does video post-production, scene restaging, transition generation, b-roll creation, and compositing/cleanup in Weave. Skill should be optimized for these workflows but approachable for anyone.

## File Structure

```
~/.claude/skills/weave/
├── weave.md                    # Main skill file (trigger + workflow logic)
├── references/
│   ├── image-models.md         # Text-to-image model comparison table
│   ├── video-models.md         # Text-to-video model comparison table
│   ├── edit-models.md          # Image editing models (inpaint, outpaint, remove bg, etc.)
│   ├── generate-from-image.md  # Image-to-image models (ControlNet, Redux, etc.)
│   ├── enhance-image.md        # Upscaling, sharpening, skin enhancement
│   ├── enhance-video.md        # Video upscaling, smoothing
│   ├── 3d-models.md            # 3D generation models
│   ├── editing-tools.md        # Non-generative nodes (crop, blur, compositor, etc.)
│   ├── text-tools.md           # Prompt nodes, LLM nodes, describers
│   └── helpers.md              # Import, export, router, iterators, datatypes
```

## Main Skill File (`weave.md`)

### Trigger Conditions

Activates when the user mentions: Weave, Weavy, Figma Weave, or asks about node-based image/video generation workflows in the context of the platform.

### Content Sections

#### 1. Platform Overview

Figma Weave is a web-based node-based AI creative tool. Key concepts:

- **Nodes** are functions with inputs (left side) and outputs (right side). Connect them by dragging output handles to input handles.
- **Generative nodes** have a Run button and cost credits. They generate new content.
- **Non-generative nodes** are free. They transform content (crop, blur, composite, etc.).
- **Node color system:**
  - Green = Image nodes
  - Purple = Text nodes / LoRA
  - Red = Video nodes
  - Blue = Array / List / 3D nodes
  - White = Multiple input options
  - Lime = Mask nodes
- **Adding nodes:** Right-click canvas or press Tab to search.
- **Parameters:** Select a node to see all settings in the right panel.

#### 2. Task-to-Node Mapping

A lookup table of ~25-30 common tasks and which nodes/models solve them:

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

#### 3. Preferred Workflows

Common workflow patterns with ASCII node graphs:

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

#### 4. Model Selection Logic

When recommending a model, Claude considers:

1. **Credit cost** — always present budget and quality options
2. **Input compatibility** — does the model accept the data type being fed to it?
3. **Output format** — does it produce what the user needs? (PNG vs JPEG, resolution, duration)
4. **Aspect ratio support** — does it support the user's target ratio?
5. **Strengths** — which model handles this specific task best based on the "Best For" column in reference files
6. **Limitations** — what the model can't do or is bad at

Format for recommendations:
- **Budget pick:** model name (X credits) — why
- **Best quality:** model name (X credits) — why
- **Alternative:** model name (X credits) — when to use instead

#### 5. Node Graph Rendering Rules

Every workflow recommendation MUST include an ASCII node graph. Rules:

- Each node is rendered as `[Node Name]`
- Arrows show data flow: `-->` for horizontal, `|` for vertical connections
- Label connection types when ambiguous: `--(image)-->`, `--(mask)-->`, `--(text)-->`
- Show prompt nodes connected to their targets
- For parallel paths, use branching with `+-->`
- Keep graphs readable — max ~6 nodes wide before wrapping

#### 6. Reference File Loading

Claude reads reference files on demand:

- User asks "which image model?" → read `references/image-models.md`
- User asks "which video model?" → read `references/video-models.md`
- User asks about editing/inpainting → read `references/edit-models.md`
- User asks about upscaling → read `references/enhance-image.md` or `references/enhance-video.md`
- User asks about node tools → read `references/editing-tools.md`
- User asks about 3D → read `references/3d-models.md`

Do NOT load all reference files at once. Only load what's relevant to the question.

## Reference File Format

Each reference file is a markdown table with these columns:

| Column | Purpose |
|--------|---------|
| **Model** | Name as it appears in Weave |
| **Credits** | Cost per run |
| **Required Inputs** | What must be connected |
| **Optional Inputs** | What can be connected |
| **Output Format** | PNG, JPEG, MP4, etc. |
| **Aspect Ratios** | Supported options |
| **Best For** | Plain-language strength |
| **Limitations** | What it's bad at or can't do |

## Behavior Rules

1. Claude ONLY recommends nodes/models that exist in the reference files — never hallucinate
2. Every workflow recommendation includes an ASCII node graph
3. Model recommendations always include budget vs. quality options with credit costs
4. Reference files are loaded on-demand, not all at once
5. When unsure which model fits, Claude asks a clarifying question rather than guessing
6. Claude uses Weave's node color terminology when helpful (green = image, red = video, purple = text, lime = mask)

## Future Considerations

- When Figma Weave ships their public API, build an MCP server for programmatic workflow creation and execution
- Reference files should be updated as Weave adds/removes models — check help.weavy.ai for changes
