# Store Stock Lookup

A small, working inventory prototype for **Pan Asia Supermarket**. Built around the interview question: select an item and see the quantity at every store, plus the total, in a popup without opening a new window.

**Sample data only.** The products, quantities, reorder points, and stock movements are fictional. The prototype is not connected to Pan Asia's inventory or POS systems. The store names are the six locations supplied for this demonstration. The visual identity is a simple prototype treatment, not an official company logo.

## Open it

Double-click `index.html` in a modern browser. Everything works directly from this one file, including without an internet connection. There are no packages, frameworks, external fonts, API keys, or build commands.

The ZIP contains:

- `index.html` — the entire application, with inline CSS and JavaScript.
- `README.md` — deployment instructions and a code walkthrough.
- `.nojekyll` — an empty file that tells GitHub Pages to serve the static files directly.

## Publish on GitHub Pages

1. Extract this ZIP. Create a new public GitHub repository, for example `store-stock-lookup`.
2. Upload the **extracted files**, not the ZIP itself. Keep `index.html` at the top level of the repository, not inside another folder. Commit the files to `main`.
3. Open the repository's **Settings → Pages**.
4. Under **Build and deployment**, choose **Deploy from a branch**.
5. Select **main**, choose **/(root)**, and click **Save**.
6. When deployment finishes, use **Visit site** in Pages settings. Share that live link in your interview follow-up. Future commits to `main` update the site.

## A short demonstration

1. Search for **Jasmine** or **RC-10421**, then select **View stores**. The same page opens a native popup showing all six stores and **296 bags** in total.
2. Notice Kansas City has **0 bags**. Select **Apply transfer** for the suggested **35 bags from Nashville to Kansas City**. Kansas City becomes 35, Nashville becomes 69, and the total stays 296.
3. Select **Receive** at Omaha, enter **12**, and confirm. Omaha becomes 30 and the total becomes 308. Close the popup to see the same total in the item list and the updated low-stock report.
4. To show validation, try selling more stock than a store has. The inline error explains the available quantity and no stock changes.
5. Refresh to restore the original sample data. Search for **Sriracha** to demonstrate the “no spare stock” case, or **Sushi Rice** to demonstrate a fully stocked item.

The core store lookup is the main feature. Receive, Sell, the low-stock report, and transfer suggestions are supporting examples of practical internal tools.

## Behavior and rules

- Search matches a name or SKU, without case sensitivity; category and search filters combine.
- The `items` array is the only inventory source. Its six quantities and six reorder points follow the order in `stores`.
- An item is **Out at a store** if any location has zero; otherwise **Low at a store** if any location is below its own reorder point; otherwise **In stock**. Exactly meeting the reorder point counts as in stock.
- The report lists each item–store alert separately, so one item can count at several stores. It always covers the full catalog; the item-list search does not hide report alerts.
- Receive and Sell accept positive whole units. Overselling, zero, negatives, decimals, and unsafe numeric values are rejected before any mutation.
- Every successful change redraws the item list, report, summary counts, and open popup from the same data.
- Transfers prioritize empty stores, then the largest deficits. Each destination is matched to the store with the most remaining surplus. A suggestion appears only if that one source can fully cover the deficit without falling below its own reorder point.
- The planner reserves proposed amounts in a temporary calculation, so two suggestions cannot promise the same stock. Actual inventory changes only when an Apply transfer button is selected. Transfers preserve the total.
- Bar lengths compare store quantities with the highest quantity for that item; the number and status label remain authoritative.
- Escape, either Close button, or a click on the backdrop closes the popup. Keyboard users can tab to a row and press Enter or Space. Arrow keys move between the two tabs. Focus returns to the item after closing, with a fallback if the alert has disappeared.
- The interface uses a navy header, neutral surfaces, and blue action buttons; red, amber, and green identify stock status. The theme follows the device's light/dark preference. On smaller screens, tables scroll inside their own containers and the main Category column is hidden.
- All data-derived text inserted through HTML templates is escaped. Other dynamic text uses `textContent`.

## Code walkthrough

`index.html` is organized into five clearly commented JavaScript sections:

1. **Data and helpers** — `stores`, `items`, `totalFor`, `storeStatusFor`, `statusFor`, `filteredItems`, and `escapeHtml`.
2. **Transfer planning** — `suggestTransfers` calculates safe suggestions without changing inventory.
3. **Writes** — `applyChange` and `applyTransfer` validate before changing quantities.
4. **Rendering** — `renderAll` rebuilds all views; dedicated functions handle the item list, report, popup, and transfer section.
5. **Interaction** — delegated click/keyboard/submit handlers keep working after rows are rendered again. `finishChange` redraws the views, restores focus, and announces the result.

The CSS begins with the color variables and dark-mode overrides, then the page, tables, dialog, and responsive styles. It uses the system font to open immediately without downloading assets.

## Moving beyond the prototype

This is intentionally an in-memory demonstration. **Changes disappear on refresh, are not shared between devices, and are not real stock transactions.** Publishing the HTML does not make it a production inventory system.

The JavaScript comments mark the two integration points:

- Load the catalog and per-store balances from an authenticated inventory database or POS API instead of hard-coded arrays.
- Save transactions through a server API with permissions, validation, an audit record, and idempotency. A transfer must update both stores in one database transaction. Use record versions or locking to handle simultaneous edits and return the confirmed balances for rendering.

For an interview walkthrough, explain these boundaries directly: this prototype demonstrates the workflow and calculations; a shared production system needs the backend integration.

## Verification

The delivered version was checked with JavaScript syntax validation, functional tests of filters and stock rules, mutation and rendering checks using a lightweight DOM stub, randomized transfer-invariant tests, and static HTML/CSS review. These checks cover totals, overselling, thresholds, transfer conservation, surplus reservation, escaping, and empty states.

A real browser was unavailable in the build environment, so native dialog behavior and visual layouts were not browser-tested there. Before sending the link, open it on your phone and laptop and run the short demonstration above, including closing the popup with Escape, testing its buttons, and scrolling the tables on a narrow screen.
