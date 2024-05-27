# email-to-linkedin-google-sheet
# Email to LinkedIn and Company Information Google Sheet

## Overview
This guide will help you set up a Google Sheet that can find company information and LinkedIn profiles based on an email address. It's a powerful tool for anyone in sales, marketing, or recruitment who wants to quickly gather valuable information about contacts.

## What's Inside:
- **Setup Requirements:** Tools and accounts you'll need.
- **Step-by-Step Instructions:** Detailed setup guide.
- **Custom Scripts:** Google Apps Script to automate the process.

## Setup Requirements
Before we start, make sure you have:
1. A Google account to access Google Sheets.
2. Basic knowledge of Google Sheets and Google Apps Script.

## Step-by-Step Instructions

### Step 1: Create a New Google Sheet
1. Open [Google Sheets](https://sheets.google.com) and create a new spreadsheet.
2. Name your spreadsheet (e.g., `Email to LinkedIn Finder`).

### Step 2: Set Up Your Sheet
1. Label the first column as **Email**.
2. Label the second column as **Company Information**.
3. Label the third column as **LinkedIn Profile**.

### Step 3: Open the Script Editor
1. Click on **Extensions** in the menu.
2. Select **Apps Script**.

### Step 4: Add Custom Script
1. Delete any code in the script editor and replace it with the following script:

```javascript
function getLinkedInProfile(email) {
  var url = `https://api.hunter.io/v2/email-finder?domain=${email.split('@')[1]}&api_key=YOUR_HUNTER_API_KEY`;
  var response = UrlFetchApp.fetch(url);
  var json = response.getContentText();
  var data = JSON.parse(json);
  if (data.data && data.data.linkedin) {
    return data.data.linkedin;
  } else {
    return 'LinkedIn profile not found';
  }
}

function getCompanyInfo(email) {
  var domain = email.split('@')[1];
  var url = `https://company.clearbit.com/v2/companies/find?domain=${domain}&api_key=YOUR_CLEARBIT_API_KEY`;
  var response = UrlFetchApp.fetch(url);
  var json = response.getContentText();
  var data = JSON.parse(json);
  if (data) {
    return data.name + ', ' + data.description;
  } else {
    return 'Company information not found';
  }
}

function findInfo() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var range = sheet.getDataRange();
  var values = range.getValues();
  
  for (var i = 1; i < values.length; i++) {
    if (values[i][0] !== '') {
      var email = values[i][0];
      var companyInfo = getCompanyInfo(email);
      var linkedInProfile = getLinkedInProfile(email);
      sheet.getRange(i + 1, 2).setValue(companyInfo);
      sheet.getRange(i + 1, 3).setValue(linkedInProfile);
    }
  }
}
