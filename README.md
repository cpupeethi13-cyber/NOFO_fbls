# North Forsyth FBLA — Chapter Website

> Built for the **2026–2027 school year**. Free to host, easy to update, no servers to babysit.

This is a static website (just HTML, CSS, JS) with three special features:

1. 🎬 **Editable video gallery** (YouTube embeds — paste an ID, done)
2. 📅 **Live Google Calendar embed** (you update Google Calendar, the site auto-updates)
3. 💬 **Account-free forum** (powered by Firebase Firestore — free tier)

---

## 📁 What's in this folder

```
nfhs-fbla/
├── index.html      ← page structure (don't usually need to edit)
├── styles.css      ← visual design (colors, layout, typography)
├── script.js       ← officers, videos, forum logic — EDIT THIS to update content
└── README.md       ← you are here
```

---

## 🚀 Quick start: get it online for free in 10 minutes

The fastest, free, permanent way to host this is **GitHub Pages**.

### 1. Make a GitHub account
Go to [github.com](https://github.com) and sign up (use your school email or personal email — just pick one you'll keep).

### 2. Create a new repository
- Click the **+** in the top-right → **New repository**
- Name it something like `nfhs-fbla-website`
- Set it to **Public**
- Click **Create repository**

### 3. Upload these files
- On the new repo page, click **uploading an existing file**
- Drag in `index.html`, `styles.css`, `script.js`, and `README.md`
- Scroll down, click **Commit changes**

### 4. Turn on GitHub Pages
- Click **Settings** (top of the repo page)
- Click **Pages** in the left sidebar
- Under "Source", pick the **main** branch and `/ (root)` folder
- Click **Save**
- Wait ~1 minute. Your site will be live at:
  `https://<your-github-username>.github.io/nfhs-fbla-website/`

That's it. You now have a free, permanent website.

> **Want a custom domain like `nfhsfbla.com`?** You can buy one (~$10/yr from Cloudflare or Porkbun) and point it at GitHub Pages — instructions [here](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site).

---

## ✏️ How to update the site (the four things you'll do most)

Open `script.js` in any text editor (VS Code, Notepad, anything). Everything you'll edit is at the top with comments.

### A. Update the officers
Find the `OFFICERS` array near the top of `script.js`:
```js
const OFFICERS = [
  { name: "Jane Doe",   role: "President" },
  { name: "John Smith", role: "Vice President" },
  ...
];
```
Change the names and roles. Save. Re-upload `script.js` to GitHub. Done.

### B. Add a new video
In `script.js`, find the `VIDEOS` array. Get the YouTube ID from any video URL:
```
https://www.youtube.com/watch?v=ABC123XYZ
                              └────────┘  ← this is the ID
```
Add a new entry:
```js
{
  youtubeId: "ABC123XYZ",
  title:     "Our Big Win at Regionals",
  tag:       "Recap",
  date:      "Mar 2027",
},
```
Save and re-upload.

### C. Swap in YOUR Google Calendar
1. Go to [Google Calendar](https://calendar.google.com)
2. Use either your personal account or — better — a chapter Google account
3. Make a new calendar named "NFHS FBLA"
4. **Settings** → click your new calendar → scroll to **Integrate calendar**
5. Set "Access permissions" → **Make available to public**
6. Copy the **Embed code** (it's an `<iframe>` tag)
7. In `index.html`, find this block:
   ```html
   <iframe
     title="NFHS FBLA Calendar"
     src="https://calendar.google.com/calendar/embed?src=en.usa..."
     ...
   ```
8. Replace that whole `<iframe>` with the one Google gave you
9. Save and re-upload

From now on, anything you add to that Google Calendar shows up on the website automatically.

### D. Update the menu of resources / forms
In `index.html`, find the `<section ... id="resources">` block. Each `<a class="resource-card" ...>` is one tile. Replace `href="#"` with real links to your Google Forms, Google Drive folders, or wherever the form lives.

---

## 💬 Forum setup (the most important part — read carefully)

The forum lets anyone post questions **with no account, no sign-up** — they just type a name (or stay anonymous). It's powered by **Firebase Firestore**, which has a free tier that's *way* more than enough for a school chapter.

Until you set this up, the forum runs in **local-preview mode** (posts only show in your own browser — it's just for designing). Here's how to switch it to a real forum.

### Step 1 — Create a Firebase project (free)

1. Go to [console.firebase.google.com](https://console.firebase.google.com) and sign in with a Google account (use a chapter account if you have one)
2. Click **Create a project**
3. Name it something like `nfhs-fbla-forum`
4. **Disable Google Analytics** (you don't need it) → **Create project**

### Step 2 — Add Firestore Database

1. In the left sidebar, click **Build → Firestore Database**
2. Click **Create database**
3. Choose **Start in production mode** → **Next**
4. Pick a location close to you (e.g., `us-east1`) → **Enable**

### Step 3 — Set the security rules (THIS IS IMPORTANT)

Firestore needs rules to know who can read/write. We want:

- **Anyone** can read posts (it's a public forum)
- **Anyone** can create a post, but only short ones (anti-spam)
- **No one** can edit/delete posts from the website (only you, through the Firebase console)

Click the **Rules** tab in Firestore. Replace what's there with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /forum_posts/{postId} {
      allow read: if true;

      allow create: if
        request.resource.data.title is string &&
        request.resource.data.title.size() > 0 &&
        request.resource.data.title.size() <= 100 &&
        request.resource.data.body  is string &&
        request.resource.data.body.size()  > 0 &&
        request.resource.data.body.size()  <= 1500 &&
        request.resource.data.name  is string &&
        request.resource.data.name.size()  <= 40;

      allow update, delete: if false;
    }
  }
}
```

Click **Publish**.

### Step 4 — Get your Firebase config keys

1. Click the **⚙️ gear** icon → **Project settings**
2. Scroll to **Your apps** → click the **`</>`** (web) icon
3. Give it a nickname like "FBLA website" → **Register app**
4. You'll see a config block that looks like:
   ```js
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "nfhs-fbla-forum.firebaseapp.com",
     projectId: "nfhs-fbla-forum",
     ...
   };
   ```
5. Copy that whole object

### Step 5 — Paste into `script.js`

Open `script.js`, find the `FIREBASE_CONFIG` block:
```js
const FIREBASE_CONFIG = {
  // apiKey: "PASTE-YOUR-API-KEY",
  // ...
};
```
Replace it with the keys you copied (uncomment the lines):
```js
const FIREBASE_CONFIG = {
  apiKey: "AIzaSy...",
  authDomain: "nfhs-fbla-forum.firebaseapp.com",
  projectId: "nfhs-fbla-forum",
  storageBucket: "nfhs-fbla-forum.appspot.com",
  messagingSenderId: "0000000000",
  appId: "1:0000000000:web:abc...",
};
```

Save, re-upload `script.js` to GitHub. The forum is now live and real.

> **Are these keys secret?** No — Firebase web API keys are designed to be public. Your security rules (Step 3) are what actually protect the database.

### Step 6 — Moderating the forum

If someone posts something inappropriate:
1. Go back to **Firebase console → Firestore Database**
2. Open the `forum_posts` collection
3. Find the post, click the **⋮** menu → **Delete document**

> **Tip:** Bookmark your Firebase console. As Webmaster, checking it once a week takes 10 seconds.

---

## 🛠 Common questions

**Q: I made a change but the site still shows the old version.**
A: Browsers cache files hard. Hit **Ctrl+Shift+R** (or **Cmd+Shift+R** on Mac) to force a refresh. Also wait ~30 seconds after pushing to GitHub for Pages to rebuild.

**Q: The forum says "local-preview mode."**
A: You haven't filled in `FIREBASE_CONFIG` in `script.js` yet. Follow the Forum setup section above.

**Q: How do I change the colors?**
A: In `styles.css`, scroll to the very top — `:root { ... }`. Change the color variables there. They cascade through the whole site.

**Q: Can someone spam the forum?**
A: The Firestore rules limit post length, and Firebase has built-in abuse protection on the free tier. If spam ever becomes a real problem, you can add Google reCAPTCHA or require an emailed token — but for a high school chapter forum, the rules above are usually enough.

**Q: How do I hand this off next year?**
A: Add the next Webmaster as a **Collaborator** on the GitHub repo (Settings → Collaborators) and as a member of the Firebase project (Project settings → Users and permissions).

---

## 🎨 Want to customize the look further?

In `styles.css`:
- The fonts are loaded from Google Fonts at the top of `index.html`. To change them, swap the Google Fonts link and update `--font-display` / `--font-body` in `:root`.
- All section spacing, colors, and corner radius values are CSS variables — easy to tweak.

In `index.html`:
- Each `<section>` is independent — feel free to delete or duplicate any of them.
- The hero stats, the FBLA marquee text, and the "Why join?" list are all hard-coded in HTML — search for the text and edit it directly.

---

## ✅ Final checklist before you go live

- [ ] Replace `[Adviser Name]`, `[Room ###]`, and the email in the footer + join section
- [ ] Update the `OFFICERS` array with the real 26–27 officer team
- [ ] Add real YouTube IDs to the `VIDEOS` array (delete the placeholder ones)
- [ ] Swap in your real Google Calendar embed
- [ ] Replace `href="#"` on the resource cards with real links
- [ ] Set up Firebase and paste keys into `FIREBASE_CONFIG`
- [ ] Replace `[Your Name]` in the footer with your name as Webmaster
- [ ] Show your adviser before publishing (institutional approval — part of your role!)

---

Built with 💼 for North Forsyth FBLA — Chapter № 13442.
