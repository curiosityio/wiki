---
name: File structure
---

Each .md file of documentation must be setup to work with peachdocs.

Pretty easy. Each file must start with a header. That header looks like:
```
---
name: Name of file here. Such as: File structure like this file is named.
---
```

That is it. Write markdown like usual.

#### To use links:

- Link to page in the same level: `[Webhook](webhook)`.
- Link to directory page: `[Introduction](../intro)`.
- Link to page in another directory: `[Getting Started](../intro/getting_started)`.

#### To use images:

By default, documentaion pages have a URL prefix `/docs`, and all your images must be put in a subdirectory of repository root directory named `images`.

Then this is how you link a image: `![](/docs/images/github_webhook.png)`.
