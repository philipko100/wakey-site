# wakey-site

The public website for the Wakey iPhone app: a landing page, the privacy policy and terms of use
that App Store Connect requires, and a support page.

Static HTML, no build step. Served by GitHub Pages from `main` at the repo root:

```
https://philipko100.github.io/wakey-site/            index.html
https://philipko100.github.io/wakey-site/privacy/    privacy/index.html
https://philipko100.github.io/wakey-site/terms/      terms/index.html
https://philipko100.github.io/wakey-site/support/    support/index.html
style.css                                            shared styles, light and dark
.nojekyll                                            skip Jekyll; these are plain files
```

Kept in its own public repository because the app repo is private, and GitHub Pages serves only
public repositories on the free plan.

**Every path in these pages is relative** (`../style.css`, `../privacy/`). This is a *project*
site served under `/wakey-site/`, so a root-absolute `/style.css` would 404. The app repo's
`scripts/check-site.sh` fetches every page and every link to prove it. If a custom domain is added
later (`CNAME`), relative paths keep working.

## Editing the privacy policy

The policy states what the app actually does. Verify each claim against the app repo before
editing; they are load-bearing for the App Store privacy label and for the law:

- **Accounts are optional; sign-in is Apple or Google through Supabase** — `Wakey/Services/SupabaseBackend.swift`.
- **Alarms and wake history are stored under the account on Supabase (US, Oregon)** — the `alarms`
  and `wake_log` tables in `supabase/migrations/`.
- **Delete account is immediate and permanent; local alarms stay** — `AccountService.deleteAccount`
  and the `delete-account` Edge Function.
- **Camera frames go to our server and to OpenAI and are not stored** — `supabase/functions/verify-object/`
  (it never logs an image) and `Wakey/Services/ObjectVerifier.swift`.
- **The random install identifier and daily counter** — `Wakey/Services/InstallIdentity.swift`,
  `verification_usage` in the migrations.
- **Purchases through RevenueCat and Superwall** — `Wakey/Services/RevenueCatSuperwallBackend.swift`.
- **Analytics is PostHog, five named events, with the switch in Settings → Privacy** —
  `Wakey/Services/Analytics.swift`, `PostHogAnalytics.swift`, `SettingsView.swift`.
- **Hide My Email works** — nothing in the app needs a real address.
- **No cookies on this site** — there is no script on any page.

`scripts/check-site.sh` in the app repo greps the live policy for the phrases "OpenAI", "PostHog",
"RevenueCat", "Superwall", "Supabase", "Delete account", "Sign in with Apple", "not stored" and
"Hide My Email". Keep them.
