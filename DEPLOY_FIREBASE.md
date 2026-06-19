# Firebase Hosting — app-ads.txt fix (AdMob)

Why: `username.github.io` is on the **Public Suffix List**, which AdMob's
app-ads.txt crawler can't reliably verify. Firebase Hosting is Google's own
recommended alternative and verifies reliably. We host the **same** files here
(app-ads.txt, privacy-policy.html, terms.html, index.html) at a `*.web.app`
domain and point the App Store **Marketing URL** to it.

`firebase.json` is already configured (public dir = this folder). You only run
the steps below — they need your Google login, so they can't be automated.

## One-time setup

Run everything from this folder:
`C:\Users\leand\Documents\leanderpro.github.io`

```sh
# 1. Install the Firebase CLI (once, globally)
npm install -g firebase-tools

# 2. Log in with your Google account (opens a browser)
firebase login

# 3. Create a Firebase project. The ID must be globally unique — if this one is
#    taken, append something (e.g. -app, -2026) and reuse that id in step 4.
firebase projects:create fair-interval-timer --display-name "Fair Interval Timer"

# 4. Select that project for this folder
firebase use fair-interval-timer

# 5. Deploy the site (uploads app-ads.txt, privacy, terms, index)
firebase deploy --only hosting
```

After deploy, your site is live at:
- `https://fair-interval-timer.web.app/`
- `https://fair-interval-timer.web.app/app-ads.txt`

(Replace `fair-interval-timer` with whatever project id you used.)

## Verify it serves correctly

```sh
curl -i https://fair-interval-timer.web.app/app-ads.txt
```
Expect `200 OK`, `Content-Type: text/plain`, and the single line:
`google.com, pub-9495314412057064, DIRECT, f08c47fec0942fa0`

## Point the store + AdMob at it

1. **App Store Connect** → app → 1.0.x version → **Marketing-URL** =
   `https://fair-interval-timer.web.app/` → Save.
   (Leave the Privacy Policy URL as-is; only the developer/marketing URL needs to
   match the app-ads.txt domain. Apple may take a few hours to reflect it.)
2. **AdMob** → Apps → Fair Interval Timer → app-ads.txt panel →
   **"Nach Updates suchen"**. With a `web.app` domain it verifies reliably
   (usually within a few hours, up to ~24h).

## Future updates

To change the hosted files later, edit them here and run:
```sh
firebase deploy --only hosting
```
