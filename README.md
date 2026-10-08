# PhishGuard Extensions

**A suite of lightweight Chrome extensions that scan web pages for phishing tactics, with specialized detectors for banking, business, education, and crypto scams.**

Each extension reads the text of the page you are on, looks for the psychological tricks phishing pages rely on (urgency, fear, fake authority, reward bait), and gives the page a **risk score from 0 to 100** with a plain-English explanation. A score of 50 or above is labelled **Fake**, otherwise **Good**.

---

## Features

- **Five detectors**: pick the one that matches your use case, or use the combined one for everything
- **One-click scanning** from the toolbar popup
- **Risk score and explanation** listing exactly which tactics were detected
- **Dark, glassmorphic popup UI** with colour-coded results (Safe / Low Risk / High Risk)
- **Runs entirely in your browser**: no servers, no API calls, no data leaves your machine
- **Minimal permissions**: only `activeTab`
- **Built-in test pages** for every detector so you can try it safely

## Detectors

| Folder | What it looks for |
|---|---|
| `banking-phishing-detector` | Urgency, fear of financial loss, account suspension threats, fake bank authority, "verify your account" pressure |
| `business-phishing-detector` | Fake invoices, urgent payment demands, corporate login requests, vendor/supplier scams, business email compromise |
| `education-phishing-detector` | Fake scholarships, exam-answer scams, student portal verification, diploma mills, "update your transcript" pressure |
| `crypto-phishing-detector` | Airdrop bait, wallet-connect requests, seed phrase prompts, forced transaction signing, FOMO manipulation |
| `combined-phishing-detector` | All of the above in a single extension |

## Installation

No build step is needed.

1. Clone or download this repository
   ```bash
   git clone https://github.com/mullangisathvika-24/phishguard-extensions.git
   ```
2. Open Chrome and go to `chrome://extensions/`
3. Turn on **Developer mode** (top right)
4. Click **Load unpacked** and select a detector folder (repeat for each one you want)
5. Pin the extension from the puzzle-piece menu so it is easy to reach

## Usage

1. Open any web page
2. Click the extension icon in the toolbar
3. Click **Scan Current Page**
4. Read the result: score, label, and detected tactics

## Testing

Every detector ships with a `test-page.html` containing deliberately fraudulent sample content.

1. From the project root, start a local server:
   ```bash
   python -m http.server 8000
   ```
2. Open a test page, for example:
   ```
   http://localhost:8000/banking-phishing-detector/test-page.html
   ```
3. Click the extension icon, then **Scan Current Page**
4. The test page should be flagged as **Fake**; a legitimate page should come back **Good**

> Use `http://localhost`, not `file://`, otherwise the content script will not run on the page.

## How it works

```
popup.js --"scan" message--> content.js --> reads page text + title
   ^                              |
   |                              v
   +---- { result, riskScore, explanation } <-- keyword rules, weighted and capped at 100
```

- `content.js` runs on every page and waits for a `scan` message
- It lowercases the page text and title, then checks for phrase groups tied to specific manipulation tactics
- Each matched tactic adds weight to the score (typically 10 to 25 points); the total is capped at 100
- `popup.js` sends the message and renders the response in `popup.html`

## Project structure

```
.
├── banking-phishing-detector/
├── business-phishing-detector/
├── education-phishing-detector/
├── crypto-phishing-detector/
├── combined-phishing-detector/
│   ├── manifest.json     # Manifest V3 config
│   ├── content.js        # Detection logic
│   ├── popup.html        # Popup UI
│   ├── popup.js          # Scan button + result rendering
│   └── test-page.html    # Sample phishing content
├── README.md
└── TODO.md
```

## Limitations

This is a **learning/prototype project**, not a replacement for real protection such as Google Safe Browsing or a dedicated security product.

- Detection is **keyword-based**, so it can produce false positives (a genuine bank page may say "account locked") and false negatives (a well-written phishing page may avoid the trigger phrases)
- It analyses **page text only**: no URL reputation, domain age, SSL checks, or form/link inspection
- Scanning is **manual**; pages are not checked automatically as you browse

## Roadmap

- [ ] Analyse URLs and domains (typosquatting, lookalike characters, suspicious TLDs)
- [ ] Inspect forms, hidden fields, and link targets that differ from their visible text
- [ ] Optional automatic scan on page load with a badge icon
- [ ] Shared detection module to remove duplicated logic between detectors
- [ ] Configurable keyword lists and thresholds
- [ ] Extension icons and Chrome Web Store listing

## Contributing

Contributions are welcome. Fork the repo, create a branch, make your changes, and open a pull request. Ideas for new detectors (e.g. e-commerce, government, social media) are especially appreciated.

## Author

Made by **Sathvika** | [GitHub](https://github.com/mullangisathvika-24)
