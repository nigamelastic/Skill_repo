---
name: create-presentation
description: Create professional, visually appealing PowerPoint presentations using Python and python-pptx. Use when the user asks to "create a presentation", "make a pptx", "generate slides", "build a PowerPoint", "create a deck", or any request involving .pptx file generation. Supports morph transitions, modern layouts, and polished design.
---

# Create Presentation

Generate polished, professional PowerPoint (.pptx) presentations using Python. Every presentation must be visually striking with modern design, consistent branding, and Morph transitions on all slides.

## Setup (Always Do First)

Before writing any presentation code, set up an isolated environment:

```bash
python3 -m venv pptx-env
source pptx-env/bin/activate    # macOS/Linux
# pptx-env\Scripts\activate     # Windows
pip install python-pptx Pillow
```

## Design System

### Theme Selection

Choose **dark** or **light** theme based on the topic. Default to dark for security, hacking, technical, or dev-focused topics.

### Light Palettes

| Palette | Primary | Secondary | Accent | Dark | Light |
|---------|---------|-----------|--------|------|-------|
| **Corporate Blue** | `#1B365D` | `#2E5090` | `#00A3E0` | `#0D1B2A` | `#E8F1FA` |
| **Modern Teal** | `#005F73` | `#0A9396` | `#EE9B00` | `#001219` | `#E9F5F5` |
| **Emerald** | `#064E3B` | `#059669` | `#F59E0B` | `#022C22` | `#ECFDF5` |

### Dark Palettes (all slides use dark backgrounds)

| Palette | bg | surface | panel | accent | accent2 | text |
|---------|------|---------|-------|--------|---------|------|
| **Hacker Red** | `#0A0A12` | `#12121F` | `#1A1A2E` | `#FF2E4C` | `#F4A261` | `#E2E8F0` |
| **Cyber Blue** | `#0A0F1A` | `#0F1729` | `#162033` | `#00D4FF` | `#7C3AED` | `#E2E8F0` |
| **Stealth Green** | `#0A100A` | `#0F1A0F` | `#1A2E1A` | `#00FF41` | `#F4A261` | `#E2E8F0` |
| **Phantom Purple** | `#0D0A14` | `#14112A` | `#1E1A35` | `#A855F7` | `#EC4899` | `#E2E8F0` |

Dark theme principles:
- **Every slide** has a near-black background (`bg` color) — never use white/light slides
- Use `surface` for panels/cards, `panel` for code blocks
- Use colored accent (`accent`) for glowing bars, bullets, highlights, section numbers
- Use `accent2` (amber/warm) for subtitles, sub-headings, secondary elements
- Text is always light (`text` color: `#E2E8F0`) on dark backgrounds
- Use `Consolas` or `Courier New` for code, monospace tags, and labels
- Add subtle grid lines, terminal dots, or `// CLASSIFIED` tags for atmosphere
- Colored bullet markers (accent-colored `\u25B8` prefix) instead of plain bullets

### Typography

- **Titles**: Calibri Light, 36-48pt, bold for emphasis
- **Subtitles**: Calibri, 18-22pt
- **Body text**: Calibri, 15-18pt
- **Code**: Consolas or Courier New, 12-14pt
- **Tags/labels**: Consolas, 10-12pt, muted color

### Layout Principles

- Use generous margins (minimum 0.5 inches from edges)
- Left-align body text (never center paragraphs)
- Limit content: max 6 bullet points per slide, max 8 words per bullet
- Use shapes and colored blocks to create visual hierarchy
- Add a thin glowing accent bar (top or left) as a recurring design element
- Every slide should have intentional whitespace — never cram content
- For dark theme: add subtle corner tags (e.g. `// SECTION 01`) in muted color

## Slide Transitions (MANDATORY on Every Slide)

python-pptx does not natively support transitions. Apply Fade via XML on **every slide** after building the presentation. Use `slide._element` (not `slide_element` which was removed in v1.0+).

**CRITICAL**: The `p:transition` element MUST be inserted **after** `p:cSld` in the XML tree. Inserting at position 0 corrupts the file.

```python
from pptx.oxml.ns import qn
from lxml import etree

def apply_transitions(prs):
    """Apply Fade transition to every slide (correct XML placement)."""
    for slide in prs.slides:
        se = slide._element
        for e in list(se.findall(qn('p:transition'))):
            se.remove(e)
        t = etree.fromstring(
            '<p:transition xmlns:p="http://schemas.openxmlformats.org/presentationml/2006/main"'
            ' spd="slow" advClick="1"><p:fade/></p:transition>'
        )
        # Insert AFTER cSld and clrMapOvr (OOXML requires this order)
        idx = 0
        for i, child in enumerate(se):
            tag = child.tag.split('}')[-1] if '}' in child.tag else child.tag
            if tag in ('cSld', 'clrMapOvr'):
                idx = i + 1
        se.insert(idx, t)
```

**Always call `apply_transitions(prs)` right before `prs.save()`.** For Morph, apply it manually in PowerPoint (Transitions > Morph on all slides) — the mc:AlternateContent XML wrapper needed for Morph can corrupt files in some viewers.

## Slide Construction Patterns

### Helper: RGBColor from Hex

```python
from pptx.util import Inches, Pt, Emu
from pptx.dml.color import RGBColor
from pptx.enum.text import PP_ALIGN, MSO_ANCHOR
from pptx import Presentation

def hex_to_rgb(hex_color):
    hex_color = hex_color.lstrip('#')
    return RGBColor(int(hex_color[0:2], 16), int(hex_color[2:4], 16), int(hex_color[4:6], 16))
```

### Pattern 1: Title Slide with Accent Block

```python
def add_title_slide(prs, title, subtitle, palette):
    slide = prs.slides.add_slide(prs.slide_layouts[6])  # blank layout

    # Full dark background
    bg = slide.background
    fill = bg.fill
    fill.solid()
    fill.fore_color.rgb = hex_to_rgb(palette['dark'])

    # Left accent bar
    accent_bar = slide.shapes.add_shape(
        1, Inches(0), Inches(0), Inches(0.15), Inches(7.5)
    )
    accent_bar.fill.solid()
    accent_bar.fill.fore_color.rgb = hex_to_rgb(palette['accent'])
    accent_bar.line.fill.background()

    # Title
    txbox = slide.shapes.add_textbox(Inches(1.2), Inches(2.0), Inches(8), Inches(1.5))
    tf = txbox.text_frame
    tf.word_wrap = True
    p = tf.paragraphs[0]
    p.text = title
    p.font.size = Pt(44)
    p.font.bold = True
    p.font.color.rgb = hex_to_rgb('#FFFFFF')
    p.font.name = 'Calibri Light'

    # Subtitle
    txbox2 = slide.shapes.add_textbox(Inches(1.2), Inches(3.8), Inches(7), Inches(1))
    tf2 = txbox2.text_frame
    tf2.word_wrap = True
    p2 = tf2.paragraphs[0]
    p2.text = subtitle
    p2.font.size = Pt(22)
    p2.font.color.rgb = hex_to_rgb(palette['accent'])
    p2.font.name = 'Calibri'

    return slide
```

### Pattern 2: Section Divider Slide

```python
def add_section_slide(prs, section_title, section_number, palette):
    slide = prs.slides.add_slide(prs.slide_layouts[6])

    bg = slide.background
    bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(palette['primary'])

    # Large section number
    txbox = slide.shapes.add_textbox(Inches(1.2), Inches(1.5), Inches(3), Inches(2.5))
    tf = txbox.text_frame
    p = tf.paragraphs[0]
    p.text = f"{section_number:02d}"
    p.font.size = Pt(96)
    p.font.bold = True
    p.font.color.rgb = hex_to_rgb(palette['accent'])
    p.font.name = 'Calibri Light'

    # Section title
    txbox2 = slide.shapes.add_textbox(Inches(1.2), Inches(4.2), Inches(8), Inches(1.5))
    tf2 = txbox2.text_frame
    tf2.word_wrap = True
    p2 = tf2.paragraphs[0]
    p2.text = section_title
    p2.font.size = Pt(36)
    p2.font.bold = True
    p2.font.color.rgb = hex_to_rgb('#FFFFFF')
    p2.font.name = 'Calibri Light'

    # Accent line under title
    line = slide.shapes.add_shape(1, Inches(1.2), Inches(5.5), Inches(2.5), Inches(0.06))
    line.fill.solid()
    line.fill.fore_color.rgb = hex_to_rgb(palette['accent'])
    line.line.fill.background()

    return slide
```

### Pattern 3: Content Slide with Bullets

```python
def add_content_slide(prs, title, bullets, palette):
    slide = prs.slides.add_slide(prs.slide_layouts[6])

    bg = slide.background
    bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(palette['light'])

    # Top accent bar
    bar = slide.shapes.add_shape(
        1, Inches(0), Inches(0), Inches(10), Inches(0.06)
    )
    bar.fill.solid()
    bar.fill.fore_color.rgb = hex_to_rgb(palette['accent'])
    bar.line.fill.background()

    # Title
    txbox = slide.shapes.add_textbox(Inches(0.8), Inches(0.5), Inches(8.4), Inches(0.9))
    tf = txbox.text_frame
    tf.word_wrap = True
    p = tf.paragraphs[0]
    p.text = title
    p.font.size = Pt(32)
    p.font.bold = True
    p.font.color.rgb = hex_to_rgb(palette['primary'])
    p.font.name = 'Calibri Light'

    # Bullets
    txbox2 = slide.shapes.add_textbox(Inches(1.0), Inches(1.8), Inches(8.0), Inches(5.0))
    tf2 = txbox2.text_frame
    tf2.word_wrap = True
    for i, bullet in enumerate(bullets):
        p = tf2.paragraphs[0] if i == 0 else tf2.add_paragraph()
        p.text = bullet
        p.font.size = Pt(18)
        p.font.color.rgb = hex_to_rgb(palette['dark'])
        p.font.name = 'Calibri'
        p.space_after = Pt(14)
        p.level = 0

    return slide
```

### Pattern 4: Two-Column Slide

```python
def add_two_column_slide(prs, title, left_items, right_items, palette,
                          left_heading="", right_heading=""):
    slide = prs.slides.add_slide(prs.slide_layouts[6])

    bg = slide.background
    bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(palette['light'])

    # Top bar
    bar = slide.shapes.add_shape(1, Inches(0), Inches(0), Inches(10), Inches(0.06))
    bar.fill.solid()
    bar.fill.fore_color.rgb = hex_to_rgb(palette['accent'])
    bar.line.fill.background()

    # Title
    txbox = slide.shapes.add_textbox(Inches(0.8), Inches(0.5), Inches(8.4), Inches(0.9))
    tf = txbox.text_frame
    tf.word_wrap = True
    p = tf.paragraphs[0]
    p.text = title
    p.font.size = Pt(32)
    p.font.bold = True
    p.font.color.rgb = hex_to_rgb(palette['primary'])
    p.font.name = 'Calibri Light'

    for col_x, heading, items in [
        (Inches(0.8), left_heading, left_items),
        (Inches(5.2), right_heading, right_items),
    ]:
        txbox_col = slide.shapes.add_textbox(col_x, Inches(1.8), Inches(4.0), Inches(5.0))
        tf_col = txbox_col.text_frame
        tf_col.word_wrap = True
        if heading:
            p_h = tf_col.paragraphs[0]
            p_h.text = heading
            p_h.font.size = Pt(20)
            p_h.font.bold = True
            p_h.font.color.rgb = hex_to_rgb(palette['secondary'])
            p_h.font.name = 'Calibri'
            p_h.space_after = Pt(12)
            start = 1
        else:
            start = 0
        for i, item in enumerate(items):
            p_i = tf_col.add_paragraph() if (heading or i > 0) else tf_col.paragraphs[0]
            p_i.text = item
            p_i.font.size = Pt(16)
            p_i.font.color.rgb = hex_to_rgb(palette['dark'])
            p_i.font.name = 'Calibri'
            p_i.space_after = Pt(10)

    return slide
```

### Pattern 5: Key Stat / Big Number Slide

```python
def add_stat_slide(prs, stat_value, stat_label, description, palette):
    slide = prs.slides.add_slide(prs.slide_layouts[6])

    bg = slide.background
    bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(palette['primary'])

    # Big number
    txbox = slide.shapes.add_textbox(Inches(1), Inches(1.5), Inches(8), Inches(2.5))
    tf = txbox.text_frame
    p = tf.paragraphs[0]
    p.text = stat_value
    p.font.size = Pt(96)
    p.font.bold = True
    p.font.color.rgb = hex_to_rgb(palette['accent'])
    p.font.name = 'Calibri Light'
    p.alignment = PP_ALIGN.CENTER

    # Label
    txbox2 = slide.shapes.add_textbox(Inches(1), Inches(4.0), Inches(8), Inches(1))
    tf2 = txbox2.text_frame
    p2 = tf2.paragraphs[0]
    p2.text = stat_label
    p2.font.size = Pt(28)
    p2.font.bold = True
    p2.font.color.rgb = hex_to_rgb('#FFFFFF')
    p2.font.name = 'Calibri'
    p2.alignment = PP_ALIGN.CENTER

    # Description
    if description:
        txbox3 = slide.shapes.add_textbox(Inches(2), Inches(5.2), Inches(6), Inches(1))
        tf3 = txbox3.text_frame
        tf3.word_wrap = True
        p3 = tf3.paragraphs[0]
        p3.text = description
        p3.font.size = Pt(16)
        p3.font.color.rgb = hex_to_rgb(palette['light'])
        p3.font.name = 'Calibri'
        p3.alignment = PP_ALIGN.CENTER

    return slide
```

### Pattern 6: Closing / Thank You Slide

```python
def add_closing_slide(prs, message, contact_info, palette):
    slide = prs.slides.add_slide(prs.slide_layouts[6])

    bg = slide.background
    bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(palette['dark'])

    # Accent bar
    bar = slide.shapes.add_shape(
        1, Inches(3.5), Inches(2.8), Inches(3), Inches(0.06)
    )
    bar.fill.solid()
    bar.fill.fore_color.rgb = hex_to_rgb(palette['accent'])
    bar.line.fill.background()

    # Message
    txbox = slide.shapes.add_textbox(Inches(1), Inches(3.0), Inches(8), Inches(1.5))
    tf = txbox.text_frame
    p = tf.paragraphs[0]
    p.text = message
    p.font.size = Pt(40)
    p.font.bold = True
    p.font.color.rgb = hex_to_rgb('#FFFFFF')
    p.font.name = 'Calibri Light'
    p.alignment = PP_ALIGN.CENTER

    # Contact
    if contact_info:
        txbox2 = slide.shapes.add_textbox(Inches(2), Inches(4.8), Inches(6), Inches(1))
        tf2 = txbox2.text_frame
        tf2.word_wrap = True
        p2 = tf2.paragraphs[0]
        p2.text = contact_info
        p2.font.size = Pt(16)
        p2.font.color.rgb = hex_to_rgb(palette['accent'])
        p2.font.name = 'Calibri'
        p2.alignment = PP_ALIGN.CENTER

    return slide
```

### Dark Theme: Code Slide with Terminal Styling

For dark-themed presentations, add a terminal-style code slide:

```python
def add_code_slide(prs, title, code_text, subtitle=""):
    slide = prs.slides.add_slide(prs.slide_layouts[6])
    # dark bg
    bg = slide.background; bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(P['bg'])

    # left glow bar
    bar = slide.shapes.add_shape(1, Inches(0), Inches(0), Inches(0.12), Inches(7.5))
    bar.fill.solid(); bar.fill.fore_color.rgb = hex_to_rgb(P['accent'])
    bar.line.fill.background()

    # title + subtitle
    # ... (same pattern as other slides)

    # terminal title bar with dots
    title_bar = slide.shapes.add_shape(1, Inches(0.5), Inches(1.55), Inches(12.3), Inches(0.35))
    title_bar.fill.solid(); title_bar.fill.fore_color.rgb = hex_to_rgb(P['panel'])
    title_bar.line.fill.background()
    # Add dot characters: \u25CF  \u25CF  \u25CF in muted color

    # code block bg (near-black #08080F)
    code_bg = slide.shapes.add_shape(1, Inches(0.5), Inches(1.9), Inches(12.3), Inches(5.2))
    code_bg.fill.solid(); code_bg.fill.fore_color.rgb = hex_to_rgb('#08080F')

    # code text in Consolas, green (#00FF41) for strings, red for keywords
    # ... render line by line with simple syntax highlighting
```

### Dark Theme: Colored Bullet Markers

Use accent-colored triangle markers instead of plain bullets:

```python
for item in items:
    p = tf.add_paragraph()
    # Colored bullet marker
    run_b = p.add_run()
    run_b.text = "\u25B8  "  # right-pointing triangle
    run_b.font.color.rgb = hex_to_rgb(P['accent'])
    # Text in light color
    run_t = p.add_run()
    run_t.text = item
    run_t.font.color.rgb = hex_to_rgb(P['text'])
```

## Full Script Template

Produce a **single self-contained Python script**:

```python
import os, time
from pptx import Presentation
from pptx.util import Inches, Pt
from pptx.dml.color import RGBColor
from pptx.enum.text import PP_ALIGN
from pptx.enum.shapes import MSO_SHAPE
from lxml import etree
from pptx.oxml.ns import qn

# Dark palette example (swap for light if needed)
P = {
    'bg': '#0A0A12', 'surface': '#12121F', 'panel': '#1A1A2E',
    'accent': '#FF2E4C', 'accent2': '#F4A261',
    'text': '#E2E8F0', 'muted': '#6B7280', 'dim': '#3A3A55',
    'green': '#00FF41',
}

# ... include hex_to_rgb, all slide pattern functions ...
# ... include apply_transitions ...

def main():
    prs = Presentation()
    prs.slide_width = Inches(13.333)
    prs.slide_height = Inches(7.5)

    # Build slides ...
    apply_transitions(prs)
    prs.save("output.pptx")

if __name__ == "__main__":
    main()
```

## Checklist Before Saving

- [ ] Virtual environment activated and python-pptx installed
- [ ] Slide dimensions set to 16:9 widescreen (13.333 x 7.5 inches)
- [ ] Palette chosen and used consistently (dark or light)
- [ ] `apply_transitions(prs)` called before `prs.save()`
- [ ] No slide exceeds 6 bullet points
- [ ] Accent bar or design element present on every slide
- [ ] Font sizes follow the typography guide
- [ ] Closing slide included
- [ ] For dark theme: all backgrounds are near-black, text is light

## Transition Tips

- Name shapes via `shape.name = "my_element"` for Morph matching
- Keep same-named shapes across consecutive slides for smooth animation
- Use the accent bar consistently as a recurring design thread
- After generating, apply Morph manually in PowerPoint (Transitions > Morph)

## Additional Resources

For more slide patterns and advanced techniques, see [reference.md](reference.md).
