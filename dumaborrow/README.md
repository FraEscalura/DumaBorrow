# DumaBorrow — Dumaguete peer-to-peer rentals

A React + TypeScript + Vite marketplace with a sage-green mobile-first interface and Supabase authentication, Postgres database, RLS, and image storage.

## What works

- Public homepage, eight labeled **sample listings** (not bookable), exploration/search/filtering, item detail pages, responsive navigation, FAQs and draft legal pages.
- Once Supabase is connected: email/password signup/signin/signout, create/edit/pause/publish/remove own listings, image upload, rental date requests, owner acceptance/decline, renter and owner dashboards, reporting and admin hiding of reported items.
- Accepted date ranges cannot overlap, enforced by a PostgreSQL exclusion constraint. Prices are snapshotted by a server-side trigger.
- Proposed 8% fee appears in estimates. **No payments are processed or collected.**

## Android-only setup and deployment (GitHub + Netlify)

1. Unzip this project. In your Android browser, open GitHub, sign in, and create a repository called `dumaborrow`. Upload the project files, preserving the `src/` and `supabase/migrations/` folders. GitHub's web uploader can be awkward for folders on mobile; use a mobile Git client or the browser's desktop-site mode if needed.
2. At https://supabase.com create a project. In **SQL Editor**, paste and run `supabase/migrations/001_initial.sql`. Review all policies before inviting users.
3. In Supabase **Project Settings → API / Connect**, copy the project URL and publishable/anon key. **Never use the service_role key in frontend variables.**
4. In Supabase **Authentication → URL Configuration**, set your deployed site URL and add your deployed URL to redirect URLs. Configure an email provider for production; the default mail service has limits.
5. Open https://app.netlify.com and import the GitHub repository. Set build command `npm run build` and publish directory `dist`.
6. In Netlify **Site configuration → Environment variables**, set `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` from step 3, then trigger a deploy.
7. Netlify needs SPA fallback. The included `public/_redirects` provides it.
8. Visit the site, register, confirm email if prompted, publish a real item, and test rental requests using **two separate accounts**.
9. To make an administrator, locate your user UUID in Supabase Authentication → Users. In SQL Editor run `insert into public.admins(user_id) values ('YOUR-USER-UUID');`. Only then does `/admin` expose reports.

## Local development

Requires Node.js 20+. Run `npm install`, copy `.env.example` to `.env`, add Supabase URL and anon key, and run `npm run dev`. Run `npm run build` to check TypeScript and generate production assets.

## Important launch limitations

- Demo listings are illustrative; no real inventory or verified owners are implied.
- The project does not provide a built-in owner/renter messaging system, identity verification, escrow, automatic payment collection, dispute handling, insurance, or transactional email notifications. Contact/coordination needs a privacy-safe operational workflow before public use.
- Photo uploads use a public storage bucket; upload only photos intended to be public. Consider image moderation, virus scanning, and EXIF stripping before launch.
- Admin tools hide reported items; there is no sophisticated abuse dashboard or appeal workflow.
- Service fee is a **proposed estimate only** and is not collected. Fee logic is hardcoded at 8% in `src/main.tsx` and the SQL trigger; update both together if changing it.
- Draft Terms and Privacy require a qualified Philippine legal/privacy review and actual operator contact information.
- Additional tests, accessibility audits, and a security review are needed before production. No live Supabase credentials were available to run end-to-end authentication or moderation tests.
