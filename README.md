# leakcheck

A static page that tells you whether your username appears in a specific leaked account list.

Type a username → the page checks it against the list → says whether it is present. No backend, no tracking, no analytics.

## What is in here

| file | contents |
|---|---|
| `index.html` | the page — no data, no dependencies |
| `usernames.json` | the 2,185 usernames that appear in this dump |

**No passwords. No password prefixes, masks, lengths or hashes.** That data was in the source leak and is deliberately not published here. In this dump the stored mask keeps the first 3–5 characters plus the exact length, and that alone recovers **683 of the 2,185 passwords (31%)** with a 10,000-entry wordlist — no cracking rig, no GPU, just a dictionary. The full dump recovers far more.

The source dump is not in this repository.

## How the check works

1. You type `SomeName`.
2. The page lowercases it and looks it up in a `Set` built from `usernames.json`.
3. A match means that username was in the dump.

Your input never leaves the tab. Open the page over `https://` (GitHub Pages) or a local server — `file://` cannot fetch the JSON, which is why the footer complains if you double-click it.

## If you get a match

1. Change that password **everywhere you reused it** — reused credentials are the actual attack.
2. Use a password manager so every account gets a unique password.
3. Turn on two-step verification on the account, and on the email address that recovers it.
4. Check the same username on [haveibeenpwned.com](https://haveibeenpwned.com).

A miss is not a clean bill of health. It only means this username is not in *this* list.

## Scope and limits

Usernames are public identifiers — they are visible in game, on leaderboards, on name-history sites. Publishing the list is publishing what was already public, plus the fact that these particular accounts were in this dump.

That second part is the sensitive bit, and it is why this repo carries a name list and nothing else. If you are in it, the actionable information is "change the password you used here", not "here is your password".

## Run it locally

```bash
python3 -m http.server 8080
# open http://localhost:8080
```

## See the masked passwords on your own machine

The repo ships names only. If you have the source dump, make a local copy of the page with the data inlined — it then opens by double-clicking, with no server and no dependencies:

1. Copy `index.html` next to your `data.json`.
2. In the copy, remove the `fetch("data.json")` block and inline the dump instead: `const database = { ...the parsed data.json... };`
3. Uncomment/enable the button immediately (`submitBtn.disabled = false;`), since nothing loads asynchronously any more.

The lookup code is unchanged — `database[query]` — so you get the original Username + Password rows on your own machine. Name the file `local.html`; it is gitignored here so it cannot be committed by accident.

Do not deploy that file. Serving it is the same act as publishing the dump, one request at a time.

## Add your own list

Replace `usernames.json` with:

```json
{ "count": 2, "usernames": ["name-one", "name-two"] }
```

`count` is display only; the page checks every entry. Keep names lowercase or make sure `trim().toLowerCase()` matches how you write them.

## Deploy

GitHub Pages: **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`**. Everything is static; that is the whole setup.

## License

MIT.
