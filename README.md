# Facebook Scheduler for Claude: Content Mill

Schedule and publish Facebook Page posts from chat: write posts, add photos or video, preview each one, and track what's scheduled or published. Facebook scheduler by Content Mill.

A Claude plugin that connects Claude to **Content Mill for Facebook**, a hosted MCP server at `https://fb-mcp.contentmill.dev/mcp`. Claude drafts the post, checks it against Facebook's limits, shows you a preview, and publishes or schedules it only after you approve that preview. It also lists what is scheduled, published or failed.

This server works with your Facebook accounts only. It cannot read or post to any other network. Content Mill has the same plugin for Instagram, X, LinkedIn, Facebook, Threads and Bluesky.

## Requirements

- A Content Mill account at [contentmill.dev](https://contentmill.dev).
- Connect a Facebook Page (posts go to Pages only; personal profiles can't be posted to). You choose the Page during sign-in. Accounts are connected in the Content Mill web app, not in Claude.
- Claude Code, Cowork or any Claude client that supports plugins and remote MCP servers. There is nothing to install or run locally.

## Install

```
/plugin marketplace add content-mill/claude-plugin-facebook
/plugin install content-mill-facebook@content-mill-facebook
/reload-plugins
```

Then run `/mcp`, pick **content-mill**, and sign in with your Content Mill email and password. That sign-in is the only credential involved; Claude never sees your Facebook login.

To use the MCP server without the plugin (no skills), add it directly:

```
claude mcp add --transport http content-mill-facebook https://fb-mcp.contentmill.dev/mcp
```

## What you can do

- “Write a Facebook post announcing our weekend sale and save it as a draft.”
- “Add these two photos to the post, show me a preview, then schedule it for Friday at 10am.”
- “Which Facebook posts are scheduled for this week, and did any fail to publish?”

Skills (each is also a slash command):

| Skill | What it does |
|---|---|
| `/content-mill-facebook:draft-post` | Write a Facebook post and save it as a draft. Never publishes. |
| `/content-mill-facebook:publish-post` | Preview, then publish now or schedule, after you approve the preview. |
| `/content-mill-facebook:review-posts` | See what is scheduled, published, failed or still a draft; cancel a scheduled post. |

## Facebook limits

Claude reads these from the server before writing, so a post is valid before it is sent:

- Pages only; a personal profile can't be posted to.
- Text-only posts are allowed.
- Up to 10 images, or one video (never mixed); each image 100 to 4,096 pixels per side.
- Video 1 second to 20 minutes, up to 1 GB.

## Tools

`list_social_accounts`, `get_platform_limits`, `create_draft`, `update_draft`, `delete_post`, `preview_post`, `publish_post`, `list_posts`, `get_post`, `upload_media_from_url`, `create_media_upload`, `finish_media_upload`.

Reads are marked read-only. `update_draft`, `delete_post` and `publish_post` are marked destructive, and only `publish_post` and `upload_media_from_url` reach outside Content Mill.

## How publishing stays safe

- Publishing is always two calls. `preview_post` stores the exact post for 10 minutes; `publish_post` redeems that preview once and publishes exactly that, re-checking it first.
- Assistants can only edit drafts. A scheduled post is removed and recreated through a new preview.
- Every tool call is recorded against your account.
- `confirmed: true` is supplied by the assistant, so the server cannot itself prove a person said yes. The plugin's skills tell Claude to show you the preview and wait.

## Privacy and support

See [PRIVACY.md](./PRIVACY.md) and [contentmill.dev/privacy](https://contentmill.dev/privacy). Questions or problems: [support@contentmill.dev](mailto:support@contentmill.dev).

## License

[MIT](./LICENSE)
