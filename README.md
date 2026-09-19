# NOLGIA for Cursor

Generate images, video, and audio on [NOLGIA](https://nolgia.ai) without leaving Cursor. This plugin adds:

- **`/nolgia`**: a command that turns a request ("a 9:16 clip of rain on a neon street", "how many credits do I have", "find the logo I made last week") into the right NOLGIA tool call, picks the model from the live catalog, states the cost before spending, and follows video jobs to the finished file.
- **The hosted NOLGIA MCP server** at `https://mcp.nolgia.ai/mcp`: generation (`nolgia_text_to_image`, `nolgia_image_to_image`, `nolgia_text_to_video`, `nolgia_image_to_video`, `nolgia_text_to_audio`), presets, your library, characters, projects, the model catalog with pricing, jobs, and your account.

## Set up your token

The server authenticates with a NOLGIA personal access token. The plugin reads it from the `NOLGIA_TOKEN` environment variable, so the token never sits in a config file.

1. Create a token at [nolgia.ai/settings/api-tokens](https://nolgia.ai/settings/api-tokens). Token requests spend your API credits.
2. Export it where Cursor will see it, for example in `~/.zshrc`:

   ```bash
   export NOLGIA_TOKEN=nol_your_token_here
   ```

3. Fully restart Cursor so it picks up the variable, then check that the `nolgia` server is enabled under Cursor Settings > MCP.

## Install

### From the Cursor Marketplace

Search for NOLGIA in the Cursor Marketplace and install it. (Listing follows this repository going public.)

### Manually

Copy the two pieces into your Cursor configuration:

```bash
git clone --depth 1 https://github.com/nolgiainc/cursor-plugin.git nolgia-cursor-plugin
mkdir -p ~/.cursor/commands
cp nolgia-cursor-plugin/commands/nolgia.md ~/.cursor/commands/nolgia.md
```

Then merge the `nolgia` entry from [`.mcp.json`](./.mcp.json) into the `mcpServers` object of `~/.cursor/mcp.json` (create the file if it does not exist):

```json
{
  "mcpServers": {
    "nolgia": {
      "url": "https://mcp.nolgia.ai/mcp",
      "headers": {
        "Authorization": "Bearer ${env:NOLGIA_TOKEN}"
      }
    }
  }
}
```

For one project only, use `.cursor/commands/` and `.cursor/mcp.json` inside that project instead.

## Use it

```
/nolgia a 5 second 9:16 clip of rain on a neon street at night
/nolgia turn my last image into a 16:9 banner
/nolgia how many credits do I have?
/nolgia what is still rendering?
```

Video renders take a few minutes; `/nolgia` keeps checking the job and hands you the finished file. Large or batch requests are priced first and wait for your go-ahead.

## Troubleshooting

| Symptom | Fix |
|---|---|
| No `nolgia_*` tools, or `401` | `NOLGIA_TOKEN` is not set in Cursor's environment, or the token was revoked. Re-export it and fully restart Cursor. |
| `402 Payment Required` | The token's API credit pool is empty. Top up at nolgia.ai. |
| `400` naming a capability | The chosen model does not accept that parameter; `/nolgia` re-reads the catalog and adjusts. |

## Related

- [nolgiainc/nolgia-skills](https://github.com/nolgiainc/nolgia-skills): the same platform as agent skills driving the `nolgia` CLI, for Claude Code, Cursor, Codex and more.
- [nolgiainc/nolgia-cli](https://github.com/nolgiainc/nolgia-cli): the `nolgia` command-line client.

## License

MIT, see [LICENSE](./LICENSE).
