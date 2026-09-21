# FlyBy Aviation — Daily WordPress Auto-Publisher

Publishes one queued blog post per day to flywithflyby.com via the WordPress
REST API, running on GitHub Actions (free tier) — no local computer or
Jetpack upgrade required.

## One-time setup (10 minutes)

1. **Create a new GitHub repository** (private is fine) — e.g. `flyby-blog-publisher`.
2. **Upload this entire folder** to the repo, keeping the folder structure
   intact (drag-and-drop the whole folder on the repo's "Add file → Upload
   files" page, or use `git push` if you're comfortable with git).
3. **Add three repository secrets**: go to the repo's
   **Settings → Secrets and variables → Actions → New repository secret**,
   and add:
   - `WP_SITE_URL` → `https://flywithflyby.com`
   - `WP_USERNAME` → your WordPress login username (e.g. `flywithflyby@gmail.com`)
   - `WP_APP_PASSWORD` → the Application Password generated from your WordPress
     profile page (Users → Profile → Application Passwords). **Do not commit
     this anywhere in the repo itself** — secrets are the safe place for it.
4. That's it. The workflow at `.github/workflows/daily-post.yml` runs every
   day at 04:00 UTC (09:30 IST) and publishes the next post from
   `posts_queue/`.

## Running it manually / testing

Go to the repo's **Actions** tab → **Daily WordPress Post** → **Run workflow**
to trigger a run immediately instead of waiting for the schedule. Check the
run logs for the published post's URL.

## Adding more posts to the queue

Each post is a single `.md` file in `posts_queue/` with a frontmatter block:

```
---
title: "Post title"
slug: "post-url-slug"
meta_description: "One or two sentence SEO meta description"
categories: ["Category Name"]
tags: ["tag1", "tag2"]
---
<p>HTML body content goes here.</p>
```

Files are published in alphabetical order, so name new files with a
numeric prefix continuing from the last one (e.g. `008-...md`,
`009-...md`) to control the sequence. Once a post is published, the
workflow moves its file into `posts_queue/published/` and commits that
change, so the queue always reflects what's left.

**The queue currently holds 7 posts** — enough for one week of daily
publishing. Ask Claude to generate more batches periodically (ideally
before the queue runs dry) to keep the daily cadence going, following the
same topic rotation: Pilot Training (CPL/ATPL), Foreign License Conversion,
and General Aviation Career Guidance.

## Changing the schedule

Edit the `cron` line in `.github/workflows/daily-post.yml`. Cron times are
in UTC — 04:00 UTC = 09:30 IST.

## If the queue runs empty

The workflow logs "Queue is empty" and exits cleanly without failing — it
just won't publish anything until new posts are added.
