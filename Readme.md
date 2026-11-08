<div align="center">

# ✈️ Yatra Automation · Selenium WebDriver Project

**Java + Selenium WebDriver 4 script that opens the fare calendar on yatra.com, finds the cheapest date in the current and next month, and compares the two**

<p>
<img src="https://img.shields.io/badge/Java-11%2B-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white" alt="Java 11+"/>
<img src="https://img.shields.io/badge/Selenium-4.44.0-43B02A?style=for-the-badge&logo=selenium&logoColor=white" alt="Selenium 4.44.0"/>
<img src="https://img.shields.io/badge/Maven-Build-C71A36?style=for-the-badge&logo=apachemaven&logoColor=white" alt="Maven"/>
<img src="https://img.shields.io/badge/Chrome-WebDriver-4285F4?style=for-the-badge&logo=googlechrome&logoColor=white" alt="Chrome WebDriver"/>
</p>
<p>
<img src="https://img.shields.io/badge/Eclipse-IDE-2C2255?style=for-the-badge&logo=eclipseide&logoColor=white" alt="Eclipse IDE"/>
<img src="https://img.shields.io/badge/XPath-Locators-6A1B9A?style=for-the-badge" alt="XPath locators"/>
<img src="https://img.shields.io/badge/Explicit-Waits-00897B?style=for-the-badge" alt="Explicit waits"/>
<img src="https://img.shields.io/badge/Target-yatra.com-E5322D?style=for-the-badge" alt="Target yatra.com"/>
</p>
<p>
<img src="https://img.shields.io/badge/Built%20with-Claude%20Code-D97757?style=for-the-badge" alt="Built with Claude Code"/>
</p>

[Overview](#overview) · [Workflow](#workflow) · [Tech stack](#tech-stack) · [Quick start](#quick-start) · [Key concepts](#key-concepts) · [Challenges](#challenges) · [Improvements](#improvements) · [Author](#author)

</div>

---

<a id="overview"></a>
## 📖 Overview

A Selenium WebDriver (Java) script that automates the flight fare calendar on [yatra.com](https://www.yatra.com). It opens the **Departure Date** calendar, reads the fare shown under every date for the **current month** and the **next month**, picks the cheapest date in each, and then prints which of the two months has the lower fare.

<a id="workflow"></a>
## 🔄 What the script does

1. Opens Chrome with browser notifications disabled, goes to `https://www.yatra.com` and maximizes the window.
2. Waits for the promotional pop-up and closes it with its **✕** icon.
3. Clicks the **Departure Date** field to open the fare calendar.
4. Takes the first two month panels from the date picker (current month and next month).
5. Reads every fare in each month, removes the `₹` symbol and commas, and finds the lowest one.
6. Reads the date belonging to that fare from its day cell's `aria-label`.
7. Prints the cheapest date and fare for each month.
8. Compares the two monthly lows and prints the cheaper one (or says both are the same).

<a id="tech-stack"></a>
## 🛠️ Tech stack

| Tool | Version / Notes |
|------|-----------------|
| Java | JDK 11 or later (Selenium 4 needs at least Java 11) |
| Selenium Java | 4.44.0 |
| Build tool | Maven |
| Browser | Google Chrome (Selenium Manager downloads the matching ChromeDriver automatically) |
| IDE | Eclipse (project files included) |

## 📁 Project structure

```
Yatra-Automation-/
├── pom.xml                                   # Maven config with the Selenium dependency
└── src/main/java/comyatraautomation/
    └── YatraAutomationScript.java            # The automation script (main method)
```

### Helper methods

| Method | What it does |
|--------|--------------|
| `closePopUp()` | Waits for the promo pop-up and clicks its close icon |
| `clickOnDepatureDate()` | Opens the departure-date calendar |
| `selectTheMonthFromCalendar()` | Returns a month panel by index (0 = current month, 1 = next month) |
| `getMeLowestPrice()` | Finds the cheapest fare in a month and returns it with its date |
| `compareTwoMonthsPrices()` | Compares the two monthly lows and prints the result |

<a id="quick-start"></a>
## 🚀 Quick start

### Prerequisites
- JDK 11+
- Maven 3.x
- Google Chrome

### Clone
```bash
git clone https://github.com/ArpitChoubey/Yatra-Automation-.git
cd Yatra-Automation-
```

### Run

**From Eclipse / IntelliJ:** import it as a Maven project, open `YatraAutomationScript.java` and run it as a Java Application.

**From the command line:**
```bash
mvn compile exec:java -Dexec.mainClass="comyatraautomation.YatraAutomationScript"
```

### 🖥️ Sample output
```
<number of month panels>
<number of month panels>
<Date label for current month>--- Price is Rs<lowest fare>
<Date label for next month>--- Price is Rs<lowest fare>
The lowest price for the two months is<lowest fare>
```

<a id="key-concepts"></a>
## 🧠 Key concepts used

- **ChromeOptions** with `--disable-notifications` to stop browser prompts from interrupting the run
- **Explicit waits** (`WebDriverWait` + `ExpectedConditions`): `visibilityOfElementLocated`, `elementToBeClickable` and `visibilityOfAllElementsLocatedBy`
- **Partial-match XPath** with `contains(@class, ...)`, plus stable attributes such as `aria-label`, `role` and `alt`
- **Relative (chained) locators** such as `.//span[...]`, which search only inside one month panel
- **Parent traversal** with `.//../..` to go from a fare to the day cell that holds its date
- **Reading attributes** with `getAttribute("aria-label")`
- **String parsing** (`replace`, `substring`, `Integer.parseInt`) to turn fare text into numbers
- **Reusable helper methods**, with the same method used for both months

<a id="challenges"></a>
## 🧩 Challenges faced and how they were solved

### 1. Promotional pop-up blocking the page
Yatra shows a promotional pop-up as soon as the page loads, and it covers the booking widget. Clicking anything behind it throws an `ElementClickInterceptedException`.
**Solution:** wait for the pop-up to become visible (`visibilityOfElementLocated`), find the close icon inside it with a relative XPath (`.//img[@alt="cross"]`) and click it before doing anything else.

### 2. Browser notification prompts
Chrome's "Allow notifications" prompt appeared over the page and got in the way of the automation.
**Solution:** start Chrome with `ChromeOptions` and the `--disable-notifications` argument, so the prompt never shows up.

### 3. Dynamic, auto-generated class names
Yatra is a React app. Many of its class names carry generated suffixes (for example `style_popup__xY12z`) that can change between builds, so locators that match the full class name break quickly.
**Solution:** match only the stable part of the class with `contains()`, as in `//div[contains(@class,"style_popup")]` and `.//span[contains(@class,"custom-day-content")]`. Where possible, use accessibility attributes instead, such as `//div[@aria-label="Departure Date inputbox" and @role="button"]`.

### 4. Two months that look identical
The date picker shows two months side by side, and both have exactly the same structure. A page-wide search for fares returns prices from both months mixed together.
**Solution:** first collect the month containers (`react-datepicker__month-container`) and pick one by index, then search **relative to that container** with `.//` XPaths so each month's fares stay separate.

### 5. Fares load after the calendar
The calendar opens first and the fares are filled in afterwards. Reading the dates straight away gave empty text or incomplete fares.
**Solution:** explicit waits for the calendar and month panels to become visible. The fares keep arriving after that, so a short pause is added before reading them (see [improvements](#improvements) for a better approach).

### 6. Parsing prices from the UI
Fares are shown as text such as `₹4,120`, and some dates (past dates, or dates without a fare) show no price at all. `Integer.parseInt` fails on both.
**Solution:** skip empty entries, strip the `₹` symbol and commas, and only then convert the text to an `int`, so fares can be compared as numbers.

### 7. Linking a price back to its date
The fare `<span>` doesn't contain the date. The date only exists in the `aria-label` of the day cell two levels above it.
**Solution:** keep a reference to the cheapest price element, move up to its day cell with `.//../..`, and read its `aria-label` to get a readable date.

### 8. Comparing two formatted results
Each month's result is returned as a single string (date + price), so the two prices can't be compared directly.
**Solution:** find the `Rs` marker with `indexOf`, cut out the number with `substring`, and convert it with `Integer.parseInt` before comparing.

<a id="improvements"></a>
## 📈 Possible improvements

- Replace `Thread.sleep()` with an explicit wait that checks the fares have loaded
- Close the browser at the end with `driver.quit()`, ideally in a `finally` block
- Handle a month with no fares, which currently causes a `NullPointerException`
- Make closing the pop-up optional, so the script doesn't fail when the pop-up doesn't appear
- Return the price as a number (or a small result object) instead of parsing it back out of a string
- Restructure the code with the **Page Object Model** (POM)
- Add **TestNG/JUnit** with assertions, plus reporting (e.g. Extent Reports)
- Move the URL, the months to compare and the timeouts into a config file or command-line arguments
- Support headless runs for CI (e.g. GitHub Actions)
- Add logging (Log4j/SLF4J) in place of `System.out.println`

<a id="author"></a>
## 👤 Author

**Arpit Choubey** – [GitHub](https://github.com/ArpitChoubey)

> This project is for learning and practice only. It is not affiliated with Yatra.
