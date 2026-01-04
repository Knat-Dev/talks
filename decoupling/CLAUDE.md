# Coralogix Slidev Presentation Assistant

You are helping create presentation slides using Slidev for Coralogix.

## Project Structure

```
├── slides.md              # Main presentation file
├── theme/                 # Coralogix brand theme
│   ├── styles/index.css   # Brand colors & styling
│   └── setup/shiki.ts     # Tokyo Night syntax highlighting
├── assets/                # Images, logos, memes
├── SCRIPT.md              # Full speaker script (optional)
└── SLIDEV-REFERENCE.md    # Slidev syntax reference
```

## Slidev Syntax Rules

### Slide Separator
```markdown
---
```

### Frontmatter (first slide only)
```markdown
---
theme: ./theme
title: Your Talk Title
layout: center
highlighter: shiki
transition: slide-left
mdc: true
---
```

### Speaker Notes
Put notes in HTML comments at the END of each slide:
```markdown
# Slide Title

Content here.

<!--
Speaker notes go here.
These appear in presenter mode (press P).
-->
```

### Click-to-Reveal (v-click)
For elements that appear on click:
```markdown
<v-click>

![meme](./assets/meme.jpg)

</v-click>
```

For sequential list items:
```html
<v-clicks>

- First point
- Second point
- Third point

</v-clicks>
```

### Layouts
```markdown
---
layout: center
---

# Centered content
```

Available layouts: `default`, `center`, `cover`, `two-cols`, `image-right`, `image-left`

### Code Blocks
Use TypeScript/Angular syntax highlighting:
```typescript
@Component({ selector: 'app-example' })
export class ExampleComponent {
  value = input<string>('');
}
```

### Images with Click Reveal
```markdown
<v-click>

<img src="./assets/meme.jpg" class="absolute bottom-10 right-10 w-80 rounded-lg shadow-xl" />

</v-click>
```

## Coralogix Brand Guidelines

### Colors (already in theme)
- Primary: `#00c853` (Coralogix green)
- Background: `#0c0c0c` (near black)
- Surface: `#1a1a1a` (dark gray)
- Text: `#e8e8e8` (light gray)

### Tone
- Technical but approachable
- Use code examples liberally
- Memes are encouraged for engagement
- Keep slides concise - details go in speaker notes

### Structure
1. Hook / Problem statement
2. Context / Why this matters
3. Solution / Tools / Patterns
4. Examples with code
5. Summary / Decision framework
6. Call to action

## When Generating Slides

1. **Keep slides minimal** - one concept per slide
2. **Code should be readable** - not too long, highlight key parts
3. **Speaker notes are detailed** - full talking points go there
4. **Use v-click for reveals** - especially for memes and punchlines
5. **Tables for comparisons** - decision frameworks work well as tables

## Commands

```bash
pnpm dev          # Start dev server with hot reload
pnpm build        # Build static site to dist/
pnpm export:pdf   # Export to PDF
```

## Presenter Mode

- Press `P` to open presenter view
- Press `O` for slide overview
- Arrow keys / Space to navigate
- Clicks trigger v-click animations
