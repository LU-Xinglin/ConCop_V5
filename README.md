# Confluence Knowledge Base Portal

An internal portal with two Confluence functions:

1. Export all or part of a space into a Word document for upload to an internal LLM.
2. Create a new Confluence child page from a Word, PowerPoint, or PDF file.

## User flow

1. Paste a Confluence page URL and personal access token.
2. Click **Scan Space**. The portal retrieves page titles and hierarchy only.
3. Choose either:
   - **Everything in this space**, or
   - One or more content branches. If the space has a single landing page, the
     portal skips single-page layers and offers the first level with multiple
     branches. Each selection includes every child and descendant beneath it.
4. Choose the preferred LLM answer depth: Brief, Standard, or Detailed.
5. Generate and download the Word document.

The exported document includes an **LLM Answer Guide** with the selected answer
depth and a fixed structure: direct answer, supporting details, source pages,
and uncertainty or assumptions.

## Confluence Writer flow

1. Select **Confluence Writer** in the left navigation.
2. Paste a page URL and personal access token, then load the page hierarchy.
3. Select any existing page to use as the new page's parent.
4. Enter the new page title and upload a `.docx`, `.pptx`, or `.pdf` file.
5. Review and edit the extracted content before choosing **Create Confluence Page**.

Word headings, paragraphs, lists, and tables are converted to Confluence
storage-format HTML. PowerPoint text and tables are grouped by slide. PDF
conversion extracts selectable text by page. Images in uploads are currently
flagged in the preview rather than copied automatically, so they can be added
to the new page separately if needed.

## Images

Confluence image attachments are downloaded with the user's Confluence token and
embedded in the Word document. Images over 15 MB, unavailable attachments, and
unsupported formats retain a visible placeholder and a source link instead.

## Setup

```bash
pip install -r requirements.txt
python app.py
```

Open `http://localhost:5000` or `http://<your-machine-ip>:5000` on the internal
network.

## Certificate

Place `hsbc-orca-g2.pem` in this project folder. The portal always uses that
file for Confluence certificate verification; users do not enter a certificate
path in the portal.

## Security notes

- Tokens are not written to files. They are retained only in server memory while
  a scan is active (currently 30 minutes) and during its export.
- The scan step fetches metadata only; page bodies are fetched only after the
  user chooses the export scope.
- The portal does not send a Confluence bearer token to external image hosts.
- Generated DOCX files are kept in `generated/`; arrange periodic cleanup if the
  portal will run continuously.
- Uploaded source files are deleted from `uploads/` immediately after content
  extraction. Extracted preview content remains in server memory for 30 minutes.
