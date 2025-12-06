# Barnbridge Neighborhood Meeting Poll

A simple web-based scheduling poll to help the Barnbridge neighborhood find the best evening to meet and discuss forming a new neighborhood association.

## Features

- **Date Selection**: Poll for 14 dates (January 1-14, 2026) with Yes/Maybe/No options
- **Real-time Results**: View all responses in a summary table
- **Response Counts**: See how many neighbors selected Yes or Maybe for each date
- **Mobile Friendly**: Responsive design works on all devices
- **No Dependencies**: Single HTML file with vanilla JavaScript

## Quick Start

1. Open `index.html` in a web browser
2. Enter your name
3. Select your availability for each date
4. Click "Submit Response"

## Setup (Backend Required)

To save responses, you need to connect a Google Apps Script backend:

### Step 1: Create a Google Sheet

1. Go to [Google Sheets](https://sheets.google.com) and create a new spreadsheet
2. Name it "Barnbridge Poll Responses"

### Step 2: Create the Apps Script

1. In your Google Sheet, go to **Extensions > Apps Script**
2. Replace the default code with:

```javascript
function doGet(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const data = sheet.getDataRange().getValues();

  if (data.length <= 1) {
    return ContentService.createTextOutput(JSON.stringify({ responses: [] }))
      .setMimeType(ContentService.MimeType.JSON);
  }

  const headers = data[0];
  const responses = data.slice(1).map(row => {
    const obj = {};
    headers.forEach((h, i) => obj[h] = row[i]);
    return obj;
  });

  return ContentService.createTextOutput(JSON.stringify({ responses }))
    .setMimeType(ContentService.MimeType.JSON);
}

function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const data = JSON.parse(e.postData.contents);

  // Set up headers if sheet is empty
  if (sheet.getLastRow() === 0) {
    const headers = ['name', 'jan1', 'jan2', 'jan3', 'jan4', 'jan5', 'jan6', 'jan7',
                     'jan8', 'jan9', 'jan10', 'jan11', 'jan12', 'jan13', 'jan14', 'timestamp'];
    sheet.appendRow(headers);
  }

  // Add response
  const row = [
    data.name,
    data.jan1 || '', data.jan2 || '', data.jan3 || '', data.jan4 || '',
    data.jan5 || '', data.jan6 || '', data.jan7 || '', data.jan8 || '',
    data.jan9 || '', data.jan10 || '', data.jan11 || '', data.jan12 || '',
    data.jan13 || '', data.jan14 || '',
    new Date().toISOString()
  ];
  sheet.appendRow(row);

  return ContentService.createTextOutput(JSON.stringify({ success: true }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

3. Click **Deploy > New deployment**
4. Select **Web app** as the type
5. Set:
   - Execute as: **Me**
   - Who has access: **Anyone**
6. Click **Deploy** and authorize the app
7. Copy the Web app URL

### Step 3: Configure the Poll

1. Open `index.html` in a text editor
2. Find this line near the top of the `<script>` section:
   ```javascript
   const SCRIPT_URL = 'https://script.google.com/macros/s/YOUR_GOOGLE_SCRIPT_ID/exec';
   ```
3. Replace `YOUR_GOOGLE_SCRIPT_ID` with the ID from your deployed web app URL

### Step 4: Deploy

Host the `index.html` file on any web server or static hosting service:
- GitHub Pages
- Netlify
- Vercel
- Any web hosting

## Customization

### Change Meeting Dates

Edit the `dates` array in `index.html`:

```javascript
const dates = [
    { id: 'jan1', day: 'Wed', full: 'January 1, 2026' },
    // ... add or modify dates
];
```

### Change Colors

Modify the CSS custom properties in the `:root` selector:

```css
:root {
    --cream: #FDF8F3;        /* Background */
    --sage: #7C9082;         /* Yes / Primary */
    --terracotta-light: #E8A889;  /* Maybe */
    --light-gray: #DFE6E9;   /* No */
    --charcoal: #2D3436;     /* Text */
}
```

## Project Structure

```
Barnbridge-Poll/
├── index.html    # Complete application (HTML + CSS + JS)
└── README.md     # This file
```

## License

MIT License - Feel free to use and modify for your own neighborhood!
