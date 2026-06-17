# Sofascore Football Match Shotmap Scraper ⚽🥅

This repository contains a Python-based Jupyter Notebook that automates the extraction of comprehensive, granular shot map data from a dynamic football match page on Sofascore (specifically tailored for the Bologna vs. Borussia Dortmund fixture).

Using **Selenium WebDriver**, the script interacts with dynamic UI components, dismisses modal disruptions, handles tab-switching, and systematically parses both on-field spatial coordinates ($x, y$) and metric statistics for every shot taken during the match.

---

## 📌 Features

* **Banners & Pop-up Handling:** Automatically detects and closes cookie banners and language selection overlays to prevent UI blockages during element interactions.


* **Dynamic Tab Navigation:** Targets and executes JavaScript-driven clicks to navigate straight to the match's *Statistics* section.


* **Team-Specific Dual Scraping:**
* **Bologna:** Loops through the left UI tabpanel container, interacting with button controls to index shots via custom unique identifiers (`minute|shooter|start_x`).


* **Borussia Dortmund:** Transitions to the right tabpanel, locating the respective team toggle buttons to extract the opposing squad's shot collection.




* **Granular Metric Extraction:** Parses explicit variables per shot event, including minute, shooter name, Expected Goals ($xG$), Expected Goals on Target ($xGOT$), Outcome, and Situation.


* **Spatial Mapping Coordinates:** Captures dynamic SVG elements to record exact starting coordinates (`cx`, `cy`) on the pitch map alongside shot arrival placement coordinates extracted from SVG `transform: translate()` attributes.


* **Data Cleaning Pipeline:** Standardizes text values (e.g., handles character normalizations like replacing `ī` with `i`, and maps missing $xGOT$ placeholders directly to Python `None`) before organizing into a structured Pandas DataFrame.



---

## 🛠️ Requirements & Installation

The script utilizes standard scraping and data processing libraries. To set up your local environment, run the embedded notebook environment cell or execute the following command in your terminal:

```bash
pip install requests beautifulsoup4 pandas openpyxl lxml selenium

```

### Pre-requisites

* **Google Chrome** installed on the host system.


* An operational installation of Chrome WebDriver matching your local browser version (Selenium handles automated management natively in newer editions).



---

## 📂 Code Pipeline Architecture

1. **Driver Initialization:** Launches a Chrome instance and navigates directly to the specified Sofascore match URL.


2. **UI Setup:** Handles modal dismissals, transitions focus directly onto the match statistics tab, and waits for target container rendering via `WebDriverWait` rules.


3. **Bologna Scraping:** Iterates through available shot sequences inside `#tabpanel-left`, saving the metric metadata and the positional markers.


4. **Team Switch:** Finds and scrolls down to the selection button for Dortmund, toggles focus, and re-triggers the matching event loop logic inside `#tabpanel-right`.


5. **Data Structuring:** Joins the shot lists from both teams into a master Pandas DataFrame, applies targeted data-cleaning procedures, and presents the tabular dataset.


6. **Teardown:** Terminates the active browser session cleanly via a comprehensive `finally` block to protect local system memory resources.



---

## 📊 Extracted Data Schema

The finalized output matrix is organized with the following feature structures:

| Column Field | Description | Type / Format |
| --- | --- | --- |
| `team` | Name of the attacking squad (e.g., Bologna, Borussia Dortmund)

 | String |
| `shot_number` | Chronological iteration index of the shot within the scraping sequence

 | Integer |
| `minute` | The specific minute of the match when the shot event occurred

 | String (e.g., `45'`) |
| `shooter` | Name of the player executing the shot (normalized character string)

 | String |
| `xG` | Expected Goals metric value for the shot opportunity

 | Numeric String |
| `xGOT` | Expected Goals on Target (normalized to `None` if blocked or off-target)

 | Numeric String / None |
| `outcome` | Categorized result of the play (e.g., Saved, Blocked, Goal, Missed)

 | String |
| `situation` | The contextual nature of the attack (e.g., Open Play, Set Piece)

 | String |
| `start_x` / `start_y` | Spatial pixel grid coordinates where the shot was fired on the pitch map

 | Numeric String |
| `arrival_x` / `arrival_y` | Spatial frame coordinates where the ball arrived on the goal framework

 | Numeric String / None |

---

## ⚠️ Notes & Troubleshooting

> [!WARNING]
> **Dynamic Web Element Identifiers:** Sports data providers like Sofascore consistently optimize layout classes and tracking protocols. If the target page encounters structural shifts, explicit relative selectors (like the rigid XPath maps used to find the `Statistics` anchor or team selector nodes) might throw a `NoSuchElementException`. If this occurs, inspect the modified target source HTML and re-map the breaking CSS selector or XPath variables accordingly.
> 
>
