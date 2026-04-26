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
