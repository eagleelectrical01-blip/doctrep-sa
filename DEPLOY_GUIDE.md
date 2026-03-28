# DoctRep SA — Free Deployment Guide
## GitHub Pages + Firebase (100% Free)

---

## WHAT YOU WILL END UP WITH

| App | URL | Who uses it |
|-----|-----|------------|
| Doctor Portal | https://eagleelectrical01-blip.github.io/doctrep-sa/doctor-portal/ | Doctors on PC or tablet |
| Rep App | https://eagleelectrical01-blip.github.io/doctrep-sa/rep-app/ | Reps on mobile phone |

Both talk to Firebase in real-time. Rep in Ladysmith books — Doctor in Durban sees it instantly.

---

## STEP 1 — CREATE FIREBASE PROJECT (free)

1. Go to https://console.firebase.google.com
2. Click "Add project" → name it "doctrep-sa"
3. Disable Google Analytics (not needed yet)
4. Click "Continue" → project is created

### Enable Authentication
1. Left menu → Build → Authentication → Get started
2. Click "Email/Password" → Enable → Save

### Enable Firestore Database
1. Left menu → Build → Firestore Database → Create database
2. Choose "Start in test mode" (gives you 30 days free access)
3. Choose region: europe-west2 (closest to SA)
4. Click Enable

### Get Your Firebase Config
1. Left menu → Project Settings (gear icon)
2. Scroll down → "Your apps" → click "</>" (Web app)
3. App nickname: "doctrep-web" → Register app
4. COPY the firebaseConfig object — you will paste this into both HTML files

It looks like this:
```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "doctrep-sa.firebaseapp.com",
  projectId: "doctrep-sa",
  storageBucket: "doctrep-sa.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

### Set Firestore Security Rules
1. Firestore → Rules tab → paste this:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /doctors/{doctorId} {
      allow read, write: if request.auth != null && request.auth.uid == doctorId;
    }
    match /slots/{slotId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null;
    }
    match /notifications/{notifId} {
      allow read, write: if request.auth != null;
    }
    match /reps/{repId} {
      allow read, write: if request.auth != null && request.auth.uid == repId;
    }
  }
}
```

2. Click "Publish"

---

## STEP 2 — ADD FIREBASE CONFIG TO BOTH FILES

### In doctor-portal/index.html
Find this section (near the bottom of the file):
```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  ...
};
```
Replace it with your actual config from Step 1.

### In rep-app/index.html
Same thing — find the firebaseConfig section and replace with your config.

---

## STEP 3 — CREATE GITHUB REPO AND UPLOAD FILES

1. Go to https://github.com/eagleelectrical01-blip
2. Click "New repository"
3. Name: doctrep-sa
4. Set to Public
5. Click "Create repository"

### Upload your files
You need this folder structure:
```
doctrep-sa/
├── doctor-portal/
│   ├── index.html
│   ├── manifest.json
│   ├── sw.js
│   └── icons/
│       ├── icon-192.png
│       └── icon-512.png
├── rep-app/
│   ├── index.html
│   ├── manifest.json
│   ├── sw.js
│   └── icons/
│       ├── icon-192.png
│       └── icon-512.png
└── README.md
```

Option A — Use GitHub Desktop (easiest):
1. Download GitHub Desktop from desktop.github.com
2. Clone your new repo
3. Drag all your files into the folder
4. Commit and Push

Option B — Upload directly on GitHub website:
1. In your repo, click "Add file" → "Upload files"
2. Drag all files → Commit

---

## STEP 4 — ENABLE GITHUB PAGES (free hosting)

1. In your GitHub repo → Settings tab
2. Left menu → Pages
3. Under "Source" → select "Deploy from a branch"
4. Branch: main → Folder: / (root) → Save
5. Wait 2-3 minutes
6. Your URLs will be:
   - Doctor Portal: https://eagleelectrical01-blip.github.io/doctrep-sa/doctor-portal/
   - Rep App:       https://eagleelectrical01-blip.github.io/doctrep-sa/rep-app/

---

## STEP 5 — CREATE THE APP ICONS

You need icon-192.png and icon-512.png for both apps.
Use Canva (free) to create them:
1. Go to canva.com → Create design → Custom size
2. 512 x 512 pixels
3. Design a simple icon — teal background, "DR" or a stethoscope
4. Download as PNG → rename to icon-512.png
5. Download again → resize to 192x192 → icon-192.png
6. Put copies in both /doctor-portal/icons/ and /rep-app/icons/

---

## STEP 6 — TEST ON YOUR DEVICES

### Install Doctor Portal on PC (Chrome)
1. Open Chrome on your PC
2. Go to: https://eagleelectrical01-blip.github.io/doctrep-sa/doctor-portal/
3. Look for the install icon (⊕) in the address bar on the right
4. Click it → "Install"
5. DoctRep Doctor Portal opens as a standalone app on your desktop
6. Pin it to your taskbar

### Install Rep App on your Android phone
1. Open Chrome on your phone
2. Go to: https://eagleelectrical01-blip.github.io/doctrep-sa/rep-app/
3. Chrome will show a banner at the bottom: "Add DoctRep Rep to Home screen"
4. Tap "Add" → it appears on your home screen like a real app
5. Tap the icon → opens full screen, no browser bar

### Test across different internet connections
- Doctor Portal: open on your PC at home (home WiFi)
- Rep App: open on your phone (mobile data / different WiFi)
- Register as a doctor on the portal
- Register as a rep on the phone app
- Add a slot on the doctor portal → should appear on the rep app within seconds
- Book a slot on the rep app → should appear as PENDING on the doctor portal within seconds
- Approve on the doctor portal → rep app shows APPROVED notification

This proves it works across different internet connections — Ladysmith to Durban.

---

## FIRESTORE DATA STRUCTURE

Your Firebase database will automatically create these collections:

| Collection | What it stores |
|-----------|---------------|
| doctors | Doctor profiles (name, specialty, practice, city) |
| reps | Rep profiles (name, company, ID, contact) |
| slots | All rep slots (date, time, duration, status, who booked) |
| notifications | Messages sent between doctor and rep |

---

## COSTS (all free at this scale)

| Service | Free limit | Cost above limit |
|---------|-----------|-----------------|
| Firebase Auth | Unlimited | Free forever |
| Firestore | 50,000 reads/day, 20,000 writes/day | $0.06 per 100k reads |
| GitHub Pages | 1GB storage, 100GB bandwidth/month | Upgrade to paid plan |
| Custom domain (optional) | — | R200/year for .co.za |

For testing with 10-50 users: completely free.
For 100+ active doctors: still free on Firebase.
Only pay when you scale to thousands of users.

---

## ADDING WHATSAPP NOTIFICATIONS (later — still free for testing)

When a doctor approves/declines, the rep gets an in-app notification.
For real WhatsApp messages, you can use:
- Twilio WhatsApp Sandbox (free for testing, ~$0.005/message in production)
- CallMeBot API (completely free, but limited)
- WhatsApp Business API (via Meta — requires business verification)

This is a Phase 2 item — get the app working first.

---

## SHARING WITH DOCTORS TO TEST INTEREST

Before spending any money, send this to a doctor you know:
"Hi Dr. [Name], I'm testing a new app called DoctRep SA that helps doctors manage medical rep visit slots from their PC — no installation needed, just open a link. Can I show you how it works and get your feedback? It's completely free. Link: [your GitHub Pages URL]"

If 5 out of 10 doctors you approach say yes, you have product-market fit.
If they say no, you find out why and adjust — before spending a cent.

---

## QUESTIONS? NEXT STEPS

Once the prototype is live and tested across real devices:
1. Get feedback from 5-10 real doctors
2. Get feedback from 5-10 real medical reps (Cornelia's network)
3. Decide what to build next based on their feedback
4. Only then consider Google Play submission or paid hosting

Good luck Johan — this stack will take you further than you think for free.
