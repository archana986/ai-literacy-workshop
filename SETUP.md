# Setup (detail)

**This file is the detailed install guide.** Start from role:

| Role | Start here |
|---|---|
| **Admin** (one-time grants + import) | [ADMIN_CHECKLIST.md](ADMIN_CHECKLIST.md), then the **Admin** sections below |
| **Participant** (run the labs) | [README.md](README.md) → Participant path, then the **Participant** sections below |

## Admin: prerequisites to grant

Do these once in the workspace before class.

- Unity Catalog and serverless notebooks (or a UC-enabled cluster)
- On the lab catalog, for every participant: `USE CATALOG` and `CREATE SCHEMA`
- **Model access (either or both):**
  - Preferred: `EXECUTE` on a `system.ai.<model>` model service (+ `USE CATALOG` / `USE SCHEMA` on `system` / `system.ai`)
  - Fallback: `CAN QUERY` on a legacy chat serving endpoint (default name `databricks-meta-llama-3-3-70b-instruct`)
- On a SQL warehouse (Genie optional), for every participant: `CAN USE`
- An account group for the PII viewer demo (presenter only; attendees usually are not members). Default widget name: `lab_pii_viewers`

Admins do **not** create each user’s schema or seed data. Lab 1 does that per participant.

Tell participants which string to paste into `llm_endpoint`: a `system.ai…` FQN **or** the legacy endpoint name. If Unity Gateway is not enabled, use the legacy name.

## Admin: setup notebook

After import, open **`admin_setup`** and Run All before class. Participants start at `00_start_here`.

## Admin: import into the workspace

1. Create a workspace folder (for example `/Workspace/Users/<you>/AI-Literacy-Workshop` or a shared path).
2. Import every notebook under `src/notebooks/` (includes `admin_setup` plus Labs 00–05).
3. Import PNGs under `src/notebooks/assets/` as workspace **FILE** objects into `<your-folder>/assets/` (same parent as the notebooks).

Example with Databricks CLI (notebooks):

```bash
databricks workspace import-dir src/notebooks /Workspace/Users/<you>/AI-Literacy-Workshop \
  --profile YOUR_PROFILE --overwrite
```

If PNGs were imported as notebooks by mistake, delete them and re-import so `object_type` is `FILE` under `.../assets/`.

Notebooks load lab diagrams with `show_diagram(...)` from `_config`, which resolves `/Workspace/.../assets/` next to the notebook.

Docs: [Images in notebooks](https://learn.microsoft.com/en-us/azure/databricks/notebooks/notebook-media)

## Participant: widgets

Set these on `00_start_here` after `%run ./_config` (change anytime, then re-run from config):

| Widget | Purpose |
|---|---|
| `catalog` | Unity Catalog for the lab (admin chose this) |
| `schema` | **Your** schema (blank = auto from login) |
| `llm_endpoint` | **LLM model (UC FQN or legacy endpoint)** — paste `system.ai…` **or** a legacy endpoint name |
| `warehouse_name` | SQL warehouse for Genie |
| `demo_filter_state` | State hidden by the row filter |
| `pii_group` | Group that sees unmasked names (leave as admin configured) |

If preflight says Unity Gateway / model service is unavailable, paste the **legacy endpoint name your admin gave you** and re-run preflight. That fallback is normal when Gateway is not enabled.

Docs: [Notebook widgets](https://learn.microsoft.com/en-us/azure/databricks/notebooks/widgets) · [Unity Gateway migration](https://docs.databricks.com/aws/en/ai-gateway/migrate-to-unity-gateway)

## Participant: run order

1. `00_start_here`
2. `01_setup_governed_data`
3. `02_build_agent`
4. `03_evaluate_and_fix`
5. Optional `04_genie_fallback`
6. `05_from_lab_to_real_agent` (wrap: what `_agent_core` is, path to a real agent)
7. Ideation canvas (also in Lab 4): [`docs/diagrams/ideation_canvas.png`](docs/diagrams/ideation_canvas.png)

## Bundle exhibit (optional; not required for class)

Usually an **admin or facilitator** demo only. Room demo is notebook Run All. Bundle default catalog is `main` (portable); set variables for your workspace when validating.

```bash
databricks bundle validate --strict --target dev --profile YOUR_PROFILE
# optional: databricks bundle deploy --target dev --profile YOUR_PROFILE
```

## Known limitations

- Building Apps / Agent Bricks / Enforce Unity Gateway in class is out of scope; Lab 5 explains the path
- Lab supports **both** Unity Gateway (`system.ai…`) and legacy serving endpoints; default widget uses legacy so unknown workspaces still run
- Row filter / mask group behavior needs the account group; functions are still created without it
- Audit system tables may be presenter-only
- If the tool loop fails on one chat model, switch `llm_endpoint` to another (default Llama is a safe starting point). Claude tool turns are handled in `_agent_core`.
- Playground and Genie UI labels/URLs can change; admins should click through once before class
