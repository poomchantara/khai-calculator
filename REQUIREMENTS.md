# คิดเงินไข่ — Requirements (v1)

## Purpose

Replace the physical calculator Dad uses when writing paper cash bills (บิลเงินสด) for egg sales. The app computes each size's line amount and the grand total so he can copy them onto the bill. **The paper bill is the record.** The app is only a calculator.

## User & context

- Dad, 61. Wears reading glasses. Comfortable with LINE/YouTube. Used to a physical calculator.
- Samsung Galaxy A57 (6.7"), portrait, one screen. Used indoors under shop lighting. Internet available.
- Many bills per day, so speed beats everything else. **No animations.**
- All UI text in Thai. Digits are Arabic (0–9).

## Domain rules

| Thing | Rule |
|---|---|
| Sizes | เบอร์ 0, 1, 2, 3, 4, 5 — six fixed rows, always shown, 0 at top |
| Unit | แผง (tray) |
| Quantity | Whole number, 0–999 per size |
| Price | Whole baht per แผง, 0–9,999. No decimals |
| Line amount | qty × price |
| Total | Sum of line amounts, plus total แผง count |
| Price memory | One price per size. **It stays until Dad changes it.** Wholesale customers: Dad overwrites the price for that bill, then changes it back himself |

**Out of scope for v1:** empty or returned tray tracking (แผงเปล่าค้าง/คืน), customer names, bill history or daily totals, multiple price lists, discounts, VAT, printing, amount in Thai words.

## Screen

There is a single screen with no navigation and no settings page. It must fit on the A57 **without scrolling**, both in a browser tab and when launched from the home screen. It must also fit on any other modern phone (320 px wide and up, so employees can use it), including with the phone's font size set to maximum.

```
 12/10/69                              [บิลใหม่]
 ┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
 เบอร์      ราคา        แผง           เงิน
   0         125         10          1,250
   1         120        [ 5]           600      ← active cell
   2         115           –             –
   3         110           –             –
   4         105           –             –
   5         100           –             –
 ─────────────────────────────────────────────
 รวม 15 แผง                      1,850 บาท     ← biggest text on screen
 ─────────────────────────────────────────────
     7          8          9          ⌫
     4          5          6          ▲
     1          2          3        ถัดไป
     ────── 0 ──────       C          ▼       ← ถัดไป ▼ is one tall key
```

- **Header:** today's date as `dd/mm/yy` in the Buddhist era (2026-10-12 → `12/10/69`), matching how the date is written on the bill. A **บิลใหม่** button on the right. While a price is being edited, the whole header turns yellow and shows `กำลังแก้ราคา เบอร์ N` instead of the date. The header keeps the same height, so nothing below it moves.
- **Table:** columns are เบอร์ | ราคา | แผง | เงิน. Empty or zero values show as a grey `–`.
- **Total bar:** `รวม N แผง` and `X,XXX บาท`. Commas as thousands separators everywhere.
- **Keypad:** fixed at the bottom. Uses calculator order (7-8-9 on top). No 00 key; 0 is double width.

## Interaction

### Selecting
- Exactly one active cell at a time.
- On app open and after บิลใหม่, the active cell is **เบอร์ 0 → แผง**.
- Tapping anywhere on a row except its price selects that row's **แผง** cell.
- Tapping a **price** selects that price. The cell turns bright yellow and the header turns yellow and reads `กำลังแก้ราคา เบอร์ N`. Showing this loudly is the guard against accidental price edits, because a wrong price persists across bills.

### Keypad
- **Digit:** the first digit after selecting a cell *replaces* its value; later digits append. Digits beyond 3 (qty) or 4 (price) are ignored.
- **⌫:** deletes the last digit. Empty means 0 and displays `–`.
- **C:** clears the active cell.
- **ถัดไป ▼ / ▲:** move down or up **within the same column**, so from a แผง cell they never jump into a price. At row 5, ▼ stays put; at row 0, ▲ stays put. This makes updating all six prices fast: tap the เบอร์ 0 price, then type, ▼, type, ▼, and so on.
- Totals recalculate on every key press, with no "=" step.
- Each key press gives a 10 ms vibration (`navigator.vibrate`), ignored if unsupported.

### บิลใหม่ (new bill)
- Instantly clears all quantities. Prices are kept. The active cell resets to เบอร์ 0 → แผง.
- For 10 seconds, the button turns into **เอาคืน** (undo), which restores the previous quantities. The undo window also ends on the first keypad press of the new bill.
- Taps on เอาคืน within 600 ms of it appearing are ignored, so a double-tap on บิลใหม่ doesn't undo itself.
- If the bill is already empty, it only moves the cursor back to เบอร์ 0 → แผง, and no undo is offered.

### Missing price
- If a row has qty > 0 but no price, its เงิน cell shows a red `ใส่ราคา`. The total still sums the other rows.

### Persistence
- Prices and the current bill (quantities plus active cell) are saved to `localStorage` on every change, under the single key `khai:v1`. They survive switching to LINE, reloads and phone restarts.
- All storage access is wrapped in try/catch, so the app still works for the session if storage is unavailable.

### Screen on
- Request a Screen Wake Lock while the app is visible, and re-acquire it on `visibilitychange`. This stops the screen from going dark while Dad writes on the paper.

## Visual

- Light theme only: white background, near-black text, no dark mode.
- Active แผง cell: thick blue outline. Active price cell: bright yellow fill.
- Font: **Sarabun**, self-hosted woff2, with the system Thai font as fallback. Numbers are right-aligned and use `tabular-nums`.
- The screen is designed for **412 × 720 CSS px** (the A57 in a browser tab). Every size (boxes, gaps and text) is scaled by `min(width / 412, height / 720)`, so the whole design always fits. Extra height on taller screens goes to the rows and the keypad.

  | Part | Design height |
  |---|---|
  | Header | 52 |
  | Column labels | 28 |
  | Rows (6 × 46) | 276 |
  | Total | 66 |
  | Keypad (4 × 64 keys + gaps) | 298 |
  | **Sum** | **720** |

- Sizes at the design size, smaller in proportion on shorter or narrower screens:
  - Row text 31px
  - Total 46px
  - Keypad digits 38px
  - Keys 64px tall
- **Phone font size:** Android's font-size setting (which LINE's browser follows) enlarges web text by up to ~1.7× without enlarging the boxes, so text overlaps. The app measures this boost on a hidden 100px probe and undoes it. The text is already sized for reading glasses, and every phone shows the same layout.
- **Fit guard:** after every render, any text still wider or taller than its box (very long amounts, a fallback font, a boost that couldn't be measured) shrinks to fit. Boxes never grow to fit their text.

- Zero motion:
  - No transitions, animations, hover effects or ripple effects
  - `touch-action: manipulation` (no double-tap zoom)
  - `user-select: none`
  - `overscroll-behavior: none` (no pull-to-refresh)

## Tech

- Static site with no framework and no build step:
  - `index.html` (inline CSS and JS)
  - `manifest.webmanifest`
  - Icons (192, 512, maskable)
  - The Sarabun font file
- Manifest:
  - name/short_name `คิดเงินไข่`
  - `display: standalone`
  - `orientation: portrait`
  - 🥚 icon on a solid background colour
- No service worker in v1. It avoids stale-version problems after updates, and Dad has internet. Add one later if launch feels slow.
- **LINE's in-app browser** draws its bottom toolbar over the page and hides the keypad's last row. When opened inside LINE (user agent contains `Line/`), the app reloads once with `?openExternalBrowser=1`, which asks LINE to open the phone's default browser (Samsung Internet on Dad's phone). If LINE stays, the app leaves 56px free under the keypad. Outside LINE the flag is removed from the address, so Add to Home screen saves the plain URL.
- `<meta name="color-scheme" content="only light">` stops browser dark modes from recolouring the page.
- Hosting: **GitHub Pages** from a public repo, at `https://poomchantara.github.io/khai-calculator/`. The repo holds no data, because prices live only on the phone.
- Install: open the URL on Dad's phone, then use Add to Home screen.

## Acceptance checks

1. Prices 125/120/115/110/105/100 with qty 10 × เบอร์ 0 and 5 × เบอร์ 1 show 1,250 and 600, then `รวม 15 แผง` and `1,850 บาท`.
2. A 4th qty digit is ignored. 999 × 9,999 on all rows gives `59,934,006 บาท`, which fits on one line.
3. Close the app, open LINE, and return: prices, quantities and the active cell are unchanged.
4. บิลใหม่ clears the quantities and keeps the prices. เอาคืน within 10 seconds restores them. After 10 seconds or a keypad press, it is gone.
5. Tapping a price shows the yellow cell and the yellow `กำลังแก้ราคา` header. ▼ from a price moves to the next price, and from a แผง cell to the next แผง.
6. On the A57, there is no scrolling in either Chrome or Samsung Internet, as a tab or from the home screen.
7. The screen stays on while the app is open.
8. With Android's font size at maximum, the screen looks the same as at the default size. No text overlaps or leaves its box.
9. Tapping the link in a LINE chat opens the app in the phone's default browser. If it stays inside LINE, the 0 and C keys are fully visible.
10. From 320 × 460 up to tablet size, nothing falls off the screen, even with 9,999 × 999 on every row.

## Decisions & alternatives considered

| Decision | Chosen | Rejected |
|---|---|---|
| Wholesale pricing | Overwrite the price inline; it persists | Two price lists; wholesale = retail − X; per-customer prices; per-bill override with auto-revert |
| Input | Built-in calculator keypad | Samsung keyboard (covers half the screen, layout jumps); −/+ steppers (too slow for large qty) |
| Columns | เบอร์ \| ราคา \| แผง \| เงิน | Paper order แผง \| รายการ \| ราคา \| เงิน |
| Price edit guard | Loud yellow highlight and status bar | Long-press (hard to discover); edit-mode toggle (extra taps) |
| Clear safety | Instant clear with 10 s undo | Confirm dialog (slows every bill); no undo |
| History | None | Today's list plus daily total (v2 candidate) |
| Phone font size | Measure the boost and undo it; scale the design to the screen | Keep the boost and shrink each group to fit (sizes would differ from phone to phone) |
| LINE in-app browser | Hand off to the default browser, with toolbar room as fallback | Only reserve toolbar room; ignore LINE |

## v2 candidates

- Today's bills list and daily total, cleared at midnight.
- Empty or returned tray tracking per customer.
