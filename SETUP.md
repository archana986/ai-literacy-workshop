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

Pick one. Both bring the notebooks **and** the `assets/*.png` diagrams as workspace **FILE** objects:

- **Git folder (recommended):** Workspace > Create > Git folder > paste the repo URL. Notebooks land in `src/notebooks/`.
- **Zip:** Workspace > right-click a folder > Import > choose the workshop zip.
- **CLI:** `databricks workspace import-dir src/notebooks /Workspace/Users/<you>/AI-Literacy-Workshop --profile YOUR_PROFILE --overwrite`

Notebooks load lab diagrams with `show_diagram(...)` from `_config`, which resolves `/Workspace/.../assets/` next to the notebook. If diagrams do not show, check that `assets/` holds 7 PNGs as FILE (not notebooks).

## Participant: widgets

Each lab notebook creates its own **widget bar** in its first code cell (**Step 0**). Run that cell, set the values in the bar at the top, then Run All. `_config` only reads the values (a `%run` notebook cannot add widgets to its caller) and saves them to `/Workspace/Users/<you>/ai_literacy_lab_settings.json`, so later labs open with the same values. Change any widget, then re-run from the config cell.

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
