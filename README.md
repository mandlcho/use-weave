# use-weave

A Claude Code skill that teaches Claude how to use [Figma Weave](https://app.weavy.ai/) (formerly WeavyAI) — a web-based, node-based AI image/video generation platform.

## What it does

Ask Claude about Weave and get:

- **Node recommendations** — "How do I remove an object?" → Painter + Flux Fill Pro
- **Model selection** — budget vs. quality picks with credit costs
- **ASCII node graphs** — visual wiring diagrams for every workflow
- **Ready-to-paste prompts** — optimized for the recommended model
- **Self-learning** — saves confirmed workflows as recipes, updates model references from help.weavy.ai

## Slash commands

| Command | Example |
|---------|---------|
| `/weave` | `/weave how do I upscale a video?` |
| `/iwantto` | `/iwantto make a morph transition between two frames` |

## Installation

Copy the skill files to your Claude Code skills directory:

```bash
cp -r skill/weave ~/.claude/skills/weave
cp -r skill/iwantto ~/.claude/skills/iwantto
```

That's it. The skills will appear in your next Claude Code session.

## File structure

```
skill/
├── weave/
│   ├── SKILL.md                          # Main skill — trigger logic, task mapping, workflows
│   └── references/
│       ├── image-models.md               # 24 text-to-image models
│       ├── video-models.md               # 16 text/image-to-video models
│       ├── edit-models.md                # 28 image editing models (inpaint, bg removal, relight)
│       ├── generate-from-image.md        # 8 image-to-image models (ControlNet, style transfer)
│       ├── enhance-image.md              # 9 upscaling/sharpening models
│       ├── enhance-video.md              # 4 video upscaling/smoothing models
│       ├── 3d-models.md                  # 9 image-to-3D models
│       ├── editing-tools.md              # Free non-generative nodes (crop, blur, levels, etc.)
│       ├── text-tools.md                 # Prompt, LLM, and describer nodes
│       ├── helpers.md                    # Import/export, iterators, datatypes
│       └── workflows/                    # Saved workflow recipes
│           ├── video-restaging.md
│           ├── morph-transition-kling.md
│           └── object-removal.md
├── iwantto/
│   └── SKILL.md                          # Quick alias for /weave
```

## Self-learning features

### Workflow library

When you confirm a workflow worked, Claude saves it to `references/workflows/`. Next time you ask for the same task, it pulls up your proven recipe instead of recommending from scratch.

### Reference updating

Say **"update Weave models"** and Claude fetches the latest model comparison pages from help.weavy.ai, diffs them against the reference files, and updates with your confirmation.

## Covered workflows

Some of the 29 task-to-node mappings included:

| Task | Key nodes |
|------|-----------|
| Remove object from scene | Painter + Edit model |
| Restage a video frame | Extract Video Frame + Seedream Edit |
| Morph/transform transition | Extract Frame (x2) + Kling First/Last Frame |
| Generate b-roll shots | Seedream V4.5 |
| Crop out character | Merge Alpha |
| Upscale image | Recraft / Magnific / Topaz |
| Upscale video | Topaz Video / Real-ESRGAN |
| Remove/replace background | Bria / SD3 |
| Batch process | Iterators + any model |

## Docs

- [Design spec](docs/superpowers/specs/2026-04-26-weave-skill-design.md)
- [Implementation plan](docs/superpowers/plans/2026-04-26-weave-skill.md)
- [Weave help center](https://help.weavy.ai/en/)
