---
name: crm-lead-tracking-workflow
when_to_use: When the user wants to build a lightweight CRM or lead-tracking system to manage contacts, leads, and campaigns with follow-up automation.
---

# Build a CRM and lead-tracking system

## Purpose

Build a lightweight CRM to manage leads, contacts, and campaigns.

## Steps

1. Create a database with tables for contacts, leads, and campaigns. Use the `create-sqlite-database` tool (or `execute-sql` against an existing database), and use `get-database-schema` to confirm the resulting structure.
2. Generate CRUD APIs for managing these records with the `generate-hyperlambda` tool. Pass a `filename` to each so the endpoints are saved and automatically exposed as MCP tools.
3. Add a web interface for viewing and updating leads — save HTML/CSS/JS files with the `create-file` tool (see the `create-website` guide).
4. Integrate email automation for follow-ups and reminders using the `send-email` tool, and schedule recurring reminders with the `schedule-task` / `create-task` tools.
5. Optionally connect to HubSpot or other CRM systems via their HTTP APIs using the `invoke-http` tool (or generated Hyperlambda).

## Outcome

A simple yet powerful CRM module that centralises lead management and automates follow-up communication.
