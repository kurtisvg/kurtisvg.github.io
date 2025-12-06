---
title: "How I Deploy This Site – Hugo, GitHub, and Cloudflare"
description: "A quick look at the stack behind this blog and walking through the steps that made the magic happen.."
summary: "I built this site in a weekend using Hugo, GitHub, and Cloudflare Pages. This blog walks through the steps that made the magic happen."
categories: ["Developer", "Open-Source", "Homelab"]
tags: ["cloudflare", "github", "infrastructure", "open-source"]
date: 2025-12-06
draft: false
---

This post is going to cover how I deployed this site using Hugo, GitHub, and
Cloudflare. The entire process took me 2-3 hours to explore some options and get
something working.

## Why I Launched My Own Site

It’s been on my backlog to build/host a blog for a while. My motivation for
doing so boils down to:
- Most of my job is explaining things to people. A blog is good practice for
  converting my raw ideas into something other people can actually understand.
- Internally, when I find myself describing the same concept multiple times, I
  write a google doc. However, lately I’ve been increasingly doing this outside
  of Google, and figure a post is easier to share than a Google doc. 
- I have a fairly complicated “homelab” setup. I end up ~~bragging~~ talking to
  people about it alot, and would like to do a better job of both documenting it
  and sharing that for folks that are interested. 

## Hugo: Building the Site

### Why Hugo?

I decided to go with Hugo largely because I’m familiar with it—I’ve used it to
build documentation for a few projects at work, most noticeably the
documentation site for [MCP Toolbox for
Database](https://googleapis.github.io/genai-toolbox/getting-started/introduction/).
I like that it compiles to a single binary (written in Go), which makes it fast
and incredibly easy to install.

Also, I’m terrible at UI design. Hugo has a wide community and a thriving
ecosystem of themes, which means I can outsource the visuals and focus entirely
on the content.

### Using Hugo

I followed the standard [Hugo installation
instructions](https://gohugo.io/installation/) to get the binary running
locally. Once installed, I bootstrapped a new project with:
```bash
hugo new site mywebsite
```

I like to use Hugo modules; I’m a fan of them because they are built on Go
modules and generally seem to just "work" compared to git submodules.
```bash
hugo mod init github.com/<username>/<repo-name>
```

For the theme, I went with **Blowfish**. I added it by creating a
`config/_default/module.toml`:
```toml
[[imports]]
disable = false
path = "github.com/nunocoracao/blowfish/v2"
```

From there, the workflow is standard. Add a post:
```bash
hugo new content/posts/my-new-post.md
```

Serve the site:
```bash
hugo serve
```

I spent the rest of the time tweaking the site until I was happy with it,
largely by ~~shamelessly copying~~ reverse engineering what existing users of
the theme had done.

## GitHub: Adding Source Control

I like having my projects source controlled—I'm a firm believer that
infrastructure as code is the way to go. I also like the idea of this project
being open source so folks can suggest changes and learn from it.

I created a new repo on GitHub and added my Hugo project to it. To create the
repo, I just added a README and committed the source.

I generally like GitHub (other than the fact that it seems to go down a lot).
The free tier is pretty generous, especially for things like GitHub Actions. I
am interested in experimenting with some self-hosted alternatives, but that’s a
project for another day.

I added a basic `.gitignore` to avoid committing unnecessary files:
```gitignore
# hugo files
/public/
resources/_gen
.hugo_build.lock
```

## Cloudflare: Deploying to Pages

### Why Cloudflare Pages?

I already use Cloudflare to manage my domains and I am generally a big fan of
their stack (though, spoiler alert: I was disappointed in this specific
instance).

I’ve used GitHub Pages before, and while I was generally happy with it, I wanted
to try something new. [Cloudflare Pages](https://pages.cloudflare.com/) offers a
bunch of features GitHub Pages doesn’t (like preview URLs, versioning, and
better analytics) so I was happy to have an excuse to kick the tires.

### The Configuration 

I configured the deployment with automatic deployment using the [Cloudflare
Pages Git
integration](https://developers.cloudflare.com/pages/configuration/git-integration/).
This part was painless: I walked through the UI, signed into GitHub, and gave it
access to *only* the repo it needed (principle of least privilege, baby!).

However, the build process was a different story. I tried to follow their
[documentation for deploying
Hugo](https://developers.cloudflare.com/pages/framework-guides/deploy-a-hugo-site/),
but found it was outdated and didn't reflect the latest version of the
Cloudflare site. The default build settings the site provided failed, and the
errors provided were pretty worthless.

I had to resort to trial and error to find the correct incantation. Luckily,
there weren't that many options and I figured it out pretty quick. Here are the
settings that actually worked for me:

* **Build command:** `hugo`
* **Deploy command:** `npx wrangler deploy --assets=./public
  --compatibility-date 2025-11-25`
* **Path:** `/`

Overall, I left feeling pretty disappointed in Cloudflare’s developer experience
compared to my previous interactions with their stack.

## Summary

TL;DR: This is how I currently deploy this site.

It works, but it took a bit more friction than I expected to get the build
pipeline flowing. However, the whole thing only took a couple of hours, so it’s
hard to complain too much. 

I hope you found this write-up interesting, helpful, or some combination of the
two.

