# skanfirmy.pl — MCP server

A free, stateless **MCP (Model Context Protocol) server** that lets AI agents
verify **Polish companies** and validate EU VAT numbers directly from official
government registers — **no key, no registration, no limits** beyond the source
registries' own.

- **Endpoint:** `https://skanfirmy.pl/mcp` (JSON-RPC 2.0 over Streamable HTTP)
- **9 tools** combining the VAT Register (Ministry of Finance), KRS (Ministry of
  Justice), REGON (Statistics Poland / GUS) and VIES (European Commission).
- Also usable as plain REST for crawlers/agents that can't speak MCP.

Made by **[Bartosz Kuć](https://skanfirmy.pl)** — the same person behind
[skanfirmy.pl](https://skanfirmy.pl) and the
[otwarteAPI.pl](https://otwarteapi.pl) catalog of Polish & EU public APIs.

## Tools

| Tool | What it does |
|---|---|
| `sprawdz_nip` | VAT status (Biała Lista) + KRS data for a company by NIP |
| `sprawdz_lista_nip` | Bulk lookup of up to 30 NIPs in one call (MF VAT Register) |
| `sprawdz_regon` | REGON registry data (GUS) by NIP — incl. sole traders not in KRS |
| `sprawdz_vies` | Validate an EU VAT number via VIES (European Commission) |
| `sprawdz_rachunek` | Is a bank account on the VAT White List for a given NIP |
| `generuj_mikrorachunek` | Individual tax micro-account (PIT/CIT/VAT) from NIP/PESEL |
| `szukaj_pkd` | Search the PKD 2025 business-activity classification |
| `oblicz_odsetki` | Statutory / commercial (B2B) late-payment interest calculator |
| `szukaj_katalog_api` | Search [otwarteAPI.pl](https://otwarteapi.pl) — a catalog of other public PL/EU APIs |

## Connect

**Any MCP client that supports remote (Streamable HTTP) servers** — point it at:

```
https://skanfirmy.pl/mcp
```

**Claude Desktop / stdio-only clients** — bridge with `mcp-remote`:

```json
{
  "mcpServers": {
    "skanfirmy": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://skanfirmy.pl/mcp"]
    }
  }
}
```

**Raw JSON-RPC** (no MCP client needed):

```bash
# list tools
curl -s https://skanfirmy.pl/mcp \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'

# check a company by NIP
curl -s https://skanfirmy.pl/mcp \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"sprawdz_nip","arguments":{"nip":"5261040828"}}}'
```

## Plain REST (no MCP)

For agents/crawlers that just want a URL:

- `GET https://skanfirmy.pl/nip/{nip}` — VAT + KRS
- `GET https://skanfirmy.pl/nips/{comma,separated,nips}` — bulk (≤30)
- `GET https://skanfirmy.pl/regon/{nip}` — REGON registry data
- `GET https://skanfirmy.pl/vies/{country}/{number}` — EU VAT (VIES)

Add `?format=json` for JSON. Full docs: [skanfirmy.pl/llms.txt](https://skanfirmy.pl/llms.txt).

## Data sources

VAT Register / Biała Lista (Ministry of Finance), KRS (Ministry of Justice),
REGON / BIR (Statistics Poland — GUS), VIES (European Commission). Independent
project — not affiliated with any of them. Nothing is persisted; responses are
transiently CDN-cached (max 24h), `POST /mcp` isn't cached at all.

## Author

**Bartosz Kuć**
· [skanfirmy.pl](https://skanfirmy.pl)
· [github.com/bartosz-kuc](https://github.com/bartosz-kuc)
· [linkedin.com/in/bartosz-kuc](https://pl.linkedin.com/in/bartosz-kuc)
· [cal.com/bartosz-kuc](https://cal.com/bartosz-kuc)

## License

MIT © [Bartosz Kuć](https://skanfirmy.pl). This repository documents the hosted
server and holds its MCP registry manifest; the server itself runs at
`https://skanfirmy.pl/mcp`.
