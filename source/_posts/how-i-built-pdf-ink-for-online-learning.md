---
title: How I Built PDF Ink for Comfortable Online Learning from PDF Books
tags: [web-app, pdf, education, open-source]
---

## Why read PDFs if you can't write on them?

Hello everyone! I'm Albert, and I love making tools more convenient.

If you have ever tried self-learning, studying programming textbooks, or reading complex technical documents from PDFs on your laptop or tablet, you know this struggle. You open a book, want to highlight a key code snippet, draw a quick diagram arrow next to an explanation, or leave a note on the margins — and realize that standard PDF viewers are either bloated, commercial, or they completely lose your drawings if you accidentally refresh the tab.

I wanted to fix this and create **PDF Ink** — a lightweight, fast web application for reading and interactively annotating PDF books directly in the browser.

<!-- more -->

---

## The Idea and Tech Stack

The main goal was to build a tool that:
1. Launches in one click in any modern browser.
2. Allows you to load any local PDF textbook.
3. Lets you draw on pages using a mouse, stylus, or touch, and type textual annotations.
4. **Most importantly:** securely saves your current page, zoom level, library list, and all drawing notes locally so you can resume learning at any moment.

For implementation, I selected the modern stack: **React + TypeScript + Vite + TailwindCSS**.

For the PDF rendering engine, I integrated Mozilla's reliable `pdfjs-dist` (PDF.js), which renders document pages directly onto HTML5 `<canvas>` elements.

---

## Double-Layer Canvas Architecture

To keep the application highly performant and responsive during mouse or touch interaction, I split the workspace into two independent layers:
1. **PDF Layer**: A static background `<canvas>` that renders the PDF page image with respect to the active scale (zoom).
2. **Annotation Layer**: An absolute overlay transparent `<canvas>` that intercepts user pointer events (`PointerEvents`), draws vector strokes, and overlays text notes.

This double-layer approach makes drawing extremely fast: when a user draws a line, the application only repaints the lightweight vector stroke overlay, leaving the heavy PDF canvas intact.

For touch-screen devices, I also added:
- Multi-touch pinch-to-zoom scaling support.
- Text note placement with touch drag-and-drop offsets mapped to page aspect ratio coordinates.

---

## Database Evolution: Upgrading to IndexedDB v2

The initial version of the prototype saved the active PDF file and all drawings in IndexedDB as a single object under the `last-opened` key.

However, as books got larger, we hit a performance bottleneck:
> **The Problem:** Every time the user added an ink stroke or moved a text note, the application performed an autosave to IndexedDB. Writing a 20MB array buffer of the raw PDF file back to disk every 250ms led to interface lag and wore down the SSD.

### The Solution: Separating Files from Annotations

I rewrote the IndexedDB manager and upgraded the store schema to version 2:
1. **`pdfs` store**: Stores the raw binary file buffers (`ArrayBuffer`) keyed by document UUID. This is written **only once** when a new file is uploaded to the library.
2. **`pdfs_metadata` store**: Stores lightweight JSON metadata, including page numbers, zoom levels, drawing strokes, and text coordinates.

Now, as you draw, only the lightweight metadata object is updated. Writing takes a fraction of a millisecond, completely eliminating lag!

I also added an automatic migration script. If a user opens the app with an old version 1 database, it automatically extracts the binary data, generates a new UUID, separates the metadata, and migrates their drawing session on the fly.

---

## Polishing UX: Library, Languages, and Collapsible Controls

To make PDF Ink feel like a premium workspaces tool, I integrated three additional features:
- **Responsive PDF Library**: A collapsible sidebar panel that displays the list of uploaded PDFs, page states, and annotation statistics (drawings count, text annotations count) with interactive cards.
- **Language Switcher**: Built a localization system supporting English (default, EN first) and Russian (RU) with settings persisted in `localStorage`.
- **Collapsible Brush Settings**: Made the pen configuration row (colors, size slider, opacity) toggleable via a Sliders icon so users can hide it and maximize screen workspace on smaller screens.

---

## Try it out!

You can try the live version of the application here:
👉 **[PDF Ink Live App](https://practice-book-app.web.app/)**

---

## Conclusion

Learning tools should help you focus on absorbing knowledge, not fighting with the interface. Developing **PDF Ink** showed me how modern browser APIs (IndexedDB, PointerEvents, HTML5 Canvas) allow developers to build powerful, offline-first tools that respect user data privacy.

Happy learning, and write code that makes lives simpler!
