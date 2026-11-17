# leakcheck

A static page that tells you whether your username appears in a specific leaked account list.

Type a username → the page checks it against the list → says whether it is present. No backend, no tracking, no analytics.

## What is in here

| file | contents |
|---|---|
| `index.html` | the page — no data, no dependencies |
| `usernames.json` | the 2,185 usernames that appear in this dump |

**No passwords. No password prefixes, masks, lengths or hashes.** That data was in the source leak and is deliberately not published here: with the first few characters and the exact length, the rest of a password falls to a wordlist in seconds.

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
