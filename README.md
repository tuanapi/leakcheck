# leakcheck

A static page that tells you whether your username appears in a specific leaked account list, without publishing the list.

Type a username → the page hashes it **in your browser** → compares against a file of SHA-256 hashes → says whether it is present. Nothing is sent anywhere; there is no backend.

## What is in here

| file | contents |
|---|---|
| `index.html` | the page — no data, no tracking, no dependencies |
| `hashes.json` | `sha256(lowercase(username))` for 2,185 accounts, plus the count |

**No usernames. No passwords. No password hints, prefixes, lengths or masks.** The source dump is not in this repository in any readable form, and this repository cannot reconstruct it.

## How the check works

1. You type `Notch`.
2. The page lowercases it and computes `SHA-256("notch")` with the Web Crypto API.
3. That hex digest is looked up in `hashes.json`.
4. A match means that exact username was in the dump.

Your input never leaves the tab. `crypto.subtle` needs a secure context, so open the page over `https://` (GitHub Pages) or `http://localhost` — `file://` will not work, which is why the footer complains if you double-click the file.

## The honest limitation

2,185 usernames is a small, low-entropy list. Anyone who wants to recover it can hash a dictionary in seconds — a hash list protects the dump from being *read*, not from being *recovered* by someone determined.

So this is a **courtesy check**, not a secret store. It exists so a person can find out whether they are in this dump without the dump being posted in plaintext. If you want better, the data has to live server-side behind a rate-limited lookup, which is what HIBP does.

## If you get a match

1. Change that password **everywhere you reused it** — reused credentials are the actual attack.
2. Use a password manager so every account gets a unique one.
3. Turn on two-step verification on the account, and on the email address that recovers it.
4. Check the same username on [haveibeenpwned.com](https://haveibeenpwned.com).

A miss is not a clean bill of health. It only means this username is not in *this* list.

## Run it locally

```bash
python3 -m http.server 8080
# open http://localhost:8080
```

## Add your own list

```bash
python3 - <<'PY'
import json, hashlib
names = json.load(open('my-usernames.json'))          # ["name", ...] or {"name": ...}
hashes = sorted({hashlib.sha256(n.lower().encode()).hexdigest() for n in names})
json.dump({"algo":"sha256","normalize":"lowercase-trim","count":len(hashes),"hashes":hashes},
          open('hashes.json','w'), separators=(',',':'))
PY
```

Normalisation must match the page: lowercase, trimmed. If you change it, change `sha256Hex` too.

## Deploy

GitHub Pages: **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`**. Everything is static, so that is the whole setup.

## License

MIT.
