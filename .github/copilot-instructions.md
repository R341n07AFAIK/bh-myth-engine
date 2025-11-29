# Copilot Instructions for BH / OIP Myth-Engine

You are GitHub Copilot working inside the **BH / OIP myth-engine** project.

## Project Purpose

This is a **prompt-generation and graphics-composition engine** that synthesizes:

- **Image prompts** for visual generators: Midjourney, DALLE, Stable Diffusion, Flux, etc.
- **Grok / xAI therapy-film prompts** for LLM-based narrative synthesis.
- **Rainbow graphics payloads** (Paper / Tilt / Glide backend stubs) for hardware-accelerated rendering.
- **Frontend manifests** for theme-driven UI composition.

**Core Concept Stack:**
- **Worlds**: Top-level contexts (e.g., BH, OIP) that define thematic boundaries.
- **Themes**: Sub-contexts within a world (e.g., h_bureaucratic_shrine).
- **Styles**: Visual / thematic modifiers (e.g., surreal-propaganda-noir).
- **Generators**: Output modules (midjourney, dall_e, stable_diffusion, flux, graphics, grok).
- **Motifs**: Integrated lexicon of surreal, apocalyptic, and archival imagery.

## Architecture Overview

### Entry Points

- **generate.js** (CommonJS)  Main CLI driver. Parses args, dispatches to generators, outputs JSON.
- **promptGenerators.js**  Registry of all generators. Each generator emits a JSON record with { generator, world, themes, styles, mode, prompt }.
- **graphicsEngine.js**  Stub for Paper / Tilt / Glide "rainbow graphics" rendering.
- **lexicon.js**  Motif collection: surreal / propaganda / bureaucratic imagery templates.
- **in/generatePrompt.js**  Higher-level CLI for OIP / BH style prompts (similar to generate.js).

### Data Flow

1. **Input**: CLI args (generator, world, themes, styles, mode, num).
2. **Lexicon expansion**: collectMotifs() combines INTEGRATED_EXAMPLES + theme/style variants.
3. **Generator dispatch**: Based on --generator=X, call generateX().
4. **Output**: JSON record or batch written to /out/ directory.

### Optional Dependencies

- **OpenAI SDK** (required): Grok/xAI integration uses OpenAI client with aseURL=https://api.x.ai/v1.
- **XAI_API_KEY** (optional): If missing, Grok generation gracefully skips with a warning.

## Common Tasks

### Generate a Single Prompt

`ash
node generate.js \\
  --generator=midjourney \\
  --world=BH \\
  --themes=bh_bureaucratic_shrine \\
  --styles=surreal-propaganda-noir
`

### List Available Generators

`ash
node generate.js --list-generators
`

### Generate Grok Therapy-Film Batch

Requires XAI_API_KEY:

`ash
XAI_API_KEY=sk-... node generate.js \\
  --generator=grok \\
  --world=BH \\
  --themes=bh_bureaucratic_shrine \\
  --styles=surreal-propaganda-noir \\
  --mode=therapy-film \\
  --num=10
`

### Run Full CTSCE Pipeline

Execute all generators (graphics, grok, flux manifest):

`ash
bash ctsce_all.sh
`

Output goes to /out/ directory.

### Export Full Bundle

`ash
bash ctsce_export_full.sh
`

## Key Files to Prioritize When Reasoning

1. **generate.js**  Understand CLI parsing and the dispatch logic.
2. **promptGenerators.js**  See all 6 generators and their output shapes.
3. **lexicon.js**  Study the motif / theme expansion system.
4. **graphicsEngine.js**  Understand the stub for Paper / Tilt / Glide payloads.
5. **package.json**  Note "type": "module" (ES modules) and OpenAI dependency.

## Important Patterns & Conventions

### CLI Argument Parsing

Arguments use --key=value or --key value format:

`javascript
const opts = parseArgs(argv); // Returns { key: value }
`

### Generator Module Shape

Every generator function returns a JSON record:

`javascript
{
  generator: "name",
  world: "BH",
  themes: ["bh_bureaucratic_shrine"],
  styles: ["surreal-propaganda-noir"],
  mode: null,
  prompt: "...synthesized text..."
}
`

### Motif Expansion

Themes and styles are interpolated into motif templates:

`javascript
// "bh_bureaucratic_shrine"  "bh bureaucratic shrine rendered as an exhausted editorial set-piece"
// "surreal-propaganda-noir"  "surreal-propaganda-noir color language layered over rain-slick asphalt"
`

### Output Directory

Generated assets go into /out/:

- /out/rainbow_graphics.json (graphics generator).
- /out/grok_prompts_ctsce.json (Grok batch).
- /out/frontend_manifest.json (Flux/theme manifest).

### Graceful Fallbacks

If XAI_API_KEY is missing, Grok generation should warn and skip, not crash.

## Coding Guidelines

- **Language**: Node.js (CommonJS + ES modules as per package.json).
- **Error handling**: Descriptive messages, especially for missing env vars or invalid generator names.
- **Idempotency**: Re-running scripts should not destroy existing /out/ assets without explicit --force flag.
- **Module structure**: Keep generators small and focused; avoid monolithic files.

## How to Learn & Debug

1. **Read** README.md and AGENTS.md (safety/verification rules).
2. **Run** 
ode generate.js --list-generators to see available outputs.
3. **Inspect** promptGenerators.js to understand the registry and each generator's shape.
4. **Run** ./scripts/project_info.sh (if present) for a quick summary.
5. **Check** /out/ for generated JSON to validate output shape.

## Safety & Destructive Operations

- **Do NOT** delete or overwrite /out/ files unless explicitly instructed.
- For any destructive action:
  1. Add a --force or --confirm flag.
  2. Default to **non-destructive** behavior (warn before deleting).
  3. Provide clear feedback: "Would delete X files. Pass --force to confirm."

## Common Errors & Resolutions

| Error | Cause | Resolution |
|-------|-------|-----------|
| XAI_API_KEY missing | Grok env var not set | Set export XAI_API_KEY=sk-... or skip Grok generation. |
| Cannot find module 'openai' | Dependency missing | Run 
pm install. |
| Invalid generator: foo | Unknown generator name | Run 
ode generate.js --list-generators to see valid names. |
| /out/ is not a directory | Directory not created | Run mkdir -p /out/ or let scripts auto-create it. |

## Additional Resources

- **AGENTS.md**  Validation rules for CI runners and automated agents.
- **package.json**  Scripts: 
pm run generate, 
pm run prompt.
- **Lexicon examples** (in lexicon.js):
  - "alternate-history Vatican corridors"
  - "surveillance shrines and Schismware terminals"
  - "rain-slick bureaucratic plazas under neon catechisms"
  - "propaganda-noir fashion editorial framing"
  - "35mm film language with volumetric godrays"
