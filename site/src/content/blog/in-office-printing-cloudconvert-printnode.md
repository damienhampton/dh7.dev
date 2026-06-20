---
title: "In-office printing with CloudConvert and PrintNode"
slug: in-office-printing-cloudconvert-printnode
publishedAt: 2026-06-20
brief: "How to trigger in-office printing programmatically using CloudConvert for document conversion and PrintNode for printer management."
tags: ["printing", "cloudconvert", "printnode", "integrations", "development"]
draft: true
---

## Problem

You want to print a document from a web application directly to a physical printer. The user shouldn't need to download the file and print it manually.

## Cause

Browsers cannot communicate with local printers directly. You need two things: a way to get the document into a printable format (PDF), and a service that can deliver that PDF to a printer on the network.

## Fix

CloudConvert handles file conversion via API. PrintNode manages printer connections and accepts print jobs via REST API.

The flow:

1. Submit the source file to CloudConvert and convert it to PDF
2. Retrieve the PDF URL from the completed job
3. Fetch the PDF and encode it as base64
4. Send the base64-encoded PDF to PrintNode with the target printer ID

## Code

### Step 1: Create a CloudConvert job

CloudConvert jobs are built from tasks. An `import/url` task fetches the source file, a `convert` task converts it, and an `export/url` task makes the result available for download.

```javascript
const axios = require('axios');

const CLOUDCONVERT_API_KEY = process.env.CLOUDCONVERT_API_KEY;

async function createConversionJob(sourceUrl, filename) {
  const response = await axios.post(
    'https://api.cloudconvert.com/v2/jobs',
    {
      tasks: {
        'import-file': {
          operation: 'import/url',
          url: sourceUrl,
          filename: filename,
        },
        'convert-file': {
          operation: 'convert',
          input: 'import-file',
          output_format: 'pdf',
        },
        'export-file': {
          operation: 'export/url',
          input: 'convert-file',
        },
      },
    },
    {
      headers: {
        Authorization: `Bearer ${CLOUDCONVERT_API_KEY}`,
        'Content-Type': 'application/json',
      },
    }
  );

  return response.data.data.id;
}
```

### Step 2: Wait for the job to complete and retrieve the PDF URL

Poll the job endpoint until the status is `finished`.

```javascript
async function waitForJob(jobId) {
  const maxAttempts = 20;
  const delayMs = 3000;

  for (let attempt = 0; attempt < maxAttempts; attempt++) {
    const response = await axios.get(
      `https://api.cloudconvert.com/v2/jobs/${jobId}`,
      {
        headers: {
          Authorization: `Bearer ${CLOUDCONVERT_API_KEY}`,
        },
      }
    );

    const job = response.data.data;

    if (job.status === 'finished') {
      const exportTask = job.tasks.find(
        (t) => t.operation === 'export/url' && t.status === 'finished'
      );
      return exportTask.result.files[0].url;
    }

    if (job.status === 'error') {
      throw new Error(`CloudConvert job failed: ${jobId}`);
    }

    await new Promise((resolve) => setTimeout(resolve, delayMs));
  }

  throw new Error(`CloudConvert job timed out: ${jobId}`);
}
```

### Step 3 & 4: Fetch the PDF and send it to PrintNode

Retrieve the printer ID first if you don't have it. `GET /printers` returns all printers registered to your PrintNode account.

```javascript
const PRINTNODE_API_KEY = process.env.PRINTNODE_API_KEY;

async function printPdf(pdfUrl, printerId, jobTitle = 'Print Job') {
  // Fetch the PDF and encode as base64
  const pdfResponse = await axios.get(pdfUrl, {
    responseType: 'arraybuffer',
  });
  const base64Content = Buffer.from(pdfResponse.data).toString('base64');

  // Send to PrintNode
  const response = await axios.post(
    'https://api.printnode.com/printjobs',
    {
      printerId: printerId,
      title: jobTitle,
      contentType: 'pdf_base64',
      content: base64Content,
      source: 'application',
    },
    {
      auth: {
        username: PRINTNODE_API_KEY,
        password: '',
      },
      headers: {
        'Content-Type': 'application/json',
      },
    }
  );

  return response.data; // Returns the print job ID
}
```

### Putting it together

```javascript
async function convertAndPrint(sourceUrl, filename, printerId) {
  const jobId = await createConversionJob(sourceUrl, filename);
  const pdfUrl = await waitForJob(jobId);
  const printJobId = await printPdf(pdfUrl, printerId, filename);
  return printJobId;
}
```

To retrieve available printer IDs:

```javascript
async function getPrinters() {
  const response = await axios.get('https://api.printnode.com/printers', {
    auth: {
      username: PRINTNODE_API_KEY,
      password: '',
    },
  });
  return response.data;
}
```

## Further reading

- [CloudConvert API documentation](https://cloudconvert.com/api/v2)
- [PrintNode API documentation](https://www.printnode.com/en/docs/api/curl)
