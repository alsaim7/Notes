# 🚀 Connect HTML Form to Google Sheets (No Backend!)

> **What you'll learn:** Send form data to Google Sheets (POST) and read data back (GET) — using only Google Apps Script + Fetch API.

---

## 📌 Table of Contents

1. [How It Works](#-how-it-works)
2. [Step 1 — Create Google Sheet](#-step-1--create-google-sheet)
3. [Step 2 — Write Apps Script Code](#-step-2--write-apps-script-code)
4. [Step 3 — Run Setup](#-step-3--run-setup)
5. [Step 4 — Deploy Web App](#-step-4--deploy-web-app)
6. [Step 5 — HTML Form (POST)](#-step-5--html-form-post)
7. [Step 6 — Read Data (GET)](#-step-6--read-data-get)
8. [Add More Fields](#-add-more-fields)
9. [Important Notes](#%EF%B8%8F-important-notes)
10. [Use Cases](#-use-cases)

---

## 🧠 How It Works

```
POST Flow:
HTML Form  ──►  Fetch API  ──►  Apps Script (doPost)  ──►  Google Sheet ✅

GET Flow:
Browser    ──►  Apps Script (doGet)  ──►  Google Sheet  ──►  JSON Response ✅
```

**Tools Used:**
- ✅ Google Apps Script (free, no server needed)
- ✅ Fetch API (built into every browser)

---

## 📋 Step 1 — Create Google Sheet

1. Go to [Google Sheets](https://sheets.google.com)
2. Create a **new spreadsheet**
3. Add these headers in **Row 1**:

### Basic (2 columns):
| A | B |
|---|---|
| `timestamp` | `email` |

### Extended (4 columns):
| A | B | C | D |
|---|---|---|---|
| `timestamp` | `email` | `firstName` | `lastName` |

> ⚠️ **Important:** Column header names must exactly match your HTML form `name` attributes!

---

## ⚙️ Step 2 — Write Apps Script Code

**Go to:** `Extensions → Apps Script`

Delete the default code and paste this:

```javascript
var sheetName = 'Sheet1';
var scriptProp = PropertiesService.getScriptProperties();

// ──────────────────────────────────────────
// 🔧 Run this ONCE to link your spreadsheet
// ──────────────────────────────────────────
function initialSetup() {
  var activeSpreadsheet = SpreadsheetApp.getActiveSpreadsheet();
  scriptProp.setProperty('key', activeSpreadsheet.getId());
}

// ──────────────────────────────────────────
// 📥 POST — Write data TO the sheet
// ──────────────────────────────────────────
function doPost(e) {
  var lock = LockService.getScriptLock();
  lock.tryLock(10000);

  try {
    var doc = SpreadsheetApp.openById(scriptProp.getProperty('key'));
    var sheet = doc.getSheetByName(sheetName);

    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
    var nextRow = sheet.getLastRow() + 1;

    var newRow = headers.map(function(header) {
      return header === 'timestamp' ? new Date() : e.parameter[header];
    });

    sheet.getRange(nextRow, 1, 1, newRow.length).setValues([newRow]);

    return ContentService
      .createTextOutput(JSON.stringify({ status: "success", row: nextRow }))
      .setMimeType(ContentService.MimeType.JSON);

  } catch (error) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: "error", error: error }))
      .setMimeType(ContentService.MimeType.JSON);

  } finally {
    lock.releaseLock();
  }
}

// ──────────────────────────────────────────
// 📤 GET — Read data FROM the sheet
// ──────────────────────────────────────────
function doGet(e) {
  var doc = SpreadsheetApp.openById(scriptProp.getProperty('key'));
  var sheet = doc.getSheetByName(sheetName);

  var data = sheet.getDataRange().getValues();
  var headers = data.shift(); // Remove header row, save it

  var json = data.map(function(row) {
    var obj = {};
    headers.forEach(function(header, i) {
      obj[header] = row[i];
    });
    return obj;
  });

  return ContentService
    .createTextOutput(JSON.stringify(json))
    .setMimeType(ContentService.MimeType.JSON);
}
```

---

## ▶️ Step 3 — Run Setup

> This step links your script to your spreadsheet. **Do it only once.**

| # | Action |
|---|--------|
| 1 | In the Apps Script editor, select function: `initialSetup` |
| 2 | Click the **▶ Run** button |
| 3 | Click **Review Permissions** |
| 4 | Choose your Google account |
| 5 | Click **Allow** |

✅ Done! Your script is now linked to the sheet.

---

## 🚀 Step 4 — Deploy Web App

| # | Action |
|---|--------|
| 1 | Click **Deploy → New Deployment** |
| 2 | Click ⚙️ gear icon → Select **Web App** |
| 3 | Set **Execute as:** `Me` |
| 4 | Set **Who has access:** `Anyone` |
| 5 | Click **Deploy** |
| 6 | **Copy the Web App URL** — you'll need it! |

> 🔗 Your URL will look like:
> `https://script.google.com/macros/s/AKfycb.../exec`

---

## 🌐 Step 5 — HTML Form (POST)

Replace `YOUR_WEB_APP_URL` with your actual URL from Step 4.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Form → Google Sheets</title>
</head>
<body>

  <form id="myForm" action="YOUR_WEB_APP_URL" method="POST">
    <input type="email" name="email" placeholder="Enter your email" required>
    <button type="submit">Submit</button>
  </form>

  <script>
    document.getElementById('myForm').addEventListener('submit', function(e) {
      e.preventDefault(); // Stop normal form submission

      const formData = new FormData(this); // Collect all form fields

      fetch(this.action, {
        method: 'POST',
        body: formData
      })
      .then(res => res.json())
      .then(data => {
        alert("✅ Data saved to Google Sheets!");
        this.reset(); // Clear the form
      })
      .catch(err => console.error("Error:", err));
    });
  </script>

</body>
</html>
```

---

## 📥 Step 6 — Read Data (GET)

### Option A — Open in Browser

Just paste your Web App URL in the browser:
```
https://script.google.com/macros/s/YOUR_ID/exec
```

You'll get a JSON response like:
```json
[
  {
    "timestamp": "2025-06-15T10:00:00",
    "email": "user@example.com"
  },
  {
    "timestamp": "2025-06-16T14:30:00",
    "email": "another@example.com"
  }
]
```

### Option B — Fetch in JavaScript

```javascript
fetch("YOUR_WEB_APP_URL")
  .then(res => res.json())
  .then(data => {
    console.log(data); // Array of row objects
  });
```

---

## ➕ Add More Fields

**Step 1 — Add to your HTML form:**
```html
<input type="text" name="firstName" placeholder="First Name">
<input type="text" name="lastName"  placeholder="Last Name">
```

**Step 2 — Add to Sheet headers (Row 1):**
```
timestamp | email | firstName | lastName
```

> That's it! No changes needed in Apps Script. It reads headers automatically. 🎉

---

## ⚠️ Important Notes

| ✅ Do This | ❌ Don't Do This |
|---|---|
| Match sheet header names exactly | Use different names in form and sheet |
| Use `Anyone` for web app access | Keep access restricted (POST will fail) |
| Run `initialSetup` once before deploying | Skip the setup step |
| Redeploy after code changes | Expect old deployment to reflect new code |

> ❌ **No trigger needed** — `doPost` and `doGet` are called automatically by the web app URL.

---

## 🎯 Use Cases

- 📬 Contact forms
- 📧 Newsletter sign-up
- 🧲 Lead capture pages
- 📊 Simple data collection (surveys, feedback)
- 🔁 Replacing a backend for small projects

---

## 🔗 Bonus Ideas

You can extend this project to:

- 🔷 Use with **React** or **Next.js**
- 🔷 Add **Axios** instead of Fetch
- 🔷 Build a **dashboard** that reads from GET API
- 🔷 Add **loading spinner + success message** UI

---

## ✅ Summary

| Feature | Status |
|---|---|
| Save form data to Google Sheets | ✅ Working |
| Read data as JSON API | ✅ Working |
| No backend / server needed | ✅ Confirmed |
| Works with any frontend | ✅ Yes (HTML, React, etc.) |

---

> 💡 **Pro Tip:** Always redeploy (create a **new deployment**) after making code changes. Updating the same deployment doesn't always reflect new code.
