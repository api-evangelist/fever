---
name: Search Fever events via MCP
description: >-
  Connect an MCP client to Fever's official event-catalog server and discover
  cities and live events/experiences in real time.
api: mcp/fever-mcp.yml
operations:
- search_cities
- search_events
---

# Search Fever events via MCP

Fever ships an official Model Context Protocol server over its real-time global
event catalog. Use it from Claude Desktop, Cursor, Windsurf, or Claude Code.

## 1. Connect
Point your MCP client at `https://data-search.apigw.feverup.com/mcp/`. On first
use the client opens a browser for OAuth 2.0 login (authorization_code + PKCE,
S256); no registration form is required and it is currently free.

## 2. Find a city
Call `search_cities` to locate cities where Fever operates and obtain a city code.

## 3. Discover events
Call `search_events` with a city code (or coordinates), an optional date range,
and keywords to find events, activities and experiences. Results support sorting
and pagination and are updated at the same rate as the Fever platform.

## Rules
- Rate limits apply; for higher limits contact data-ai@feverup.com.
- This catalog surface is separate from the partner Reporting API (different host and auth).
