# Nemo Qwen AI Rename

A Python extension that adds **Suggest Name with Qwen VL…** to Nemo's context menu. It analyzes
one local file or folder with a locally hosted Qwen model, proposes a concise name, and presents
an editable confirmation dialog before renaming anything.

All inference stays local. The extension connects only to `127.0.0.1:8080`.

## Features

- Uses Qwen VL to understand images and scanned PDF pages.
- Extracts bounded text samples from PDFs, text files, DOC/DOCX, spreadsheets, presentations,
  EPUB, and OpenDocument files.
- Summarizes a bounded folder listing without uploading its contents anywhere.
- Runs extraction and inference in a background thread so Nemo remains responsive.
- Preserves file extensions, including common compound extensions.
- Rejects path separators, oversized names, and collisions.
- Requires explicit confirmation before renaming.
- Waits while llama.cpp is loading the model or temporarily busy.

## Requirements

- Nemo file manager with Nemo Python bindings
- Python 3 with PyGObject, GTK 3, and GdkPixbuf bindings
- A Qwen-compatible model served by `llama-server`
- A vision projector (`mmproj`) for image and scanned-PDF analysis
- `poppler-utils` for `pdftotext` and `pdftoppm`
- `catdoc` for legacy `.doc` files

Example Debian or Ubuntu packages:

```bash
sudo apt install python-nemo python3-gi gir1.2-gtk-3.0 gir1.2-nemo-3.0 poppler-utils catdoc
```

Package names can vary by distribution.

## Start the local model

Run an OpenAI-compatible llama.cpp server with a multimodal Qwen model:

```bash
llama-server \
  --model /path/to/qwen-model.gguf \
  --mmproj /path/to/mmproj-model.gguf \
  --host 127.0.0.1 \
  --port 8080
```

Using `127.0.0.1` prevents the unauthenticated model API from being exposed to the local network.
The model must support the `/v1/chat/completions` endpoint and OpenAI-style image content.

## Install

```bash
mkdir -p ~/.local/share/nemo-python/extensions
cp qwen-ai-rename.py nemo-qwen-ai-rename.svg ~/.local/share/nemo-python/extensions/
nemo --quit
pkill -x nemo-desktop
```

Open Nemo again, right-click one supported local file or folder, and select
**Suggest Name with Qwen VL…**. Review or edit the proposed filename, then choose **Rename**.

The first request may take longer while the model loads.

## Supported inputs

- Images supported by GdkPixbuf
- Text, Markdown, CSV, JSON, XML, YAML, logs, and similar text files
- PDF, including a first-page vision fallback for scanned documents
- DOC and DOCX
- XLSX, PPTX, EPUB, ODT, ODS, and ODP
- Folders, using a bounded two-level listing

Unsupported binary types produce an error rather than a name inferred only from the old filename.

## Configuration

The endpoint and extraction limits are constants near the top of `qwen-ai-rename.py`. Change
`QWEN_ENDPOINT` if the local server uses a different port.

## Uninstall

```bash
rm ~/.local/share/nemo-python/extensions/qwen-ai-rename.py \
  ~/.local/share/nemo-python/extensions/nemo-qwen-ai-rename.svg
nemo --quit
pkill -x nemo-desktop
```

Open Nemo again to finish unloading the extension.
