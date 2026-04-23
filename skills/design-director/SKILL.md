---
name: Design Director
description: >
  Visual direction for web interfaces: distinct art direction, typography systems, layout rhythm, and style selection to avoid generic UI repetition.
  Trigger: Designing websites or interfaces that need a clear visual identity, stronger differentiation, or multiple style directions before implementation.
license: MIT
metadata:
  author: Henry Carmenate
  version: "1.0"
  applies-to:
    - claude-code
    - cursor
    - codex
---

## When to Use
Use this skill when:
- Designing marketing sites, landing pages, portfolios, editorials, or product websites that need a clear visual identity
- The user wants the design to feel less generic, less repetitive, or more memorable
- Choosing between multiple visual directions before implementation
- Defining typography, color, layout rhythm, component language, and motion as one coherent system
- Guiding Tailwind CSS, shadcn/ui, daisyUI, Material UI, Bootstrap, or plain CSS with stronger art direction

## Workflow
Follow this sequence before writing implementation code:

1. Interpret the product and audience:
- What is being sold, shown, or explained?
- Who is the audience?
- Should the interface feel premium, technical, playful, editorial, raw, calm, or bold?

2. Propose 2-3 distinct visual directions:
- Each direction must be meaningfully different in typography, composition, color behavior, and component language
- Do not propose minor variations of the same safe SaaS aesthetic

3. Select one direction:
- Explain why it fits the product and what it intentionally avoids

4. Define the visual system:
- Typography pairing
- Palette behavior
- Spacing rhythm
- Border radius strategy
- Shadow philosophy
- Background treatment
- Motion behavior

5. State anti-generic constraints:
- Explicitly name what should not appear in the implementation

6. Hand off to implementation:
- Apply this direction through the relevant framework or styling skill

## Style Direction Matrix

### Direction 1: Editorial Contrast
- **Best for**: Fashion, media, architecture, premium storytelling, portfolios
- **Typography**: High-contrast serif with restrained sans-serif support
- **Color**: Minimal palette with one strong accent or monochrome contrast
- **Layout**: Asymmetric sections, deliberate whitespace, oversized headlines
- **Components**: Quiet UI chrome, thin borders, understated buttons
- **Motion**: Slow fades, reveal transitions, subtle parallax or mask effects
- **Avoid**: Card-heavy dashboards, bubbly controls, generic SaaS hero sections

### Direction 2: Premium Minimal
- **Best for**: Luxury products, agencies, high-end services, fintech with restraint
- **Typography**: Clean sans-serif or refined neo-grotesk with disciplined scale
- **Color**: Soft neutrals, restrained dark tones, one elevated accent color
- **Layout**: Spacious grids, precise alignment, high consistency
- **Components**: Low-noise controls, careful radii, strong hierarchy through spacing
- **Motion**: Crisp micro-movements, subtle elevation, restrained transitions
- **Avoid**: Bright rainbow palettes, excessive badges, loud gradients everywhere

### Direction 3: Playful Saturation
- **Best for**: Consumer products, youth brands, creative tools, community-driven platforms
- **Typography**: Expressive display font plus simple readable support font
- **Color**: Saturated palette, bold contrast, layered backgrounds
- **Layout**: Energetic blocks, overlapping shapes, visible sectional transitions
- **Components**: Chunkier controls, friendly radii, colorful states
- **Motion**: Snappy transitions, hover shifts, animated accents with personality
- **Avoid**: Gray-on-white monotony, ultra-corporate restraint, sterile cards everywhere

### Direction 4: Technical System
- **Best for**: Developer tools, infrastructure products, dashboards, documentation platforms
- **Typography**: Neutral sans-serif with controlled monospace usage
- **Color**: Low-saturation base with precise electric accents
- **Layout**: Dense but orderly, modular panels, utility-first hierarchy
- **Components**: Sharp corners or minimal radii, clear dividers, efficient spacing
- **Motion**: Functional motion only, state changes with strong clarity
- **Avoid**: Decorative luxury styling, oversized empty whitespace, playful visuals that reduce legibility

### Direction 5: Brutalist / Raw
- **Best for**: Experimental brands, cultural projects, rebellious portfolios, anti-corporate identities
- **Typography**: Heavy grotesk, uppercase moments, mono or utilitarian support
- **Color**: Hard contrast, aggressive accent usage, black/white plus one loud signal color
- **Layout**: Tension, broken grids, abrupt scale shifts
- **Components**: Hard edges, obvious borders, utilitarian controls
- **Motion**: Abrupt reveals, directional movement, minimal polish
- **Avoid**: Soft shadows, glassmorphism, rounded generic startup visuals

### Direction 6: Warm Organic
- **Best for**: Lifestyle brands, wellness, travel, local products, editorial commerce
- **Typography**: Humanist sans or soft serif pairings
- **Color**: Earth tones, warm neutrals, low-gloss accents
- **Layout**: Relaxed rhythm, natural spacing, image-forward sections
- **Components**: Gentle radii, tactile surfaces, softer separators
- **Motion**: Calm transitions, organic movement, subtle depth
- **Avoid**: Cold enterprise blue-gray defaults, rigid dashboard composition, overly technical UI language

## Critical Patterns

### Pattern 1: Start with a Creative Brief
Always extract a compact design brief before composing sections.

```ts
type DesignBrief = {
  productType: string
  audience: string
  brandPersonality: string[]
  desiredTone: string[]
  constraints: string[]
}
```

Minimum questions to answer internally:
- What is the product?
- What should the user feel?
- What should the interface not look like?

### Pattern 2: Generate Distinct Directions Before Choosing
Do not jump straight to implementation with the first plausible style.

```ts
type VisualDirection = {
  name: string
  concept: string
  typography: string[]
  colorStrategy: string
  layoutRhythm: string
  componentLanguage: string
  backgroundTreatment: string
  motionStyle: string
  avoid: string[]
}
```

At least two directions must differ in:
- Typography personality
- Layout behavior
- Color intensity
- Component shape language

### Pattern 3: Typography Must Carry Personality
Typography is not a last-mile detail. It defines much of the interface voice.

Good typography decisions include:
- one expressive display font plus one stable support font
- scale contrast between hero, section headings, and body copy
- matching font personality to brand posture

Weak typography patterns to avoid:
- defaulting to Inter in every project
- using the same heading scale for every site
- treating fonts as interchangeable implementation details

### Pattern 4: Layout Rhythm Should Change by Product Type
Do not use the same centered hero plus stacked sections for every site.

Examples:
- Editorial: asymmetry, image-led composition, generous whitespace
- Technical: denser panels, utility grouping, tighter rhythm
- Premium minimal: disciplined grid, strong margins, sparse accents
- Playful: layered blocks, offset shapes, visible transitions between sections

### Pattern 5: Libraries Must Follow the Direction, Not Replace It
Tailwind, shadcn/ui, daisyUI, MUI, and Bootstrap are implementation layers, not the art direction.

Before using a library, decide:
- what the buttons feel like
- how cards differ from defaults
- whether shadows are soft, sharp, or absent
- whether radii are none, subtle, or oversized
- how backgrounds create atmosphere

If the library default fights the chosen direction, override it.

## Output Contract
When producing a design proposal, return this structure before implementation:

```text
Direction:
Concept:
Typography:
Color System:
Layout Rhythm:
Component Language:
Background Strategy:
Motion:
Avoid:
Implementation Notes:
```

The `Avoid:` line is required. It prevents regression into generic UI patterns.

## Anti-Patterns
Avoid these defaults unless the product explicitly requires them:
- Inter + gray + blue as the automatic starting point
- centered hero with one headline, paragraph, and CTA for every homepage
- repeated card grids with soft shadows as the primary layout language
- rounded-xl surfaces everywhere without purpose
- decorative gradients without a broader visual system
- using component-library defaults as final design
- treating accessibility and visual identity as competing goals

## Collaboration with Other Skills
- Use `Design Director` first to choose direction and constraints
- Use `UX Design` to validate usability, accessibility, and responsive behavior
- Use `Tailwind CSS`, `shadcn/ui`, `daisyUI`, `Material UI`, or `Bootstrap` to implement the chosen direction
- Re-check the final UI against the chosen direction before calling the design complete
