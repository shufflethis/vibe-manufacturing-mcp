# Vibe Manufacturing MCP

A hosted, remote [MCP](https://modelcontextprotocol.io) server for finding manufacturers and getting a part made. It gives an AI agent the same loop a person follows on [vibe-manufacturing.com](https://vibe-manufacturing.com): match a part spec to contract manufacturers, estimate the landed cost, and send a quote request.

- **Endpoint:** `https://vibe-manufacturing.com/api/mcp` (Streamable HTTP, no sign-up, no API key)
- **Registry name:** `com.vibe-manufacturing/manufacturer-finder` ([official MCP Registry](https://registry.modelcontextprotocol.io))
- **Docs for agents:** https://vibe-manufacturing.com/agents
- **Open data:** https://vibe-manufacturing.com/data (one JSON file per country)

## Tools

| Tool | What it does |
| --- | --- |
| `match_manufacturers` | Ranks manufacturers for a structured part spec (process, materials, certifications, quantity, CAD formats, country). Returns a score and the reasons for each match. |
| `find_suppliers` | Lists manufacturers by country, process and city. |
| `estimate_product_cost` | Landed cost per unit and the price needed for a target margin (USD). |
| `get_playbook_step` | One of the 13 playbook steps from idea to product, with a checklist. |
| `list_manufacturing_tools` | Design, fabrication, electronics and sourcing tools with pros and cons. |
| `search_site` | Searches the playbook, guides, tool reviews and comparisons. |
| `submit_quote_request` | Sends a quote request for the human the agent acts for. Requires `user_consent: true`; a person reviews the request and replies by email. Rate limited. |

Everything except `submit_quote_request` is read-only.

## Connect

Claude Desktop, ChatGPT and other clients with remote MCP support: add a custom connector with the URL `https://vibe-manufacturing.com/api/mcp`.

Cursor or any client using an `mcp.json`:

```json
{
  "mcpServers": {
    "vibe-manufacturing": { "url": "https://vibe-manufacturing.com/api/mcp" }
  }
}
```

Clients that only speak stdio can bridge with `npx mcp-remote https://vibe-manufacturing.com/api/mcp`.

Example call:

```bash
curl -s https://vibe-manufacturing.com/api/mcp -H 'content-type: application/json' -d '{
  "jsonrpc": "2.0", "id": 1, "method": "tools/call",
  "params": { "name": "match_manufacturers",
    "arguments": { "process": "cnc-machining", "materials": ["aluminium"], "country": "DE", "quantity": 5, "file_formats": ["step"] } } }'
```

Processes: `laser-cutting`, `cnc-machining`, `sheet-metal`, `3d-printing`, `injection-molding`, `pcb-assembly`, `die-casting`, `welding`, `waterjet-cutting`, `metal-stamping`, `surface-finishing`.

## Where the data comes from

The directory is compiled automatically from OpenStreetMap contributors (ODbL), Overture Maps Foundation places data, Wikidata, and each company's own public website. Capabilities are detected from website text by keyword rules and can be incomplete or out of date, so confirm details with the manufacturer. See the [methodology](https://vibe-manufacturing.com/methodology). Manufacturers can [register, correct or remove](https://vibe-manufacturing.com/suppliers/register) their listing.

## License

This repository (documentation and registry metadata) is MIT licensed. The data is available under the terms described at https://vibe-manufacturing.com/data.
