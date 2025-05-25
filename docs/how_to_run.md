# How to Run TechFeed

This project turns Markdown files in the `content/` directory into a simple blog site.
Follow the steps below to run it locally.

## 1. Clone and Install Dependencies

```bash
git clone <repository-url>
cd techfeed
composer install
```

The dependencies include Parsedown which converts Markdown into HTML.

## 2. Start the Local Server

Run PHP's built-in web server from the project root:

```bash
php -S localhost:8000
```

Visit `http://localhost:8000` in your browser. The first request will generate `data/articles.json` which caches your article metadata.

## 3. Add an Article

Create a new Markdown file inside `content/` with a filename like `2025-05-25-my-post.md` and include YAML front matter:

```markdown
---
title: "My First Post"
date: "2025-05-25"
tags: ["php", "markdown"]
summary: "An example post."
---

Write your article here.
```

## 4. Refreshing the Cache

If you add or update articles, you can regenerate the JSON cache by visiting:

```
http://localhost:8000/?refresh_cache=1
```

After refreshing, reload the page to see the updates.

Happy coding!
