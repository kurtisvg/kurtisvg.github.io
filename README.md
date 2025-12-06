# kvg.dev

This is the source code my personal site (kvg.dev).

## Build

This site is generated using [Hugo](https://gohugo.io/) and the [Blowfish](https://blowfish.page/) theme. 

To build locally, [install hugo](https://gohugo.io/installation/) and serve with:

```sh
hugo serve
```

## Deploy

This site is currently deployed to [Cloudflare Pages](https://pages.cloudflare.com/).

It's configured for automatic deploying using the [Cloudflare Pages Git integration](https://developers.cloudflare.com/pages/configuration/git-integration/).

It uses the following Build configuration:

- Build command: `hugo`
- Deploy command: `npx wrangler deploy --assets=./public --compatibility-date 2025-11-25`
- Path: `/`

The status of the build is atttached to each commit. 
