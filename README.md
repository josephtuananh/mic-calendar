# MIC Congregation Calendar

A browser-based annual calendar for the MIC (Marian Fathers of the Immaculate Conception) congregation. The application generates twelve monthly pages in landscape A4 format and combines:

- Gregorian dates
- Vietnamese lunar dates
- Vietnamese and English weekday and month names
- Catholic liturgical feasts and seasons
- Special liturgical-day colors
- Marian virtues in unused calendar cells
- Lunar New Year information
- Optional PDF export

The project is written with plain HTML, CSS, and JavaScript, so it does not require Node.js or a build system.

## Features

- Generates a complete twelve-month calendar in one browser window
- Supports leap years
- Calculates the lunar date for every Gregorian date
- Calculates Easter using the Gregorian Meeus-Jones-Butcher algorithm
- Builds the liturgical calendar from the Easter year
- Includes:
  - Advent
  - Christmas
  - Lent
  - Holy Week
  - Easter
  - Ordinary Time
  - Movable solemnities, feasts, and memorials
- Displays solemnities, feasts, memorials, Sundays, and special observances with distinct colors
- Uses an A4 landscape layout suitable for printing
- Can export the generated calendar to PDF with browser print or JavaScript PDF libraries

## Preview

Open `index.html` in a browser, or use a local development server. The calendar is rendered automatically when the page loads.

The main application is located at [index.html](./index.html).

## Requirements

- A modern desktop browser, such as Chrome, Edge, or Firefox
- Internet access for:
  - jsPDF
  - html2canvas
  - Google Fonts
  - The MIC logo loaded from Wikimedia Commons
- A PDF printer or PDF export option in the browser, if saving through `Ctrl+P`

No npm packages or server-side dependencies are required.

## Getting Started

### Option 1: Open Directly

1. Download or clone the repository.
2. Open `index.html` in a browser.
3. Wait for the monthly calendars to be generated.

Opening the file directly may restrict some browser features or external resources. A local server is recommended.

### Option 2: Use VS Code Live Server

The repository includes a Live Server configuration in `.vscode/settings.json` on port `5501`.

1. Open the repository in Visual Studio Code.
2. Install the **Live Server** extension.
3. Right-click `index.html`.
4. Select **Open with Live Server**.
5. Visit `http://localhost:5501/` if the browser does not open automatically.

### Option 3: Use a Simple Local HTTP Server

From the repository directory, start one of the following servers.

With Python:

```bash
python -m http.server 5501
```

With Node.js and `npx`:

```bash
npx serve . -l 5501
```

Then open `http://localhost:5501/`.

## Change the Calendar Year

The output year is configured in [js/const.js](./js/const.js):

```javascript
const yearOfCalendar = 2027;
```

To generate a calendar for another year:

1. Open `js/const.js`.
2. Change `yearOfCalendar`.
3. Save the file.
4. Refresh the browser.

The value must be a valid Gregorian year. The liturgical calculations support Easter years from 1583 through 4099.

The year input in `index.html` is currently a control element, but `generateCalendars()` obtains the year from `yearOfCalendar` in `const.js`. Editing the number field alone does not change the rendered year.

## Printing the Calendar

The page is styled for A4 landscape printing.

1. Open the generated calendar.
2. Press `Ctrl+P`.
3. Choose **Save as PDF**, or select a physical printer.
4. Set paper size to **A4**.
5. Set orientation to **Landscape**.
6. Set margins to `0` or **Minimum**.
7. Enable background graphics if your browser provides that option.
8. Print or save.

There is a blank cover page inserted during the browser print shortcut. The implementation first adds a `.fake-calendar` element and then restores the original calendar content after the print operation.

In browser print settings, scale should normally be `100%`, `Default`, or `Fit to page`, depending on your browser.

## PDF Export with JavaScript

The project contains PDF export functions:

- `downloadPDF()` in [js/generateCalendar.js](./js/generateCalendar.js)
- `exportPDF()` in [js/exportCal.js](./js/exportCal.js)

`downloadPDF()` captures each `.calendar` element with html2canvas and adds each result as a page in jsPDF.

The PDF export buttons in `index.html` are currently commented out. To enable `downloadPDF()` after editing or adding a visible button:

```html
<button onclick="downloadPDF()">Download PDF</button>
```

An internet connection is required for external jsPDF and html2canvas scripts unless they are downloaded locally.

## Project Structure

```text
.
├── index.html                         # Main application page
├── README.md
├── base64fonts/                       # Embedded base64 font data
├── css/
│   ├── font.css                       # Custom font declarations
│   ├── index.css                      # Calendar layout and print styles
│   └── modal.css                      # Processing modal styles
├── font/
│   └── font-title/                    # TTF files for titles and numbers
├── html/
│   ├── catholic-calendar.html         # Standalone Catholic calendar prototype
│   └── liturgicalCalendar.html        # Liturgical calculator interface
├── image/
│   ├── logo.png
│   └── Artboard 7.png
└── js/
    ├── const.js                       # Year, weekday, priority, and virtue data
    ├── exportCal.js                   # Alternate PDF export functions
    ├── functions.js                   # Month-name conversion helpers
    ├── generateCalendar.js            # Main twelve-month calendar renderer
    ├── generateLiturgicalCalendar.js  # Easter and liturgical-calendar calculations
    ├── lunar.js                       # Vietnamese lunar calendar calculations
    ├── modal.js                       # Modal open and close helpers
    └── renderLiturgicalCalendar.js    # Rendering for the liturgical calculator
```

## How the Calendar Is Generated

1. `index.html` loads CSS, external PDF libraries, fonts, and JavaScript files.
2. The inline `generateCalendars()` call starts the renderer.
3. [js/generateLiturgicalCalendar.js](./js/generateLiturgicalCalendar.js) calculates Easter and movable feasts.
4. [js/generateCalendar.js](./js/generateCalendar.js) creates twelve `.calendar` elements.
5. [js/lunar.js](./js/lunar.js) converts each Gregorian date to the Vietnamese lunar date.
6. Liturgical data is matched to each day.
7. [css/index.css](./css/index.css) applies layout, print, weekend, feast, and solemnity styles.

## Lunar Calendar

The lunar conversion is implemented in [js/lunar.js](./js/lunar.js). It uses a GMT+7 timezone and converts each Gregorian date with:

```javascript
convertSolar2Lunar(day, month, year, 7);
```

The resulting array contains:

```text
[lunarDay, lunarMonth, lunarYear, leapMonthFlag]
```

- When the lunar day is `1`, the date is displayed as `day/month`.
- Leap lunar months are marked with `Nhuận`.
- Lunar New Year's Day is labeled `Tết Nguyên Đán`.

## Liturgical Calendar

The liturgical engine is implemented in [js/generateLiturgicalCalendar.js](./js/generateLiturgicalCalendar.js).

Its main entry function is:

```javascript
buildLiturgicalYearForEasterYear(year);
```

To extend or modify feast data, start with the item construction in `buildLiturgicalYearForEasterYear()` and the movable feast calculation in `computeMovableFeasts()`.

Liturgical priority values are declared in [js/const.js](./js/const.js):

| Priority | Meaning |
|---|---|
| `solemnityOverride` | Solemnity that replaces a Sunday observance |
| `solemnity` | Solemnity |
| `feastOverride` | Feast that replaces a Sunday observance |
| `feast` | Feast |
| `memorialOverride` | Memorial that replaces an existing observance |
| `memorial` | Memorial |
| `sunday` | Sunday or ordinary liturgical observance |

Because priorities determine both text and CSS classes, verify the generated month view after changing feast logic.

## Customization

### Fonts

The primary custom fonts are loaded from [css/font.css](./css/font.css):

- `TitleFont`
- `fontNumber`

The font files are stored in `font/font-title/`.

The page also loads the following Google Fonts:

- `WDXL Lubrifont TC`
- `Crimson Text`

To replace a font, update both the `@font-face` declaration and the matching CSS selector.

### Colors and Cell Styles

Most calendar colors are defined in [css/index.css](./css/index.css). Common selectors include:

- `.sunday`
- `.saturday`
- `.sundayOrFeast`
- `.solemnity`
- `.feast`
- `.memorial`
- `.espicialSunday`
- `.verySolemnity`
- `.ash`

For example, to change the Sunday/feast text color:

```css
.sundayOrFeast,
.solemnity {
  color: #be1e2d;
}
```

### Page Size

The printed calendar uses the A4 landscape aspect ratio from [css/index.css](./css/index.css).

```css
.calendar {
  width: 100%;
  max-width: 300mm;
  max-height: 210mm;
  aspect-ratio: 297 / 210;
}
```

When changing page dimensions, also update the PDF orientation and print settings.

### Logo

The current renderer creates a Wikimedia image URL dynamically in [js/generateCalendar.js](./js/generateCalendar.js):

```javascript
logo.src =
  "https://upload.wikimedia.org/wikipedia/commons/b/b4/MIC_COA_2009.png";
```

To use the local project logo, replace that URL with:

```javascript
logo.src = "./image/logo.png";
```

Using a local or bundled logo avoids network dependency and can improve PDF export reliability.

## Development Notes

- Use a local HTTP server rather than repeatedly opening `file://`.
- Keep JavaScript load order intact. Liturgical and lunar functions must be loaded before `generateCalendars()`.
- Use browser developer tools (`F12`) to inspect JavaScript errors.
- Test one target year, then print or export it before changing layout-wide styles.
- PDFs created from HTML and CSS can vary slightly between browsers.
- External fonts and images can cause CORS or loading delays.

## Troubleshooting

### The Calendar Does Not Appear

- Confirm that `index.html` can access the repository files.
- Open the browser console and check for JavaScript errors.
- Make sure `js/lunar.js`, `js/const.js`, `js/functions.js`, and `js/generateLiturgicalCalendar.js` load successfully.
- Serve the repository through HTTP instead of opening it directly.

### The Wrong Year Is Displayed

Update `yearOfCalendar` in [js/const.js](./js/const.js), then refresh the page.

### Lunar Dates Are Missing or Incorrect

- Confirm that the timezone argument is `7` in `convertSolar2Lunar()`.
- Verify the Gregorian day, month, and year passed to the lunar function.
- Do not change lunar conversion output without checking lunar month and leap-month behavior.

### PDF Export Is Unavailable

The UI buttons may be commented out in `index.html`. Either enable `downloadPDF()` or use `Ctrl+P` and save as PDF.

If calling `downloadPDF()` from the console or a button produces a network error:

- Check internet access.
- Confirm that jsPDF and html2canvas are loaded.
- Serve the page locally.
- Verify that external logo images allow cross-origin canvas rendering.

### Printed Output Is Too Small or Too Large

- Set print orientation to landscape.
- Set paper size to A4.
- Set scaling to `100%` or `Fit`.
- Remove extra browser print headers and footers.
- Set print margins to minimum or zero.

### Font Does Not Display Correctly

- Confirm that files in `font/font-title/` exist.
- Confirm paths in [css/font.css](./css/font.css).
- Reload with browser cache disabled.
- Check for a missing font weight when using `fontNumber`.
