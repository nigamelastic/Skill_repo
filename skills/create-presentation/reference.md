# Presentation Reference — Advanced Patterns

## Additional Slide Patterns

### Timeline / Process Slide

```python
def add_timeline_slide(prs, title, steps, palette):
    """steps: list of (step_title, step_desc) tuples, max 4-5 steps."""
    slide = prs.slides.add_slide(prs.slide_layouts[6])

    bg = slide.background
    bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(palette['light'])

    # Top accent bar
    bar = slide.shapes.add_shape(1, Inches(0), Inches(0), Inches(13.333), Inches(0.06))
    bar.fill.solid()
    bar.fill.fore_color.rgb = hex_to_rgb(palette['accent'])
    bar.line.fill.background()

    # Title
    txbox = slide.shapes.add_textbox(Inches(0.8), Inches(0.5), Inches(11), Inches(0.9))
    tf = txbox.text_frame
    tf.word_wrap = True
    p = tf.paragraphs[0]
    p.text = title
    p.font.size = Pt(32)
    p.font.bold = True
    p.font.color.rgb = hex_to_rgb(palette['primary'])
    p.font.name = 'Calibri Light'

    # Horizontal connector line
    n = len(steps)
    start_x = 1.5
    total_width = 10.0
    spacing = total_width / max(n - 1, 1)

    line_shape = slide.shapes.add_shape(
        1, Inches(start_x), Inches(3.45), Inches(total_width), Inches(0.04)
    )
    line_shape.fill.solid()
    line_shape.fill.fore_color.rgb = hex_to_rgb(palette['secondary'])
    line_shape.line.fill.background()

    for i, (step_title, step_desc) in enumerate(steps):
        cx = start_x + i * spacing

        # Circle node
        from pptx.enum.shapes import MSO_SHAPE
        circle = slide.shapes.add_shape(
            MSO_SHAPE.OVAL, Inches(cx - 0.2), Inches(3.15), Inches(0.65), Inches(0.65)
        )
        circle.fill.solid()
        circle.fill.fore_color.rgb = hex_to_rgb(palette['accent'])
        circle.line.fill.background()
        circle.text_frame.paragraphs[0].text = str(i + 1)
        circle.text_frame.paragraphs[0].font.size = Pt(16)
        circle.text_frame.paragraphs[0].font.bold = True
        circle.text_frame.paragraphs[0].font.color.rgb = hex_to_rgb('#FFFFFF')
        circle.text_frame.paragraphs[0].alignment = PP_ALIGN.CENTER
        circle.text_frame.paragraphs[0].font.name = 'Calibri'

        # Step title
        txbox_t = slide.shapes.add_textbox(
            Inches(cx - 0.8), Inches(4.1), Inches(2.0), Inches(0.7)
        )
        tf_t = txbox_t.text_frame
        tf_t.word_wrap = True
        p_t = tf_t.paragraphs[0]
        p_t.text = step_title
        p_t.font.size = Pt(14)
        p_t.font.bold = True
        p_t.font.color.rgb = hex_to_rgb(palette['primary'])
        p_t.font.name = 'Calibri'
        p_t.alignment = PP_ALIGN.CENTER

        # Step description
        txbox_d = slide.shapes.add_textbox(
            Inches(cx - 0.8), Inches(4.7), Inches(2.0), Inches(1.2)
        )
        tf_d = txbox_d.text_frame
        tf_d.word_wrap = True
        p_d = tf_d.paragraphs[0]
        p_d.text = step_desc
        p_d.font.size = Pt(11)
        p_d.font.color.rgb = hex_to_rgb(palette['dark'])
        p_d.font.name = 'Calibri'
        p_d.alignment = PP_ALIGN.CENTER

    return slide
```

### Quote / Highlight Slide

```python
def add_quote_slide(prs, quote, attribution, palette):
    slide = prs.slides.add_slide(prs.slide_layouts[6])

    bg = slide.background
    bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(palette['dark'])

    # Large quotation mark
    txbox_q = slide.shapes.add_textbox(Inches(1.0), Inches(1.0), Inches(2), Inches(2))
    tf_q = txbox_q.text_frame
    p_q = tf_q.paragraphs[0]
    p_q.text = "\u201C"
    p_q.font.size = Pt(144)
    p_q.font.color.rgb = hex_to_rgb(palette['accent'])
    p_q.font.name = 'Georgia'

    # Quote text
    txbox = slide.shapes.add_textbox(Inches(1.5), Inches(2.5), Inches(9.5), Inches(3))
    tf = txbox.text_frame
    tf.word_wrap = True
    p = tf.paragraphs[0]
    p.text = quote
    p.font.size = Pt(26)
    p.font.italic = True
    p.font.color.rgb = hex_to_rgb('#FFFFFF')
    p.font.name = 'Calibri Light'
    p.space_after = Pt(20)

    # Attribution
    if attribution:
        p2 = tf.add_paragraph()
        p2.text = f"— {attribution}"
        p2.font.size = Pt(18)
        p2.font.color.rgb = hex_to_rgb(palette['accent'])
        p2.font.name = 'Calibri'

    return slide
```

### Comparison / Pros-Cons Slide

```python
def add_comparison_slide(prs, title, left_label, left_items, right_label, right_items, palette):
    slide = prs.slides.add_slide(prs.slide_layouts[6])

    bg = slide.background
    bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(palette['light'])

    # Top bar
    bar = slide.shapes.add_shape(1, Inches(0), Inches(0), Inches(13.333), Inches(0.06))
    bar.fill.solid()
    bar.fill.fore_color.rgb = hex_to_rgb(palette['accent'])
    bar.line.fill.background()

    # Title
    txbox = slide.shapes.add_textbox(Inches(0.8), Inches(0.5), Inches(11), Inches(0.9))
    tf = txbox.text_frame
    p = tf.paragraphs[0]
    p.text = title
    p.font.size = Pt(32)
    p.font.bold = True
    p.font.color.rgb = hex_to_rgb(palette['primary'])
    p.font.name = 'Calibri Light'

    # Left panel background
    left_panel = slide.shapes.add_shape(
        1, Inches(0.6), Inches(1.8), Inches(5.6), Inches(5.0)
    )
    left_panel.fill.solid()
    left_panel.fill.fore_color.rgb = hex_to_rgb('#FFFFFF')
    left_panel.line.color.rgb = hex_to_rgb(palette['secondary'])
    left_panel.line.width = Pt(1)
    left_panel.shadow.inherit = False

    # Right panel background
    right_panel = slide.shapes.add_shape(
        1, Inches(6.8), Inches(1.8), Inches(5.6), Inches(5.0)
    )
    right_panel.fill.solid()
    right_panel.fill.fore_color.rgb = hex_to_rgb('#FFFFFF')
    right_panel.line.color.rgb = hex_to_rgb(palette['secondary'])
    right_panel.line.width = Pt(1)

    for col_x, label, items in [
        (Inches(1.0), left_label, left_items),
        (Inches(7.2), right_label, right_items),
    ]:
        # Label
        txbox_l = slide.shapes.add_textbox(col_x, Inches(2.0), Inches(5.0), Inches(0.6))
        tf_l = txbox_l.text_frame
        p_l = tf_l.paragraphs[0]
        p_l.text = label
        p_l.font.size = Pt(22)
        p_l.font.bold = True
        p_l.font.color.rgb = hex_to_rgb(palette['primary'])
        p_l.font.name = 'Calibri'

        # Items
        txbox_i = slide.shapes.add_textbox(col_x, Inches(2.8), Inches(5.0), Inches(3.5))
        tf_i = txbox_i.text_frame
        tf_i.word_wrap = True
        for j, item in enumerate(items):
            p_i = tf_i.paragraphs[0] if j == 0 else tf_i.add_paragraph()
            p_i.text = f"\u2022  {item}"
            p_i.font.size = Pt(16)
            p_i.font.color.rgb = hex_to_rgb(palette['dark'])
            p_i.font.name = 'Calibri'
            p_i.space_after = Pt(10)

    return slide
```

### Image Slide (Full Bleed with Caption)

```python
ALLOWED_IMAGE_EXTENSIONS = {'.png', '.jpg', '.jpeg', '.gif', '.bmp', '.tiff', '.webp'}

def add_image_slide(prs, image_path, caption, palette):
    import os
    if not os.path.isfile(image_path):
        raise FileNotFoundError(f"Image not found: {image_path}")
    if os.path.splitext(image_path)[1].lower() not in ALLOWED_IMAGE_EXTENSIONS:
        raise ValueError(f"Unsupported image format: {image_path}")

    slide = prs.slides.add_slide(prs.slide_layouts[6])

    slide.shapes.add_picture(
        image_path, Inches(0), Inches(0),
        width=Inches(13.333), height=Inches(7.5)
    )

    # Semi-transparent caption bar at bottom
    caption_bg = slide.shapes.add_shape(
        1, Inches(0), Inches(5.8), Inches(13.333), Inches(1.7)
    )
    caption_bg.fill.solid()
    caption_bg.fill.fore_color.rgb = hex_to_rgb(palette['dark'])
    from pptx.oxml.ns import qn as _qn
    fill_elem = caption_bg.fill._fill
    solid = fill_elem.find(_qn('a:solidFill'))
    if solid is not None:
        color_elem = solid[0]
        color_elem.set('val', palette['dark'].lstrip('#'))
        alpha = etree.SubElement(color_elem, _qn('a:alpha'))
        alpha.set('val', '80000')  # 80% opacity
    caption_bg.line.fill.background()

    # Caption text
    txbox = slide.shapes.add_textbox(Inches(1), Inches(6.0), Inches(11), Inches(1.2))
    tf = txbox.text_frame
    tf.word_wrap = True
    p = tf.paragraphs[0]
    p.text = caption
    p.font.size = Pt(20)
    p.font.color.rgb = hex_to_rgb('#FFFFFF')
    p.font.name = 'Calibri'

    return slide
```

## Adding Tables

```python
from pptx.util import Inches, Pt

def add_table_slide(prs, title, headers, rows, palette):
    slide = prs.slides.add_slide(prs.slide_layouts[6])

    bg = slide.background
    bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(palette['light'])

    # Top bar
    bar = slide.shapes.add_shape(1, Inches(0), Inches(0), Inches(13.333), Inches(0.06))
    bar.fill.solid()
    bar.fill.fore_color.rgb = hex_to_rgb(palette['accent'])
    bar.line.fill.background()

    # Title
    txbox = slide.shapes.add_textbox(Inches(0.8), Inches(0.5), Inches(11), Inches(0.9))
    tf = txbox.text_frame
    p = tf.paragraphs[0]
    p.text = title
    p.font.size = Pt(32)
    p.font.bold = True
    p.font.color.rgb = hex_to_rgb(palette['primary'])
    p.font.name = 'Calibri Light'

    num_rows = len(rows) + 1
    num_cols = len(headers)
    table_shape = slide.shapes.add_table(
        num_rows, num_cols, Inches(0.8), Inches(1.8), Inches(11.5), Inches(4.5)
    )
    table = table_shape.table

    # Header row styling
    for i, header in enumerate(headers):
        cell = table.cell(0, i)
        cell.text = header
        cell.fill.solid()
        cell.fill.fore_color.rgb = hex_to_rgb(palette['primary'])
        for paragraph in cell.text_frame.paragraphs:
            paragraph.font.size = Pt(14)
            paragraph.font.bold = True
            paragraph.font.color.rgb = hex_to_rgb('#FFFFFF')
            paragraph.font.name = 'Calibri'

    # Data rows with alternating colors
    for r_idx, row in enumerate(rows):
        for c_idx, value in enumerate(row):
            cell = table.cell(r_idx + 1, c_idx)
            cell.text = str(value)
            if r_idx % 2 == 0:
                cell.fill.solid()
                cell.fill.fore_color.rgb = hex_to_rgb('#FFFFFF')
            else:
                cell.fill.solid()
                cell.fill.fore_color.rgb = hex_to_rgb(palette['light'])
            for paragraph in cell.text_frame.paragraphs:
                paragraph.font.size = Pt(13)
                paragraph.font.color.rgb = hex_to_rgb(palette['dark'])
                paragraph.font.name = 'Calibri'

    return slide
```

## Adding Charts (via python-pptx Chart API)

```python
from pptx.chart.data import CategoryChartData
from pptx.enum.chart import XL_CHART_TYPE

def add_chart_slide(prs, title, categories, series_data, palette):
    """
    series_data: list of (series_name, values) tuples
    """
    slide = prs.slides.add_slide(prs.slide_layouts[6])

    bg = slide.background
    bg.fill.solid()
    bg.fill.fore_color.rgb = hex_to_rgb(palette['light'])

    # Top bar
    bar = slide.shapes.add_shape(1, Inches(0), Inches(0), Inches(13.333), Inches(0.06))
    bar.fill.solid()
    bar.fill.fore_color.rgb = hex_to_rgb(palette['accent'])
    bar.line.fill.background()

    # Title
    txbox = slide.shapes.add_textbox(Inches(0.8), Inches(0.5), Inches(11), Inches(0.9))
    tf = txbox.text_frame
    p = tf.paragraphs[0]
    p.text = title
    p.font.size = Pt(32)
    p.font.bold = True
    p.font.color.rgb = hex_to_rgb(palette['primary'])
    p.font.name = 'Calibri Light'

    chart_data = CategoryChartData()
    chart_data.categories = categories
    for name, values in series_data:
        chart_data.add_series(name, values)

    chart_shape = slide.shapes.add_chart(
        XL_CHART_TYPE.COLUMN_CLUSTERED,
        Inches(1.0), Inches(1.8), Inches(11.0), Inches(5.0),
        chart_data
    )

    chart = chart_shape.chart
    chart.has_legend = True
    chart.legend.include_in_layout = False
    chart.legend.font.size = Pt(12)

    # Color the series
    palette_colors = [palette['primary'], palette['accent'], palette['secondary']]
    for i, series in enumerate(chart.series):
        series.format.fill.solid()
        series.format.fill.fore_color.rgb = hex_to_rgb(
            palette_colors[i % len(palette_colors)]
        )

    return slide
```

## Shape Naming for Morph

For smooth Morph animations, name shapes that should animate between slides:

```python
shape = slide.shapes.add_shape(...)
shape.name = "accent_bar"

shape2 = slide.shapes.add_textbox(...)
shape2.name = "main_title"
```

When the same `shape.name` appears on consecutive slides, PowerPoint Morph will interpolate position, size, color, and rotation between them.

## Slide Dimensions Reference

| Aspect Ratio | Width | Height | Use Case |
|---|---|---|---|
| 16:9 (default) | 13.333 in | 7.5 in | Modern widescreen |
| 4:3 | 10.0 in | 7.5 in | Legacy projectors |
| 16:10 | 13.333 in | 8.333 in | Some laptops |

Always prefer 16:9 unless the user specifies otherwise.

## Troubleshooting

| Issue | Fix |
|---|---|
| Morph not working | Open in PowerPoint 2016+ or Office 365; older versions fall back to Fade |
| Text overflows | Reduce font size or split across slides |
| Colors look different on screen | PowerPoint applies gamma; test on target display |
| lxml import error | `pip install lxml` (usually bundled with python-pptx) |
