# คิดเงินไข่

One-screen calculator for writing egg cash bills (เบอร์ 0–5, priced per แผง). See [REQUIREMENTS.md](REQUIREMENTS.md).

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

1. Open the URL in Chrome on the phone.
2. Menu ⋮ → *Add to Home screen* / *Install app* → name stays “คิดเงินไข่”.
3. Open from the 🥚 icon; it runs full-screen.

Prices and the current bill are stored only on that phone (`localStorage`, key `khai:v1`). Clearing Chrome's site data resets them.
