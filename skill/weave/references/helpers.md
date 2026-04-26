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
