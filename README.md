# Fall 2026 Command Center — web app

Five files, no build step, no dependencies. Open `index.html` and it works.
Everything below is about getting it onto a URL and syncing between devices.

---

## 1. Put it on the web (about 5 minutes, free, no card)

1. Go to github.com and make a new **public** repository called `fall2026`.
2. On the repo page, click **Add file → Upload files** and drag in all five files:
   `index.html`, `sw.js`, `manifest.webmanifest`, `icon-192.png`, `icon-512.png`.
   Do not upload the README if you would rather not, it changes nothing.
3. Commit.
4. Go to **Settings → Pages**. Under *Source* pick **Deploy from a branch**,
   branch `main`, folder `/ (root)`. Save.
5. Wait about a minute. Your URL is `https://YOURNAME.github.io/fall2026/`.

Netlify Drop (`app.netlify.com/drop`) is the faster alternative: drag the folder
onto the page and you get a URL immediately, no account needed to start.

**One caution.** A public repo means anyone who finds the URL can read the page.
The page itself contains no marks, only the course structure, so that is fine.
Do not paste your Supabase keys into any file in the repo — they get typed into
the app's Sync tab at runtime and stay in your browser.

## 2. Add it to your home screen

- **iPhone:** open the URL in Safari (it must be Safari, not Chrome), tap Share,
  then **Add to Home Screen**.
- **Android:** open in Chrome, tap the three dots, then **Install app**.
- **Desktop:** Chrome shows an install icon in the address bar.

It then launches full screen with its own icon and works offline, because the
service worker caches the page on first visit.

## 3. Turn on sync (about 15 minutes, free)

Skip this and the app still works, but your marks live only in the browser you
typed them into. The laptop and the phone will not agree.

1. Sign up at **supabase.com** and create a new project. Any region, any name.
   Note the database password somewhere, though you will not need it here.
2. When it finishes provisioning, open **SQL Editor**, paste this and run it:

   ```sql
   create table semester_state (
     id text primary key,
     data text,
     updated bigint
   );

   alter table semester_state enable row level security;

   create policy "anon full access"
     on semester_state for all
     to anon
     using (true) with check (true);
   ```

3. Go to **Project Settings → API** and copy two things:
   - **Project URL** (looks like `https://abcdefgh.supabase.co`)
   - **anon public** key (the long one labelled `anon`, not `service_role`)
4. Open the app, go to the **Sync** tab, paste both in, and invent a **sync ID**.
   Use something unguessable, like `mustapha-f26-7Kq2xR`. Press *Save and connect*.
5. On every other device, open the same URL and enter the **same three values**.
   Press *Pull from cloud* the first time.

After that it pushes about a second after any change, and pulls whenever you
open or refocus the app.

**What the policy above means.** Anyone who knows all three values can read and
write your data. That is why the sync ID needs to be unguessable and must never
go into the repo. The stored data is only course marks and checkboxes. If you
would rather have real auth, Supabase supports it, but it is more setup than
this is worth.

## 4. If you skip Supabase

The Sync tab has a text box holding your entire state as one line of JSON.
Copy it on one device, paste it on the other, press *Load what is in the box*.
Manual, but it needs no account and no keys.

---

## What is different from the artifact version

- **Gone:** the AI quiz generator. On a public URL it would need your own
  Anthropic API key sitting in the page source where anyone could spend it.
  Keep using the artifact in Claude for practice questions.
- **New:** installable, offline-capable, real URL, optional cross-device sync,
  JSON export.
- **Same:** all 34 graded items, five separate grade scales, the drop-lowest
  rule on FNCE assignments, the what-you-need-on-the-rest calculator, the
  readings tracker, the section conflict checker, the pressure-point analysis.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app. All logic, styles and course data. |
| `sw.js` | Service worker. Caches the page so it opens offline. |
| `manifest.webmanifest` | Makes it installable with a name and icon. |
| `icon-192.png`, `icon-512.png` | Home screen icons. |

## Editing course data later

Everything lives in the `COURSES` and `PREP` arrays near the top of the script
block in `index.html`. Weights are percentages of the final grade and must total
100 per course. Re-upload the file to GitHub and the change is live in a minute.
Your saved marks are keyed by assessment id, so they survive as long as you do
not rename the ids.
