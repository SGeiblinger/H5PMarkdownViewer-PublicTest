# Markdown Parser Test Suite

This file exercises a broad range of Markdown / GFM features and edge cases for testing the MarkdownViewer's rendering, highlighting, alert, and entity-decoding behavior. Test each section independently — if one section renders wrong, the others should still be unaffected.

---

## 1. Headings

# H1 Heading
## H2 Heading
### H3 Heading
#### H4 Heading
##### H5 Heading
###### H6 Heading

Heading with `inline code` and **bold** inside it
---

## 2. Inline formatting

Plain text, **bold text**, *italic text*, ***bold italic***, ~~strikethrough~~, `inline code`.

Mixed: **bold with *nested italic* inside**, and `code with **fake bold** that should stay literal`.

Escaped characters: \*not italic\*, \# not a heading, 1\. not a list item, \`not code\`.

Line break test — two trailing spaces below should force a line break:
Line one  
Line two (should be on a new line)

Line break test — backslash:
Line one\
Line two (should be on a new line)

No break, just a new paragraph because of a blank line:

Line one

Line two

---

## 3. Links

[Inline link](https://example.com)
[Link with title](https://example.com "Example Title")
[Reference link][ref1]
<https://example.com/autolink>
Plain URL not wrapped: https://example.com/plain

[ref1]: https://example.com/reference "Reference-style link"

---

## 4. Lists

### 4.1 Unordered

- Item 1
- Item 2
  - Nested item 2.1
  - Nested item 2.2
    - Deeply nested 2.2.1
      - Even deeper 2.2.1.1
- Item 3

### 4.2 Ordered

1. First
2. Second
3. Third

### 4.3 Ordered list starting at a custom number

7. Seven
8. Eight
9. Nine

### 4.4 Mixed nesting (ordered inside unordered inside ordered)

1. Top ordered item
   - Nested unordered
     1. Nested ordered inside that
     2. Second nested ordered item
2. Second top-level item

### 4.5 List item with multiple paragraphs

- First item, first paragraph.

  First item, second paragraph (indented to stay part of the same list item).

- Second item.

### 4.6 Task list (GFM)

- [x] Completed task
- [ ] Incomplete task
- [ ] Another incomplete task
  - [x] Nested completed subtask

---

## 5. Blockquotes

> Simple blockquote.

> Multi-line blockquote
> that spans several lines
> of quoted text.

> Nested blockquote:
> > This is nested one level.
> > > This is nested two levels.

> Blockquote containing a list:
> - Quoted item 1
> - Quoted item 2

### GitHub-style alert blockquotes

> [!NOTE]
> This is a note callout. Useful for supplementary information.

> [!TIP]
> This is a tip callout.

> [!IMPORTANT]
> This is an important callout.

> [!WARNING]
> This is a warning callout.

> [!CAUTION]
> This is a caution callout.

---

## 6. Code blocks

Fenced, with language (should get highlight.js coloring):

```javascript
function greet(name) {
  const message = `Hello, ${name}!`;
  return message;
}
```

```python
def fibonacci(n):
    a, b = 0, 1
    for _ in range(n):
        yield a
        a, b = b, a + b
```

Fenced, no language specified (should fall back to plaintext):

```
No language tag here.
Should render as plain monospace text, no color.
```

Fenced, unknown/invalid language (tests the `hljs.getLanguage(lang) ? lang : 'plaintext'` fallback):

```notarealllanguage
This should not throw an error.
Should render as plaintext instead.
```

Indented code block (4 spaces, no fences):

    this.is = "an indented code block";
    should.render = "as code too";

Code block containing Markdown-like syntax (should NOT be interpreted as Markdown):

```
# This is not a real heading
**this is not bold**
- this is not a list item
> this is not a blockquote
```

Code block containing raw HTML (should NOT be rendered as HTML):

```html
<div class="test">
  <script>alert('should stay as visible text, not execute')</script>
</div>
```

---

## 7. Tables

### 7.1 Basic table

| Name  | Role      | Active |
|-------|-----------|--------|
| Alice | Developer | Yes    |
| Bob   | Designer  | No     |

### 7.2 Column alignment

| Left | Center | Right |
|:-----|:------:|------:|
| a    |   b    |     c |
| left-aligned | centered | right-aligned |

### 7.3 Table with inline formatting and code in cells

| Feature | Example | Supported |
|---------|---------|-----------|
| **Bold** | `**bold**` | Yes |
| *Italic* | `*italic*` | Yes |
| Link | [example](https://example.com) | Yes |
| Code | `const x = 1;` | Yes |

### 7.4 Table with an escaped pipe character in a cell

| Expression | Result |
|------------|--------|
| \| is a pipe | literal |
| a \| b | still one cell |

### 7.5 Edge case: list inside a table cell

Standard Markdown tables are single-line per cell, so a "real" multi-line list can't exist inside a cell without raw HTML. This tests how the parser handles an attempted list using `<br>` inside a cell:

| Task | Steps |
|------|-------|
| Setup | 1. Clone repo<br>2. Run npm install<br>3. Run build |
| Cleanup | - Remove node_modules<br>- Remove dist folder |

### 7.6 Edge case: table immediately followed by a list (no blank line between)

| A | B |
|---|---|
| 1 | 2 |
- This list starts right after the table with no blank line — tests whether the parser correctly separates them.

---

## 8. Horizontal rules

Three different valid syntaxes:

---

***

___

---

## 9. Images

![Alt text for a broken/placeholder image](https://example.com/does-not-exist.png "Optional title")

---

## 10. Raw inline and block HTML

Inline: This sentence has <strong>raw HTML bold</strong> and <em>raw HTML italic</em> mixed in.

Block-level raw HTML:

<div style="border: 1px solid red; padding: 8px;">
  <p>This is a raw HTML block. Depending on sanitization settings, this may or may not render as styled HTML.</p>
</div>

Potentially dangerous raw HTML (important security test case — should be neutralized if HTML sanitization is enabled):

<script>console.log('this should not execute if sanitized')</script>
<img src="x" onerror="console.log('this should not execute if sanitized')">

---

## 11. HTML entities and special characters

These characters should render literally, not break parsing: & < > " ' © × ÷ € 日本語 emoji: 🎉 ✅ ⚠️

Ampersand in text: Markdown & HTML & JavaScript.

A comparison that looks like a tag: 5 < 10 and 10 > 5.

A quoted "string" with 'single quotes' too.

---

## 12. Footnotes (not supported by core `marked` without an extension — tests graceful fallback)

Here is a sentence with a footnote reference.[^1]

[^1]: This is the footnote content. If footnotes aren't supported, this should render as plain text rather than breaking the page.

---

## 13. Long unbroken string (overflow/wrap test)

Supercalifragilisticexpialidocioussupercalifragilisticexpialidocioussupercalifragilisticexpialidocious

Long URL: https://example.com/this/is/a/very/long/url/path/that/should/test/word-wrapping/behavior/in/the/rendered/output/without/breaking/the/layout

---

## 14. Empty / near-empty edge cases

Empty code block:

```
```

Empty table cell:

| A | B |
|---|---|
| filled | |
| | filled |

---

## 15. Definition-list-like syntax (not standard CommonMark — tests fallback)

Term 1
: Definition 1

Term 2
: Definition 2a
: Definition 2b

---

*End of test file.*
