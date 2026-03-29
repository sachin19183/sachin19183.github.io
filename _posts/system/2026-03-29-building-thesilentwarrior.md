---
layout: single
title: How I Built thesilentwarrior.org — From Notes to a Structured System
date: 2026-03-29
categories: [SYSTEM]
tags: [jekyll, github-pages, architecture, learning]
---

# How I Built *thesilentwarrior.org* — A Journey From “Just Notes” to a Structured System

Before I get into the technical journey, I want to acknowledge something important.

I started this with **zero experience in building a website**.

This entire project was built with the help of ChatGPT—through long, back-and-forth conversations, trial and error, debugging sessions, and a lot of questions. What you’re reading here is not something I built in isolation.

It was a collaborative process:

> I explored, experimented, and broke things—and ChatGPT helped me reason through them until things made sense.

This blog is as much about that process as it is about the final system.

---

I didn’t design *thesilentwarrior.org*.

I arrived at it.

What started as a simple idea—*“let me put my notes online”*—slowly turned into a system. Not because I planned it that way, but because things kept **not working the way I expected**.

And each time something broke, I had to understand *why*.

---

## Phase 1: “Just Put Everything in `_posts/`”

My first instinct was simple:

> Jekyll has a `_posts/` folder → so I’ll put everything there.

So I did:

```id="8mgjka"
_posts/
  2025-12-05-Docker-Intro.md
  2025-12-07-Docker-notes.md
```

And inside each post:

```yaml id="p3ht1z"
categories: [DOCKER]
```

This worked… partially.

* Posts showed up
* Pages rendered
* Everything looked fine

Until I tried to organize it.

---

## Phase 2: “I Want a Docker Page”

I didn’t just want posts.

I wanted structure:

* A **Docker page**
* A **GCP page**
* A **ML page**

So I created a `categories.md` and added links like:

```markdown id="46vp0x"
[Open DOCKER Index](/DOCKER/)
```

This is where things broke.

➡️ Clicking the link → **404**

---

## Phase 3: The First Realization — URLs Are Not Abstract

I assumed `/DOCKER/` was just a label.

It isn’t.

Jekyll maps URLs directly to files.

So this:

```id="ggsw9g"
/DOCKER/
```

Only works if this exists:

```id="3bmt0i"
DOCKER/index.md
```

That was my first structural shift.

---

## Phase 4: Creating Category Pages (First Fix)

So I created:

```id="a301px"
DOCKER/
  index.md
```

And suddenly:

* `/DOCKER/` worked
* Navigation worked

Problem solved?

Not really.

---

## Phase 5: “Why Is My Docker Page Empty?”

Now I had a working page… with no content.

I expected Jekyll to automatically show Docker posts there.

It didn’t.

That’s when I added this:

```liquid id="vsqnjx"
{% assign posts = site.categories.DOCKER | sort: "date" %}
```

And looped through posts.

Now the posts appeared.

But this raised a deeper question:

> Where is `site.categories.DOCKER` even coming from?

---

## Phase 6: The Second Realization — Categories Are Not Folder-Based

At this point, I tried organizing posts like this:

```id="te85t6"
_posts/docker/
  2025-12-05-Docker-Intro.md
```

Logically, that should define the category.

But it didn’t.

The page was still empty.

That’s when I understood:

> Jekyll does **not** infer categories from folder structure.

This line is what actually matters:

```yaml id="4q4yap"
categories: [DOCKER]
```

Without it:

* Your posts exist
* But your category page sees nothing

---

## Phase 7: The System Finally Emerges

At this point, everything started making sense.

I ended up with a **3-layer system**:

### 1. Physical Organization (for me)

```id="4fyz5b"
_posts/docker/
_posts/gcp/
```

### 2. Logical Categorization (for Jekyll)

```yaml id="8ql2u1"
categories: [DOCKER]
```

### 3. Custom Rendering (for the site)

```liquid id="1hrn68"
site.categories.DOCKER
```

And category pages like:

```id="h6bfsg"
DOCKER/index.md
```

---

## 🧠 Visualizing the Architecture

At this point, I needed a mental model to understand what I had actually built.

So I represented it like this:

![Silent Warrior Architecture](/images/silent-warrior-architecture.png)

> A 3-layer system: physical folders → frontmatter categories → custom rendered pages.

---

## Phase 8: Making the Site Feel Like “Mine”

Once the structure stabilized, I moved to customization.

Inside `_config.yml`, I added:

* Site identity:

```yaml id="qdp4cc"
title: "Silent Warrior Tech Notes"
name: "Sachin"
```

* Domain:

```yaml id="1q48ng"
url: "https://thesilentwarrior.org"
```

* Theme:

```yaml id="wt4y6p"
remote_theme: "mmistakes/minimal-mistakes"
```

Then the personal touches:

### Avatar

```yaml id="zawh7z"
avatar: "/assets/images/silentWarriorDP.png"
```

### Bio

```yaml id="0l622z"
bio: "A knowledge seeker fighting the lazy mind..."
```

### Social Links

* LinkedIn
* X (Twitter)
* Codeberg

This was the moment it stopped being “a site” and became **my site**.

---

## Phase 9: Homepage Control

I didn’t want a generic homepage.

So I added:

```id="h05s3t"
_layouts/home.html
```

And controlled:

* how posts are selected
* how many are shown
* how they are rendered

Now the site wasn’t just static content.

It had **logic**.

---

## What This Journey Actually Taught Me

### 1. Structure Comes Before Features

### 2. Jekyll Is Simple—but Not Obvious

### 3. Organization Has Layers

### 4. Most Problems Are Silent

---

## What *thesilentwarrior.org* Is Now

It’s not just a blog.

It’s a **structured knowledge system**:

* Docker → organized notes
* GCP → categorized learnings
* ML / LLM → evolving topics
* SYSTEM → how everything is built

---

## Final Thought

I didn’t build this in one go.

I built it by:

* trying something
* seeing it fail
* understanding why
* fixing the structure

Over and over again.

And somewhere along that process, this became more than a website.

It became a system that actually reflects how I learn.

---

> If something doesn’t work in Jekyll, it’s usually not broken—you just haven’t matched the structure yet.
