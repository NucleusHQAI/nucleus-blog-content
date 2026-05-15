# Daily Blog Publish Task

You are running the daily blog publish task for Nucleus AI. This task runs Monday to Friday at 7:50am, surfacing today's queued blog post in `posts.json` and pushing it to `main` so it goes live.

## Step 1 — Read the post queue

Read `/home/user/nucleus-blog-content/post-queue.json`.

Find the entry in the `"queue"` array whose `"date"` matches today's date (e.g. `"19 May 2026"`).

- If no entry matches today, stop and do nothing — there is no post scheduled for today.
- If the matching entry already has `"published": true`, stop — this post has already been published.

## Step 2 — Check the post file exists

Confirm `/home/user/nucleus-blog-content/<slug>.md` exists.

If it does not exist, stop — something went wrong in the content generation step and `posts.json` must not be updated.

## Step 3 — Update posts.json

Read `/home/user/nucleus-blog-content/posts.json`.

Add a new entry at the **top** of the `"posts"` array:

```json
{
  "slug": "<slug>",
  "title": "<title>",
  "date": "<date e.g. 19 May 2026>",
  "readTime": "<readTime>",
  "excerpt": "<excerpt>",
  "url": "/blog/post.html?slug=<slug>"
}
```

Write the updated file back to `/home/user/nucleus-blog-content/posts.json`.

## Step 4 — Mark as published

Read `/home/user/nucleus-blog-content/post-queue.json` again and add `"published": true` to the matching entry. Write the file back.

## Step 5 — Commit and push to main

Stage both changed files and commit directly to `main`:

```bash
git add posts.json post-queue.json
git commit -m "Publish: <title>"
git push -u origin main
```

If the push fails, retry up to 4 times with exponential backoff (2s, 4s, 8s, 16s).

## Notes

- All files are in the cloned repo at `/home/user/nucleus-blog-content/`.
- The `.md` post files must already be committed to `main` before this task runs — content generation is handled separately.
- Do not push if Step 2 fails. Do not create a pull request. Push directly to `main`.
- If the queue is empty or today has no matching post, finish silently with no changes.
- Use British English in any log output.
