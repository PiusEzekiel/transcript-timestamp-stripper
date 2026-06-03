# Transcript Timestamp Stripper

A Pinokio-ready, serverless browser tool for cleaning transcript files. Paste text or upload a transcript, strip timestamp lines and inline timestamp markers, then save the cleaned result as `.txt` or `.json`.

## What It Does

- Removes standalone timestamps such as `00:54:45`
- Removes SRT/VTT cue ranges such as `00:00:01,000 --> 00:00:04,000`
- Removes leading inline timestamps such as `[00:54:45] Text`
- Optionally removes SRT cue numbers
- Optionally collapses extra blank lines or merges the output into one paragraph
- Exports TXT or structured JSON locally in the browser

## Use In Pinokio

1. Push this folder to a GitHub repository.
2. In Pinokio, add the GitHub repository URL.
3. Open the app. No install step is required.
4. Upload a transcript or paste text.
5. Click **Strip timestamps**.
6. Choose TXT or JSON and click **Save file**.

## Local Use

Open `index.html` in any modern browser. The tool is fully offline and does not upload transcript text anywhere.

## Programmatic API

The cleaning logic lives in the browser function `stripTranscriptTimestamps(rawText, options)`.

### JavaScript

```js
const result = stripTranscriptTimestamps(transcriptText, {
  collapseBreaks: true,
  removeCueNumbers: true,
  mergeParagraphs: false
});

console.log(result.text);
console.log(result.paragraphs);
console.log(result.stats);
```

### Python

```python
import re

def strip_transcript_timestamps(raw_text):
    timestamp = r"(?:\d{1,2}:)?\d{1,2}:\d{2}(?:[\.,]\d{1,3})?"
    timestamp_only = re.compile(rf"^\s*(?:\[|\()?{timestamp}(?:\s*-->\s*{timestamp})?(?:\]|\))?\s*$")
    leading = re.compile(rf"^\s*(?:\[|\()?{timestamp}(?:\]|\))?\s*[-–—:]?\s*")
    inline = re.compile(rf"(?:^|\s)(?:\[|\()?{timestamp}(?:\]|\))?(?=\s|$)")

    lines = []
    for line in raw_text.replace("\r\n", "\n").replace("\r", "\n").split("\n"):
        if timestamp_only.match(line) or re.match(r"^\s*\d+\s*$", line):
            lines.append("")
            continue
        line = leading.sub("", line)
        line = inline.sub(" ", line)
        lines.append(line.rstrip())

    return re.sub(r"\n{3,}", "\n\n", "\n".join(lines)).strip()
```

### Curl

This is a static browser app and does not run an HTTP API server. For command-line automation, use the Python snippet above or adapt the JavaScript function from `index.html`.

## Files

- `index.html` - the complete app
- `pinokio.json` - Pinokio metadata
- `icon.svg` - app icon
