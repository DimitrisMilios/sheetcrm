# 📑 Master Architecture Blueprint: Antigravity SheetCRM
This comprehensive instruction file serves as your complete development and deployment guide for the **Antigravity SheetCRM** Chrome Extension. This unified platform turns Google Sheets into a fully functional micro-CRM using **Flutter Web**, **Chrome Manifest V3**, and **Google Apps Script**.

---

## 💡 Project Vision & Purpose

### What is Antigravity SheetCRM?
Small businesses heavily rely on Excel and Google Sheets because they are highly flexible, familiar, and free. However, spreadsheets lack automation, require endless manual copy-pasting, and fail to provide interactive client workflows.

**Antigravity SheetCRM** bridges this gap. It turns a standard cloud spreadsheet into a powerhouse CRM without forcing users to adapt to an expensive or complicated platform. It acts as an interactive productivity overlay layer directly inside the Google Chrome sidebar, feeding data back and forth to their existing spreadsheets seamlessly.

---

## 🎯 System Core Features

Your Antigravity CRM application holds **5 primary problem-solving systems**:

*   **Feature 1: The "Right-Click to CRM" Lead Scraper**  
    Eliminates tedious manual copy-pasting. Users can highlight raw text (like contact names, phone numbers, or notes) on any public website, right-click, and instantly push it directly into their Google Sheet columns.
*   **Feature 2: Dynamic Social Context Overlay**  
    Pulls real-time tracking details into a clean card layout. Selecting a client row within the extension populates an interactive layout showing contact history, client statuses, and public details instantly.
*   **Feature 3: One-Click Outbound Communication**  
    Reduces window shifting. Provides instant communication hotkeys within the dashboard viewport to trigger external channels (like default system e-mail handlers) pre-populated with data.
*   **Feature 4: Browser-Wide Background Reminders**  
    Solves the issue of forgotten tasks. Operates an alarm system inside Chrome that runs even when the extension or Google Sheet tabs are completely closed, showing desktop notification popups and visual badge alerts.
*   **Feature 5: Smart Template String Injections**  
    Accelerates outreach. Features a message editing drawer that allows users to save cold text templates containing variables like `{Name}` or `{Company}` that dynamically switch data based on the active row.

---

## 🏗️ 1. System Architecture
The system consists of three interconnected layers designed to preserve performance and pass security compliance:
1. **The Antigravity UI Engine (Flutter Web):** Powers the Side Panel interface, template string injections, and contextual views.
2. **The Native Chrome Bridge (Pure JavaScript):** Manages background tasks, desktop notifications, runtime execution alarms, and system context menus.
3. **The Data Engine (Google Apps Script Webhook):** Operates as a micro-REST API to securely read and append rows to Google Sheets.

---

## 🛠️ 2. Step-by-Step Project Initialization

### Step A: Initialize Flutter Web Application
Execute these shell commands to build your directory structure using strict lowercase naming metrics:
```bash
flutter create antigravity_crm --platforms web
cd antigravity_crm
```

### Step B: Dependencies Configuration
Open your `pubspec.yaml` file located in the root folder and add the following production dependencies:
```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.2.0
  provider: ^6.1.1
  url_launcher: ^6.3.0
```
Save the file and download the packages via your terminal:
```bash
flutter pub get
```

---

## 🌐 3. Chrome Manifest V3 Bridge Setup
Navigate to the `antigravity_crm/web/` directory. You must configure these files to register the compiled web bundle as an organic browser extension.

### Create: `web/manifest.json`
Overwrite the existing default manifest with this precise configuration file:
```json
{
  "manifest_version": 3,
  "name": "Antigravity SheetCRM",
  "version": "1.0.0",
  "description": "Turn your Google Sheets into a powerful CRM using Flutter.",
  "permissions": [
    "contextMenus",
    "storage",
    "sidePanel",
    "alarms",
    "notifications"
  ],
  "host_permissions": [
    "https://google.com*",
    "https://google.com*"
  ],
  "background": {
    "service_worker": "background.js"
  },
  "side_panel": {
    "default_path": "index.html"
  },
  "action": {
    "default_title": "Open Antigravity CRM"
  }
}
```

### Create: `web/background.js`
Create a new file explicitly titled `background.js` inside the `web/` folder to orchestrate off-screen platform processes and link your JavaScript components to your storage layers:
```javascript
// =========================================================================
// ANTIGRAVITY BACKGROUND SERVICE WORKER
// =========================================================================

// Feature 1: Instantiation of the Global Context Menu Scraper
chrome.runtime.onInstalled.addListener(() => {
  chrome.contextMenus.create({
    id: "antigravity_scrape",
    title: "Send '%s' to Antigravity CRM Row",
    contexts: ["selection"]
  });
  
  // Feature 4: Instantiate Background Automation Alarm (Runs Every 60 Minutes)
  chrome.alarms.create("antigravity_check_reminders", { periodInMinutes: 60 });
});

// Feature 1: Context Menu Click Event Execution Listener
chrome.contextMenus.onClicked.addListener((info, tab) => {
  if (info.menuItemId === "antigravity_scrape") {
    // Write selected runtime text data into persistent local extension storage
    chrome.storage.local.set({ lastScrapedText: info.selectionText }, () => {
      // System Action: Auto-deploy sidepanel instance once data ingestion completes
      chrome.sidePanel.open({ tabId: tab.id });
      
      // Notify the active Flutter app instance about the newly scraped item
      chrome.runtime.sendMessage({ action: "textScraped", text: info.selectionText });
    });
  }
});

// Feature 4: Background Polling Alarm Event Core Engine
chrome.alarms.onAlarm.addListener((alarm) => {
  if (alarm.name === "antigravity_check_reminders") {
    // Execution Step: System updates Badge Layer visually to point out overdue activities
    chrome.action.setBadgeText({ text: "!" });
    chrome.action.setBadgeBackgroundColor({ color: "#E53935" });
    
    chrome.notifications.create({
      type: "basic",
      iconUrl: "icons/Icon-192.png",
      title: "Antigravity CRM System Alert",
      message: "You have pending client follow-ups due inside your Google Sheet engine!"
    });
  }
});
```

---

## ⚙️ 4. Data Engine Setup (Google Apps Script)
To connect your Chrome extension to an actual Google Sheet, you need a backend endpoint. Follow these steps to build your micro-API webhook:

1. Open your target Google Sheet.
2. In the top menu, click **Extensions** > **Apps Script**.
3. Clear out all template code and paste the following script:

```javascript
// =========================================================================
// ANTIGRAVITY GOOGLE APPS SCRIPT BACKEND APIS
// =========================================================================

function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  
  try {
    var data = JSON.parse(e.postData.contents);
    
    // Feature 1 Endpoint: Appends a newly scraped lead raw row into your Sheet
    if (data.action === "addLead") {
      sheet.appendRow([
        new Date(), 
        data.name || "Unknown Name", 
        data.email || "Unknown Email", 
        data.company || "Unknown Company", 
        "New Lead", 
        data.notes || ""
      ]);
      
      return ContentService.createTextOutput(JSON.stringify({"status": "success", "message": "Lead written to Sheet."}))
                           .setMimeType(ContentService.MimeType.JSON);
    }
  } catch(error) {
    return ContentService.createTextOutput(JSON.stringify({"status": "error", "message": error.toString()}))
                         .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var rows = sheet.getDataRange().getValues();
  var jsonArray = [];
  
  // Feature 2 & 4 Endpoint: Read existing sheet data entries for UI processing
  // Assumes structure: Column 0: Date, 1: Name, 2: Email, 3: Company, 4: Status
  for (var i = 1; i < rows.length; i++) {
    var row = rows[i];
    jsonArray.push({
      "name": row[1] ? row[1].toString() : "",
      "email": row[2] ? row[2].toString() : "",
      "company": row[3] ? row[3].toString() : "",
      "status": row[4] ? row[4].toString() : ""
    });
  }
  
  return ContentService.createTextOutput(JSON.stringify(jsonArray))
                       .setMimeType(ContentService.MimeType.JSON);
}
```
4. Click **Deploy** > **New deployment**.
5. Select **Web app** as the deployment type.
6. Set **Execute as:** to "Me", and set **Who has access:** to "Anyone".
7. Copy the generated **Web app URL**. You will copy this URL into your Flutter app's network layer.

---

## 💻 5. Core App Implementation (Flutter Dart Engine)
Open your `lib/main.dart` and overwrite its entire block with this unified production template. Replace `YOUR_GOOGLE_APPS_SCRIPT_WEB_APP_URL` with your deployment string:

```dart
import 'package:flutter/material.dart';
import 'package:url_launcher/url_launcher.dart';
import 'package:http/http.dart' as http;
import 'dart:convert';
import 'dart:html' as html; // Handles integration hooks with Chrome storage layers

void main() {
  runApp(const AntigravityCRMApp());
}

