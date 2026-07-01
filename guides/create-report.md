---
name: create-report
when_to_use: When the user wants a downloadable report — generate a PDF from HTML and let the user download it.
---

# Create a PDF report

When the user wants a report of some sort, offer to create a PDF report they can download.

1. Generate the report as HTML.
2. Render it to PDF with `create-pdf-file-from-html`, saving the file into the `/etc/tmp/` folder.
3. Let the user download the PDF with `download-file`.
