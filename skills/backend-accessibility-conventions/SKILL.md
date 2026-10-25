---
name: backend-accessibility-conventions
description: Use when backend code generates content a human perceives directly — HTML emails, PDFs/invoices — or shapes API error messages the frontend surfaces to users. Not for internal-only data endpoints with no human-facing rendering. Covers accessibility concerns that originate on the backend, distinct from frontend markup/ARIA concerns.
---

# Backend accessibility conventions

Unlike `accessibility-conventions` (frontend markup/ARIA), this covers accessibility concerns that originate on the backend: content it renders or generates directly, and API response shapes the frontend depends on to be accessible.

## HTML emails (`app/core/email.py` and equivalents)

- Every `<img>` needs a real `alt`. The existing logo embed already does this correctly (`alt="STOK Mobility"`) — preserve it if you touch the embed logic.
- The existing dark palette already passes contrast: body text `#AAAAAA` on `#1A1A1A` card background measures ~7.5:1 (WCAG AAA for normal text), and the `#C8E63A`/`#D1F801` accent colors measure above 12:1 on that same background. When introducing a *new* color pairing for a template, check it holds similar contrast — don't assume a new accent/background combo is fine just because the existing ones are.
- Keep using real heading tags (`<h2>`, `<h3>`) for template section titles rather than styling a `<p>` to look like a heading — screen readers and several email clients rely on actual heading semantics, not visual size.
- Tables (`<table>`/`<th>`/`<td>`) used for line items/order details are fine as-is; don't convert them to `<div>` layouts for styling convenience.

## Generated PDFs (`reportlab` — invoices)

`reportlab` does not produce tagged/accessible PDFs by default — its output has no semantic structure for a screen reader to navigate (no reading order, no alt text on embedded images). Full PDF accessibility is a non-trivial, separate effort. If asked to generate or modify a PDF, say this limitation up front rather than implying the result is accessible just because it looks right visually.

## API error messages

Always return a real, human-readable `detail` string in `HTTPException` — the existing `security.py` pattern does this correctly (`"Could not validate credentials"`), never a bare error code alone. The frontend surfaces this text directly to users, including through assistive tech; a code with no message forces the frontend to invent copy or show nothing meaningful.
