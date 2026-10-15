# Balloon

Balloon is a Chrome extension for tracking online programming practice. It saves problems from LeetCode, Codeforces, CodeChef, GeeksforGeeks, HackerRank or any other site, reminds you when to revise them, and shows your progress over time.

It is built with Manifest V3, plain HTML, CSS and JavaScript. All data stays in your browser.

## Features

- Save a problem from the popup or from a small button shown on the problem page
- Title, platform, difficulty and topics are filled in automatically where the site allows it
- Duplicate detection: the same problem opened through different links is stored once
- Spaced revision queue with a badge on the toolbar icon showing how many problems are due
- Library with search, filters, sorting, editing and deletion
- Statistics: streak, progress, difficulty and platform breakdown, and an activity heatmap
- Export and import of your data as JSON, light and dark themes

## Architecture

The extension has three parts that run in separate contexts and communicate through messages and shared storage.

| Part | Files | Responsibility |
|---|---|---|
| Popup | `index.html`, `style.css`, `popup.js` | The user interface: logging form, revision queue, library and statistics |
| Content script | `content.js` | Runs on supported problem sites, reads page details and shows the save button |
| Service worker | `background.js` | Updates the toolbar badge and handles quick-save requests from the content script |

Shared logic lives in `lib/` and is imported by both the popup and the service worker:

| Module | Responsibility |
|---|---|
| `storage.js` | The only module that reads or writes `chrome.storage`; add, update, delete, import and export |
| `platforms.js` | Platform detection, URL normalisation and title cleaning |
| `review.js` | Revision schedule rules (1, 3, 7, 14 and 30 day intervals) |
| `insights.js` | Pure functions for streaks, heatmap data, statistics, filtering and sorting |
| `dates.js` | Date helpers |

Data flow: the content script sends a message to the service worker, which saves through `storage.js`. The popup reads and writes through the same module, so there is a single source of truth. Problems are stored in `chrome.storage.local`.

Permissions used: `storage`, `activeTab` and `alarms`. The content script runs only on the supported problem sites.

## Setup

1. Download or clone this repository.
2. Open `chrome://extensions` in Chrome.
3. Turn on **Developer mode** in the top right corner.
4. Click **Load unpacked** and select the project folder.
5. Pin the Balloon icon to the toolbar.

To use it, open a problem page and click the icon, or use the round button at the bottom right of the page. After changing any code, click the refresh icon on the extension card and reload the problem tab.

## Notes

- Automatic detection of difficulty and topics depends on each site's page layout. When it cannot find a value, the field is left empty and can be filled in by hand.
- If choosing a file to import closes the popup, open Balloon in a tab from the Statistics view and import from there.
