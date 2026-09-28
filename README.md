# skanfirmy.pl — MCP server

A free, stateless **MCP (Model Context Protocol) server** that lets AI agents
verify **Polish companies** and validate EU VAT numbers directly from official
government registers. No key and no registration for the lookups; per-IP rate
limits apply (see [Limits and personal data](#limits-and-personal-data)).

- **Endpoint:** `https://skanfirmy.pl/mcp` (JSON-RPC 2.0 over Streamable HTTP)
- **14 tools**: 9 lookups and calculators combining the VAT Register (Ministry of
  Finance), KRS (Ministry of Justice), REGON (Statistics Poland / GUS) and VIES
  (European Commission), plus 5 tools for the optional change monitoring, which
  need an API key.
- Also usable as plain REST for crawlers/agents that can't speak MCP.

Made by **[Bartosz Kuć](https://skanfirmy.pl)** — the same person behind
[skanfirmy.pl](https://skanfirmy.pl) and the
[otwarteAPI.pl](https://otwarteapi.pl) catalog of Polish & EU public APIs.

## Tools

| Tool | What it does |
|---|---|
| `sprawdz_nip` | VAT status (Biała Lista) + KRS data for a company by NIP |
| `sprawdz_lista_nip` | Bulk lookup of up to 30 NIPs in one call (MF VAT Register) |
| `sprawdz_regon` | REGON registry data (GUS) by NIP; for sole traders and other natural persons only name, legal form, entity type, town and activity status |
| `sprawdz_vies` | Validate an EU VAT number via VIES (European Commission) |
| `sprawdz_rachunek` | Is a bank account on the VAT White List for a given NIP |
| `generuj_mikrorachunek` | Individual tax micro-account (PIT/CIT/VAT) from a NIP; the formula for a PESEL is in the tool description, to compute locally |
| `szukaj_pkd` | Search the PKD 2025 business-activity classification |
| `oblicz_odsetki` | Statutory / commercial (B2B) late-payment interest calculator |
| `szukaj_katalog_api` | Search [otwarteAPI.pl](https://otwarteapi.pl) — a catalog of other public PL/EU APIs |

Monitoring tools (need a skanfirmy API key, which you get after confirming a
sign-up at [skanfirmy.pl/monitoring](https://skanfirmy.pl/monitoring)):
`observe_nip`, `unobserve_nip`, `list_observations`, `changes_since`,
`set_webhook`.

## Limits and personal data

- **Rate limits per IP address.** MCP: at most 60 requests per 10 seconds, then
  HTTP 429 with a JSON-RPC error (`error.data.contact`) for 10 seconds. REST
  endpoints: at most 20 requests per 10 seconds, then HTTP 429 for 10 seconds.
  The service is meant for single lookups started by users or their agents,
  not for bulk data harvesting.
- **Source limits.** The Ministry of Finance White List API has its own daily
  limits (search: 100 queries a day, up to 30 NIPs each), shared by all users
  of the service. When they run out, lookups answer HTTP 503 until the next day.
- **Sole traders and other natural persons** (entities without a KRS number, and
  civil partnerships) get minimised data (GDPR): name, NIP, VAT status, town
  and the *number* of White List accounts. No REGON number, address,
  registration date, PKD codes or list of account numbers. To check one
  specific account, use `sprawdz_rachunek` (REST: `POST /rachunek`).
- **Numbers restricted from presentation.** Data for some numbers is not
  presented (a data-protection procedure). The answer is then `restricted: true`
  with a fixed sentence and a link to the Ministry of Finance search, with no
  data and no fields about validity, VAT status or presence in the register.
  Send the user to the official search.

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
- `POST https://skanfirmy.pl/rachunek` — is one bank account on the White List for a NIP

Add `?format=json` for JSON. Full docs: [skanfirmy.pl/llms.txt](https://skanfirmy.pl/llms.txt),
OpenAPI: [skanfirmy.pl/openapi.yaml](https://skanfirmy.pl/openapi.yaml).

## Claude Agent Skill

Prefer a drop-in [Agent Skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills)?
[`skill/`](skill/) holds **`verify-polish-company`** — a single `SKILL.md` that
teaches Claude Code, the Claude apps or the Agent SDK when and how to call the
endpoints above (no key, nothing to run beyond ordinary HTTP):

```bash
cp -R skill ~/.claude/skills/verify-polish-company
```

See [`skill/README.md`](skill/README.md) for details.

## Data sources

VAT Register / Biała Lista (Ministry of Finance), KRS (Ministry of Justice),
REGON / BIR (Statistics Poland — GUS), VIES (European Commission). Independent
project — not affiliated with any of them. What the service stores and for how
long is described in its [privacy policy](https://skanfirmy.pl/privacy).

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
