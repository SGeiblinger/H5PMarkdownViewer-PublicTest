# H5PMarkdownViewer-PublicTest
Test repo for the H5P MarkDown viewer

## Why?
To test one of the three inputs modalities -> loading from an external URL.

## Some markdown for testing
 - List1
 - List2

1. step one
2. step two

# Some code blocks:

## JavaScript

```javascript
function greet(name) {
  const message = `Hello, ${name}!`;
  console.log(message);
  return message;
}

greet("World");
```

## Python

```python
def fibonacci(n):
    a, b = 0, 1
    for _ in range(n):
        yield a
        a, b = b, a + b

print(list(fibonacci(10)))
```

## CSS

```css
.h5p-markdownviewer {
  font-family: sans-serif;
  padding: 1rem;
  border-radius: 4px;
}
```

## HTML

```html
<div class="container">
  <h1>Hello</h1>
  <p>This is a test.</p>
</div>
```

## Bash

```bash
#!/bin/bash
for i in {1..5}; do
  echo "Iteration $i"
done
```

## No language specified

```
This block has no language tag at all.
Should fall back to plaintext (or auto-detect, if you enable that).
```

## Unknown/invalid language

```notarealllanguage
This tests your fallback logic when hljs.getLanguage(lang) returns falsy.
It should render as plaintext instead of throwing an error.
```

## JSON

```json
{
  "name": "test",
  "nested": {
    "value": 42,
    "list": [1, 2, 3]
  }
}
```
