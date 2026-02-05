# Export Embedded Slide Decks to PDF using Python & Playwright

A reproducible, automation-friendly pipeline for exporting **restricted or embedded slide decks**
from a webpage into a single PDF using browser automation.

This repository demonstrates a practical pattern for capturing browser-rendered slides
when native export is unavailable, unreliable, or impractical.
The approach has been tested on **large decks (700+ slides)** and avoids hardcoding slide counts.

---

## Why this exists

In many real-world settings, slide decks cannot be exported directly:

- export options may be disabled
- content may only be accessible via an embedded viewer
- decks may be too large for manual workflows
- slides may be rendered dynamically inside an iframe

Rather than relying on platform-specific export features, this project
operates at the **browser level**, capturing what is actually rendered
and converting it into a clean, shareable PDF.

---

## What this pipeline does

The workflow consists of three main stages:

### 1. Slide capture
- Opens the embedded slide deck directly in a browser
- Navigates slides using keyboard input (`PageDown`)
- Captures each slide as a high-resolution image
- Automatically detects the end of the deck by identifying repeated slides

### 2. Output organisation
- Stores individual slide images in a dedicated folder
- Writes the final PDF to a separate output directory
- Creates folders automatically if they do not already exist

### 3. PDF generation
- Combines all captured slide images into a single multi-page PDF
- Preserves slide order
- Uses Pillow (PIL) for image-to-PDF conversion

---

## Applicability beyond Google Slides

Although Google Slides is used as a **worked example**, this pipeline can be applied
to **any restricted slide deck embedded within a webpage**, provided that:

- the slides are rendered in the browser
- keyboard or scripted navigation is possible
- the embedded content can be accessed via a stable URL

The core idea is browser-level capture, not platform-specific export.

---

## Locating the embed URL (Google Slides case study)

For embedded or restricted slide decks, the visible page URL is often
*not* the URL that actually renders the slides.

The simplest way to locate the correct URL is via your browser’s developer tools.

### Typical steps

1. Open the page where the slides are embedded
2. Right-click on the slide area and select **Inspect**
3. Locate the `<iframe>` element that contains the slides
4. Copy the value of the iframe’s `src` attribute
5. Use this value as the embed URL in the notebook or script

For Google Slides, the iframe `src` typically looks like:

```
https://docs.google.com/presentation/d/…/embed
```

This embed URL is what Playwright loads and navigates during capture.

### Why this matters

Browser automation operates on what the browser actually renders.
Targeting the iframe source directly avoids:

- surrounding site navigation
- page-level redirects
- unrelated page elements

This results in more reliable navigation and consistent slide capture.

---

## Tech stack

- Python 3.10+
- Playwright (browser automation)
- Pillow (PIL) for image → PDF conversion
- `pathlib` for filesystem handling

---

## Project structure

```text
project/
│
├── slides_out/                        # PNG screenshots (auto-generated)
├── PDF_Slides/                        # Final PDF output
├── RestrictedSlideDecktoPDF.ipynb     # Step-by-step tutorial notebook
├── requirements.txt
├── README.md
└── .gitignore
```

Notes:
- Output folders are intentionally excluded from version control
- No slide content or private URLs are committed

---

## Setup

### Install dependencies

```bash
pip install -r requirements.txt
playwright install
```

### Why both steps are required

- `pip install -r requirements.txt` installs the **Python libraries**
  used by the project (Playwright and Pillow).
- `playwright install` downloads the **browser binaries**
  (Chromium, Firefox, WebKit) that Playwright controls.

The browser engines are not Python packages and cannot be installed via `pip`,
which is why this second step is required.

---

## Getting started

The **Jupyter notebook** is the recommended entry point.

The notebook walks through:
- configuring output paths
- supplying your own embed URL
- capturing slides
- converting images into a PDF

It is written as a tutorial and can be run top-to-bottom once configured.

---

## Design decisions

A few intentional choices worth highlighting:

- **No hardcoded slide count**  
  The script stops automatically when slides stop changing.

- **Configurable safety cap (`max_slides`)**  
  Prevents infinite loops while remaining adaptable to different deck sizes.

- **PageDown navigation**  
  More reliable than arrow keys in embedded slide contexts.

- **Separated output folders**  
  Keeps large binaries out of version control and improves clarity.

---

## GitHub safety

The repository intentionally ignores:
- generated slide images
- generated PDFs
- virtual environments
- notebook checkpoints

This keeps the repository lightweight, shareable, and safe to publish.

---

## Responsible use

This approach is intended for legitimate use cases such as:
- exporting your own content
- working with decks you are authorised to access
- creating personal archives or internal documentation
- - Offline review and accessibility
- Research archiving and reproducibility
- QA and documentation workflows

Always ensure you have appropriate permission before capturing
or redistributing slide content.

This project is intended for legitimate use cases such as:

- It assumes the user has lawful access to the embedded content.
- It does not attempt to bypass authentication, encryption, or DRM.

Users are responsible for complying with applicable terms of service and copyright restrictions.

---

## Notes and limitations

- Timing parameters may need adjustment for very slow or media-heavy slides
- Headed mode (`headless=False`) is recommended for debugging
- The approach assumes keyboard navigation is enabled in the embed

---

## Licence

This project is released under the MIT Licence.
