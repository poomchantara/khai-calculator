# คิดเงินไข่

One-screen calculator for writing egg cash bills (เบอร์ 0–5, priced per แผง). See [REQUIREMENTS.md](REQUIREMENTS.md).

**Live:** https://poomchantara.github.io/khai-calculator/

Plain static files — no build step, no dependencies.

## Run locally

```sh
python3 -m http.server 8000
# open http://localhost:8000
```

On a computer you can also use the keyboard: digits, Backspace, Delete/Esc (C), ↑/↓/Enter, ←/→ to switch between price and แผง.

## Deploy (GitHub Pages)

1. Push this folder to a **public** GitHub repo (`khai-calculator`).
2. Repo → Settings → Pages → Source: *Deploy from a branch* → `main` / `(root)`.
3. Site appears at `https://poomchantara.github.io/khai-calculator/` after a minute.

## Install on Dad's phone

1. Open the URL on the phone. A link tapped in LINE hands itself over to the phone's default browser (Samsung Internet or Chrome), because LINE's own browser hides the bottom keys.
2. Add it to the home screen. In Samsung Internet: menu ≡ → *Add page to* → *Home screen*. In Chrome: menu ⋮ → *Add to Home screen* / *Install app*. The name stays “คิดเงินไข่”.
3. Open from the 🥚 icon; it runs full-screen.

The layout scales to any phone and ignores the phone's font-size setting, which only made text overlap; the app's text is already large.

Prices and the current bill are stored only on that phone, in that browser (`localStorage`, key `khai:v1`). Clearing the browser's site data resets them.
