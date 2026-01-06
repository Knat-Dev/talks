# Coralogix Slidev Presentation Assistant

You are helping create presentation slides using Slidev with the Coralogix theme.

## Project Structure

```
├── slides.md              # Main presentation file
├── theme/                 # Coralogix brand theme
│   ├── layouts/           # Vue layout components
│   └── styles/index.css   # Global styles
├── assets/                # Images, logos, memes
└── package.json           # Scripts: dev, build, export
```

## Available Layouts

### `a-main-cover-2` - Opening slide
Green gradient background, logo top-left, content centered-left.
```markdown
---
layout: a-main-cover-2
---

# Talk Title

**Speaker Name** · @handle
```

### `section` - Section dividers
Light gradient, large text, for introducing new sections.
```markdown
---
layout: section
---

# Section Title

## Optional subtitle

**Bold text** renders in green.
```

### `default` - Content slides
Light gradient background, logo top-left, page number bottom-right.
```markdown
---
layout: default
---

# Slide Title

Content, code blocks, lists, etc.
```

### `image-right` - Split layout
30% green panel left (title), 70% white panel right (content/table).
```markdown
---
layout: image-right
---

::left::

# Left Title

Optional subtitle

::default::

| Column 1 | Column 2 |
|----------|----------|
| Data     | Data     |
```

### `two-cols` - Two columns
Equal width columns on light background.
```markdown
---
layout: two-cols
---

# Left content

::right::

# Right content
```

### `quote` - Quote slide
Styled blockquote with optional attribution.
```markdown
---
layout: quote
author: Author Name
role: Title/Company
---

Quote text goes here as the slot content.
```

### `end` - Closing slide
Green gradient, centered content, for "Thank You" slides.
```markdown
---
layout: end
---

# Thank You

**Speaker Name** · @handle

Questions?
```

## Slide Syntax

### Frontmatter (first slide only)
```markdown
---
theme: ./theme
title: Your Talk Title
layout: a-main-cover-2
highlighter: shiki
transition: slide-left
mdc: true
---
```

### Speaker Notes
HTML comments at the END of each slide:
```markdown
# Slide Title

Content here.

<!--
Speaker notes go here.
Visible in presenter mode (press P).
Aim for 1-2 minutes of speaking per slide for timing.
-->
```

### Click Animations
Single element reveal:
```markdown
<v-click>

Content that appears on click

</v-click>
```

Sequential list items:
```markdown
<v-clicks>

- First point
- Second point
- Third point

</v-clicks>
```

### Images with Overlay
For memes that appear over content:
```markdown
<v-click>
<img src="/assets/meme.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>
```

### Code Blocks
Keep code SHORT to avoid line wrapping. Use `typescript` or `html` language tags:
```typescript
@Component({ selector: 'app-example' })
export class ExampleComponent {
  value = input<string>('');
}
```

## Formatting Rules

1. **Code blocks must not wrap** - If a line is too long, shorten variable names or split across lines
2. **One concept per slide** - Details go in speaker notes
3. **Tables for comparisons** - Decision frameworks work well as tables
4. **Memes use v-click** - Let the speaker trigger them
5. **Bold text (`**text**`) renders green** in most layouts

## Timing Guidelines

- Target: ~1-1.5 minutes speaking per slide
- Speaker notes should be conversational, not bullet points
- For a 30-minute talk: ~20-25 slides
- Expand notes to hit timing targets, don't add more slides

## AI Workflow

### Creating a new presentation
1. Start with topic/abstract
2. Generate outline with section headers
3. Create slide structure with layouts
4. Add code examples (keep short!)
5. Expand speaker notes to target duration
6. Review for formatting issues (line wrapping, overflow)

### Expanding speaker notes
When asked to expand notes for timing:
- Write conversationally (how you'd actually speak)
- Add context, examples, transitions
- Include [click for meme] cues where relevant
- Aim for 100-200 words per slide

### Reviewing a presentation
Check for:
- Code block line wrapping
- Consistent layout usage
- Speaker notes timing (read aloud)
- Slide count vs target duration
- Missing transitions between sections

## Commands

```bash
pnpm dev          # Start dev server (http://localhost:3030)
pnpm build        # Build static site to dist/
pnpm export       # Export to PDF
```

## Presenter Mode

- `P` - Open presenter view (see notes + next slide)
- `O` - Slide overview
- Arrow keys / Space - Navigate
- Click triggers v-click animations
