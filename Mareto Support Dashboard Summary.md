# Mareto Support Dashboard — Summary & Next Steps

## What We Built

We built a two-tier support ticket system for Mareto, split into two standalone dashboards:

**Client Dashboard** — the view that each client organization sees inside their own Mareto instance. Team members can submit support tickets, track their status, view conversation threads with the HelpSeeker team, leave feedback on resolved tickets, and view their organization's account info (contract details, seat usage, active modules, SLA). Clients only see their own organization's data.

**Admin Dashboard** — the internal HelpSeeker view that aggregates all tickets from every client organization into a single management console. Admins can filter tickets by organization, status, type, and priority. They get charts showing ticket distribution by type and status, can update ticket status/priority/assignment, write resolution notes, and drill into any client organization's account details including contract value, renewal dates, and seat utilization.

Both dashboards are stored in the HelpSeeker Technologies GitHub repository:

- **Repository:** https://github.com/HelpSeekerTechnologies/Mareto-Support-Dashboard
- **Client Dashboard:** https://github.com/HelpSeekerTechnologies/Mareto-Support-Dashboard/blob/main/Mareto%20Support%20Dashboard%20CLIENT.html
- **Admin Dashboard:** https://github.com/HelpSeekerTechnologies/Mareto-Support-Dashboard/blob/main/Mareto%20Support%20Dashboard%20ADMIN.html

In the current prototype, both dashboards share ticket data through the browser's local storage. Creating a ticket in the Client view immediately appears in the Admin view (and vice versa) when switching between tabs. This allows realistic end-to-end testing of the full workflow in a single browser.

## How It Will Work in Production

The end-state architecture has three layers:

1. **Client Boards (one per organization)** — Each client organization (e.g., SSTS, KFS, Transition Initiative, Sunrise) has a Client Dashboard embedded in their own Mareto instance. All team members within that organization can see and submit tickets. Tickets are scoped to their organization only.

2. **Admin Board (HelpSeeker HQ)** — A single Admin Dashboard hosted in the HelpSeeker Mareto Head Quarters instance. This is the central hub where every ticket from every client board flows in. The HelpSeeker support team manages, triages, and resolves tickets from here.

3. **Corteza Backend** — Corteza replaces browser local storage as the shared data layer. Client Dashboards write tickets to Corteza via API. The Admin Dashboard reads from the same Corteza instance, giving it a real-time aggregated view across all client organizations.

## Next Steps

### Phase 1 — Azure Staging
- Deploy both dashboards to Azure Blob Storage (`hsdeliverables.z9.web.core.windows.net/mareto-support/`)
- Test the full client-to-admin ticket flow in the Azure-hosted environment
- Validate that the local storage sync works identically on Azure

### Phase 2 — Corteza Integration
- Define the Corteza module schema for support tickets (fields: ID, subject, category, type, priority, status, organization, submitted by, assigned to, messages, resolution notes, feedback)
- Define the Corteza module schema for account/organization data (fields: org name, primary contact, project lead, seats, contract value, renewal date, support tier, active modules)
- Replace the hardcoded JavaScript data layer with Corteza REST API calls
- Implement authentication so each client board automatically scopes to its organization
- Connect the Admin Dashboard to read all organizations' tickets from Corteza

### Phase 3 — Embed in Mareto Instances
- Embed the Client Dashboard as an iframe or page within each client's Mareto instance
- Embed the Admin Dashboard within the HelpSeeker Mareto HQ instance
- Configure Corteza permissions so clients can only read/write their own tickets and admins have full cross-org access

### Phase 4 — Production Hardening
- Add email notifications on ticket creation, status changes, and replies
- Add real file attachment support (upload to Azure Blob, link in Corteza)
- Add SLA tracking and escalation rules based on support tier
- Add audit logging for compliance
