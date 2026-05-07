# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A home-sale storefront backed by Firebase. Products (image, name, price, description) are stored in **Firestore** and images in **Firebase Storage**. There is no build step — the `public/` directory is served directly via Firebase Hosting.

## Local Development & Deployment

Requires the [Firebase CLI](https://firebase.google.com/docs/cli):

```bash
npm install -g firebase-tools
firebase login

# Serve locally (Hosting + SDK auto-config)
firebase serve

# Deploy to production
firebase deploy
```

The Firebase project is `hello-world-bce9c` (`.firebaserc`). `/__/firebase/init.js` is auto-served by Firebase Hosting and injects the project config — it only works under `firebase serve` or after deploy, not by opening files directly.

## Architecture

```
public/
  index.html   # Storefront — reads products from Firestore
  admin.html   # Admin panel — Google sign-in, add/delete products
  404.html     # Custom error page
```

**Stack:** Alpine.js 3 (reactivity) + Tailwind CSS (CDN, utility styling) + Firebase JS SDK v12.12.1 compat mode. No bundler, no npm dependencies in the app.

**Data model — Firestore collection `products`:**
```
{
  name:        string
  price:       number
  description: string         // optional
  imageUrl:    string         // Firebase Storage download URL
  createdAt:   timestamp
}
```

**Storefront** (`index.html`): fetches all products ordered by `createdAt desc`, does client-side search (name + description) and pagination (6 per page).

**Admin** (`admin.html`): authenticates with Google, uploads image to `products/{timestamp}_{filename}` in Storage, saves document to Firestore. Deleting a product also deletes its Storage object. Access is gated by a hardcoded `AUTHORIZED_UID` constant at the top of the inline `<script>` — only the Firebase UID matching that value is granted the dashboard. To authorize a different account, replace that string with the new user's Firebase UID.

## Code Review Workflow

Use the **pr-review** skill whenever you ask Claude Code to review code changes.

## Required Firebase Setup (one-time, in the Firebase Console)

1. **Firestore** — Create database in production mode.
2. **Storage** — Enable Firebase Storage.
3. **Authentication** — Enable the Google sign-in provider.

### Firestore Security Rules

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /products/{id} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

### Storage Security Rules

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /products/{allPaths=**} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

Only the Google account used to sign in to `admin.html` can write. All visitors can read.

## Adding Products (for non-technical users)

1. Open `https://<your-site>/admin.html` in a browser.
2. Click **"Continuar con Google"** and sign in.
3. Fill in the form: upload a photo, enter name, price, and optional description.
4. Click **"Publicar artículo"** — it appears on the storefront immediately.
5. To remove an item, click the trash icon next to it in the list.
