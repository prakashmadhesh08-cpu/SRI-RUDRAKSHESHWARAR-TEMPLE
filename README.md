# Rudraksheshwarar Shiva Temple — Next.js Website

Full rebuild of the temple website on **Next.js 16 (App Router)**, with a built-in
admin panel, JSON-file content database, image/video uploads, and a fully
editable theme (colors, font) — no redeploy needed for content or look changes.

## Stack

- **Next.js 16** (App Router, Server Components, Route Handlers as the API layer)
- **React 19**
- **Tailwind CSS 4**
- File-based JSON "database" in `/data` (swap for a real DB later if you outgrow it)
- `jsonwebtoken` + an HttpOnly cookie for admin sessions, `bcryptjs` for password hashing

## Project structure

```
src/
  app/
    page.js                       # Public homepage (server component)
    layout.js                     # Root layout, theme + fonts
    admin/page.js                  # /admin dashboard (client) — login + editor
    api/
      content/route.js             # GET public content
      admin/login/route.js         # POST password -> session cookie
      admin/logout/route.js
      admin/session/route.js       # GET current auth state
      admin/password/route.js      # PUT change admin password
      admin/reset/route.js         # POST restore default content
      admin/messages/route.js      # GET contact messages (auth)
      contact/route.js             # POST contact form -> data/messages.json
      upload/route.js              # POST image/video upload -> public/uploads
  components/                      # Hero, About, History, Gallery, Festivals,
                                    # Donations, Contact, Footer, Header, ThemeStyle
  components/admin/                 # Login, Dashboard, ImageUploader, AdminApp
  lib/
    store.js                       # JSON read/write helpers
    auth.js                        # JWT session helpers
data/
  content.json                     # LIVE site content — edit via /admin
  content.default.json             # Backup used by "Reset to defaults"
  admin.json                       # Hashed admin password
  messages.json                    # Contact form submissions
public/uploads/                    # Uploaded images/videos, served statically
```

## Quick start

```bash
npm install
cp .env.example .env     # then edit SESSION_SECRET
npm run dev
```

- Website: http://localhost:3000
- Admin: http://localhost:3000/admin (default password: `PS18` — change immediately)

For production:

```bash
npm run build
npm start
```

## Editing everything from the browser

Once logged in at `/admin`, with no code changes and no redeploy, you can:

- Edit every text block (hero, about, history, importance, contact, etc.)
- Add / edit / delete gallery images, videos, festivals, announcements
- Upload images and videos directly into `public/uploads`
- Show/hide Donations, edit UPI ID and QR code
- Change the entire theme — primary/secondary/accent colors, background,
  text color, font family — applied instantly site-wide
- View contact form submissions
- Change the admin password
- One-click "Reset to defaults"

All changes save to `data/content.json` immediately.

## API reference

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/content` | - | Full site content |
| PUT | `/api/content` | yes | Save full content object (theme, sections, everything) |
| POST | `/api/admin/login` | - | `{ password }` -> session cookie |
| POST | `/api/admin/logout` | - | Clears session |
| GET | `/api/admin/session` | - | `{ authed }` |
| POST | `/api/admin/reset` | yes | Restore default content |
| PUT | `/api/admin/password` | yes | Change admin password |
| GET | `/api/admin/messages` | yes | List contact messages |
| POST | `/api/contact` | - | Submit contact form |
| POST | `/api/upload` | yes | Multipart upload, returns `{ url }` |

## Performance notes

- Homepage is server-rendered straight from disk JSON, fast first paint for every visitor.
- Uploads served as static files (CDN-cacheable on Vercel/Netlify/Nginx).
- Admin JS only loads on `/admin`, never shipped to regular visitors.

## Deploying with your own domain

1. Push this folder to a Git repo.
2. Deploy to Vercel (zero-config for Next.js) or any Node host (PM2 + Nginx works too).
3. Set env vars on the host: `SESSION_SECRET`, `ADMIN_PASSWORD`, `NODE_ENV=production`.
4. Point your domain's DNS at the host, enable HTTPS.
5. Log into `/admin`, change the password, start editing.

## Legacy file

`rudraksheshwarar_v5.html` (original single-file version) is kept for reference only; not used at runtime.
