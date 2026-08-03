# Publishing / listing this server

Steps for **Bartosz Kuć** to publish `skanfirmy.pl` to the MCP registry and the
main MCP directories. Everything below is done from your **private GitHub
account `bartosz-kuc`** (never a work account).

## 0. Create this repo (once)

```bash
cd ~/skanfirmy-mcp
git init && git add -A && git commit -m "skanfirmy.pl MCP server — docs + registry manifest"
gh repo create bartosz-kuc/skanfirmy-mcp --public --source=. --push \
  --description "Free MCP server to verify Polish companies (NIP/KRS/REGON) + EU VAT (VIES). 9 tools, no key."
```

Add topics after pushing (GitHub → repo → About → ⚙):
`mcp` `model-context-protocol` `poland` `nip` `regon` `krs` `vat` `vies` `ai-agents` `api`

## 1. Official MCP Registry — `io.github.bartosz-kuc/skanfirmy`

The registry currently lists this server under the **dead** namespace
`io.github.bibi-bambi/skanfirmy` (old username, renamed to `bartosz-kuc`). That
entry can't be updated anymore. Publishing under `io.github.bartosz-kuc/…` gives
the correct, verifiable attribution. `server.json` in this repo is already set up
for it.

```bash
# install the publisher CLI
brew install mcp-publisher            # or the binary: see the registry releases page

cd ~/skanfirmy-mcp
mcp-publisher login github            # OAuth as bartosz-kuc — authorises the io.github.bartosz-kuc/* namespace
mcp-publisher publish                 # validates ./server.json and uploads
```

Verify:

```bash
curl -s "https://registry.modelcontextprotocol.io/v0/servers?search=skanfirmy" | jq .
```

## 2. MCP directories (backlinks + agent discovery)

- **awesome-mcp-servers** (e.g. `punkpeye/awesome-mcp-servers`) — open a PR adding
  this line under the best-fitting category (Finance / Government data):

  ```markdown
  - [skanfirmy.pl](https://github.com/bartosz-kuc/skanfirmy-mcp) 🌐 ☁️ — Verify Polish companies by NIP/KRS/REGON and validate EU VAT (VIES) from official government registers. 9 tools, no key.
  ```

- **glama.ai** — indexes public MCP repos automatically; once this repo is public
  you can also claim/submit it at glama.ai (search for it, then "claim").
- **mcp.so** — submit at the site's "Submit" flow with the repo URL + the remote
  endpoint `https://skanfirmy.pl/mcp`.
- **Smithery (smithery.ai)** — "Add server" / connect the `bartosz-kuc/skanfirmy-mcp`
  repo; it's a remote (Streamable HTTP) server at `https://skanfirmy.pl/mcp`.

For all of them the pitch is the same: a **free, no-key, hosted** MCP server for
Polish company data, made by Bartosz Kuć.

## Notes

- The server itself is unchanged and already live at `https://skanfirmy.pl/mcp` —
  these steps only make it **discoverable** and correctly attributed.
- Keep `version` in `server.json` in step with real tool changes when you re-publish.
