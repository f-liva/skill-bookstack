---
name: bookstack
description: "BookStack Wiki & Documentation API integration. Manage knowledge base programmatically: CRUD operations on books, chapters, pages, and shelves with full-text search. Use when you need to: (1) Create or edit wiki pages and documentation, (2) Organize content in books and chapters, (3) Search the knowledge base, (4) Automate documentation workflows, (5) Sync content between systems. Supports both HTML and Markdown content. Handles Cloudflare-protected instances."
---

# BookStack API Skill

Manage a BookStack wiki via its REST API using the bundled Python script. No external dependencies required.

## Configuration

Credentials are stored in `~/.clawdbot/clawdbot.json`:

```json
{
  "skills": {
    "entries": {
      "bookstack": {
        "env": {
          "BOOKSTACK_URL": "https://your-bookstack.example.com",
          "BOOKSTACK_TOKEN_ID": "your-token-id",
          "BOOKSTACK_TOKEN_SECRET": "your-token-secret"
        }
      }
    }
  }
}
```

To create a token: BookStack → Profile → API Tokens → Create Token. The user's role must have **"Access System API"** permission.

## Usage

Run the script with env vars from the config. All commands follow:

```
python3 scripts/bookstack.py <command> [args] [options]
```

### Quick Reference

| Action | Command |
|--------|---------|
| Search | `search "query" [--type page\|book\|chapter]` |
| List pages | `list_pages [--count N]` |
| Read page | `get_page <id> [--content\|--markdown]` |
| Create page | `create_page --book-id <id> --name "Title" --html "<p>content</p>"` |
| Update page | `update_page <id> --html "<p>new content</p>"` |
| Delete page | `delete_page <id>` |

Same pattern applies to `books`, `chapters`, and `shelves`. See [references/api-commands.md](references/api-commands.md) for the full command reference.

## Important Notes

- **Cloudflare**: The script includes a `User-Agent` header. Instances behind Cloudflare block requests without it (HTTP 403).
- **Content format**: Pages use HTML by default. Use `--markdown` flag for Markdown content. When reading, `get_page --content` returns HTML, `--markdown` returns Markdown.
- **Large updates**: For updating page HTML, prepare the content in a temp file and use Python to read it into the `--html` argument, or use the API directly via the script's `api_call()` function.
- **Context-mode sandbox**: If `curl` is blocked by context-mode hooks, use `mcp__plugin_context-mode_context-mode__execute` with shell language to run curl commands for API calls. The sandbox can reach external URLs that Python urllib from the same sandbox may not (Cloudflare behavior).
