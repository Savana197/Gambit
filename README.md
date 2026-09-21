# Gambit

A chess blog/content site I built to actually learn Next.js properly instead of just watching tutorials — first real project with the App Router, so some of the choices below are "how it turned out" rather than "the one correct way to do it."

It's a small content platform for chess content: regular blog posts (called "news" in the code), a separate content type for chess openings (title, image, description), comments and likes on posts, search and pagination, and a basic admin panel for managing users and their roles.

## Stack

- **Next.js 16** (App Router) + **React 19**
- **PostgreSQL** with **Prisma** as the ORM
- **Lucia** for session-based auth, with passwords hashed by hand via `lib/hash.js` rather than pulling in NextAuth — wanted to actually understand what a session/auth flow does instead of trusting a library to hide it
- **Zod** for input validation
- **Cloudinary** for image uploads (post images, opening images)
- **Bootstrap 5** for styling, **Swiper** for the image carousels on the homepage

## Structure

Routes live under `app/(group)/` as a route group so the public/admin pages (`home`, `news`, `openings`, `users`) share one layout, while `app/api/` holds the actual backend — separate route handlers for news, openings, comments, likes, and user auth/admin actions. `lib/` has the non-UI logic: Prisma client setup, Cloudinary config, auth/session handling, and the data-access functions each API route calls into.

### Data model

Five Prisma models: `User` (with a `Role` enum — user / editor / admin), `Post`, `Opening`, `Comment`, and `Like`. Posts and openings both belong to a `User` author; comments and likes belong to both a user and a post, with cascade deletes so removing a user or a post cleans up what's attached to it instead of leaving orphaned rows.

## Running it locally

You'll need a PostgreSQL database and Node. Set up a `.env` with your Postgres connection string for Prisma, your Cloudinary credentials, and whatever secret `lib/auth.js` expects for signing sessions — there's no `.env.example` checked in yet, so open `lib/db.js` / `lib/prisma.js` / `lib/auth.js` to see the exact variable names before running this on a new machine.

```
npm install
npx prisma migrate dev
npm run dev
```

## What's missing

No tests, no CI — this was a "get something working and learn the framework" project, not built test-first like my other one. Also no rate limiting or any real abuse protection on the auth/comment endpoints, which I'd want before treating this as more than a personal project.
