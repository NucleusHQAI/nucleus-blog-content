# Blog Posting Guide

## Overview

The Nucleus AI blog uses a **two-repository architecture**:

- **This repo** (`nucleus-ai-website`) — hosts the HTML shell pages that render the blog index and individual posts.
- **Content repo** (`NucleusHQAI/nucleus-blog-content`) — holds all blog content as Markdown files plus a `posts.json` manifest.

At runtime, the browser fetches content directly from `raw.githubusercontent.com`. No build step is required.

Local checkout paths:

- Website repo: `H:\My Drive\Nucleus-AI-Website`
- Blog content repo: `H:\My Drive\nucleus-blog-content`

---

## How It Works

### Blog index (`/blog/`)

`blog/index.html` fetches `posts.json` from the content repo and renders a card grid dynamically. Each card shows the post title, date, read time, and excerpt.

### Individual posts (`/blog/post.html?post=<slug>`)

`blog/post.html` is a single dynamic template. It reads the `?post=` query parameter, then fetches two things in parallel:

1. `posts.json` — to get the post's title, date, read time, and excerpt (used for the page `<title>`, `<meta>`, and header).
2. `<slug>.md` — the Markdown file for the post body, rendered via [marked.js](https://marked.js.org/).

---

## Content Repository Structure

```
nucleus-blog-content/
├── posts.json          ← manifest: index of all posts
├── my-first-post.md    ← post content
├── another-post.md
└── ...
```

---

## `posts.json` Schema

```json
{
  "posts": [
    {
      "slug":     "my-first-post",
      "title":    "My Post Title",
      "date":     "14 May 2026",
      "readTime": "5 min read",
      "excerpt":  "One or two sentences summarising the post for the card and meta description.",
      "url":      "/blog/post.html?post=my-first-post"
    }
  ]
}
```

**Field notes:**

| Field | Required | Notes |
|-------|----------|-------|
| `slug` | Yes | Must exactly match the `.md` filename (without extension). Use lowercase kebab-case. |
| `title` | Yes | Used in the card heading, page `<title>`, and OG tags. |
| `date` | Yes | Human-readable, e.g. `"14 May 2026"`. No strict format enforced. |
| `readTime` | Yes | E.g. `"4 min read"`. Estimate manually or use ~200 words/min. |
| `excerpt` | Yes | Shown on the card and in `<meta name="description">`. Keep under 160 characters. |
| `url` | Yes | Always `/blog/post.html?post=<slug>`. |

Posts are displayed in the order they appear in the array. Put the newest post **first**.

---

## Adding a New Post

### Step 1 — Write the Markdown file

Create `<slug>.md` in the root of `nucleus-blog-content`. Plain Markdown — no frontmatter needed.

```markdown
## Why This Topic Matters

Opening paragraph here…

## Section Heading

More content…
```

Standard Markdown elements supported: headings (`##`, `###`), paragraphs, bold, italic, bullet lists, numbered lists, blockquotes, inline code, code blocks, and links. The `breaks: true` option is set, so single line breaks render as `<br>`.

### Step 2 — Update `posts.json`

Add a new entry to the **top** of the `posts` array:

```json
{
  "slug":     "your-slug-here",
  "title":    "Your Post Title",
  "date":     "14 May 2026",
  "readTime": "4 min read",
  "excerpt":  "Short summary for the card and meta description.",
  "url":      "/blog/post.html?post=your-slug-here"
}
```

### Step 3 — Commit and push to `main`

Both files must be on the `main` branch of `nucleus-blog-content`. The website fetches from `main` — no deployment of the website repo is needed.

Make sure you are in a local checkout of the **content repo**, not this website repo, before committing:

```bash
cd "H:\My Drive\nucleus-blog-content"
git remote -v
```

The remote should point to `NucleusHQAI/nucleus-blog-content`. Then commit and push the Markdown file plus `posts.json`:

```bash
git status
git add posts.json your-slug-here.md
git commit -m "Add blog post: Your Post Title"
git push origin main
```

If the content repo is missing locally, clone it separately outside the website repo:

```bash
cd "H:\My Drive"
git clone https://github.com/NucleusHQAI/nucleus-blog-content.git
```

---

## Migrated Historic Posts

The original `post-1`, `post-2`, and `post-3` articles now use the same Markdown + `posts.json` approach as new posts. Their old static HTML pages have been removed from this website repo.

---

## Checklist for a New Post

- [ ] Markdown file created in `nucleus-blog-content` with a clear kebab-case slug
- [ ] Entry added to the **top** of `posts.json`
- [ ] `slug` in `posts.json` matches the `.md` filename exactly
- [ ] `url` field is `/blog/post.html?post=<slug>`
- [ ] `excerpt` is under 160 characters
- [ ] Both files committed and pushed to `main` in `nucleus-blog-content`
- [ ] Visit `/blog/post.html?post=<slug>` to verify the post loads correctly
- [ ] Visit `/blog/` to verify the new card appears at the top of the grid
