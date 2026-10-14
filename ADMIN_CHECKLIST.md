# Admin checklist: AI Literacy Workshop

**Who this is for:** the workspace admin or platform owner who prepares Databricks **once** before a class or pilot.

**Who this is not for:** workshop participants. Participants only open notebooks and run Labs 0–5. Send them the repo [README.md](README.md) section **Participant path**.

**Repo:** https://github.com/archana986/ai-literacy-workshop

You can paste this whole file into email. Detail and CLI examples also live in [SETUP.md](SETUP.md).

## What you do once (admin)

1. Confirm Unity Catalog is enabled and attendees can use **Serverless** notebooks (or a UC-enabled cluster).
2. Choose a **catalog** for the lab (existing or new).
3. Grant every participant on that catalog:
   - `USE CATALOG`
   - `CREATE SCHEMA`
4. **Model access (preferred + fallback)** — tell participants which value to paste into the `llm_endpoint` widget:

   **Preferred — Unity Catalog model service (Unity Gateway)**  
   - In Catalog Explorer, open `system.ai` and pick a chat model service (name looks like `system.ai.<model-name>`).  
   - Participants need `EXECUTE` on that model service, plus `USE CATALOG` on `system` and `USE SCHEMA` on `system.ai` (often already true for account users).  
   - They paste the **full FQN** into `llm_endpoint`.

   **Fallback — legacy Foundation Model serving endpoint**  
   - Use this if Unity Gateway is not enabled, is still a preview in the account, or preflight fails on a `system.ai…` name.  
   - Workshop default endpoint name: `databricks-meta-llama-3-3-70b-instruct`.  
   - Grant every participant **`CAN QUERY`** on that endpoint.  
   - They paste the **endpoint name** (not a UC path) into `llm_endpoint`.

   **What to say to the room:**  
   “Paste into `llm_endpoint` either the `system.ai…` name from Catalog Explorer **or** the legacy endpoint name I give you. If your preflight says Gateway unavailable, use the legacy name.”

5. Confirm a **SQL warehouse** exists for optional Genie. Grant every participant `CAN USE` on that warehouse.
6. (Presenter / PII demo only) Create an **account group** for unmasked names (example name: `lab_pii_viewers`). Add only people who should see full names in the demo. Attendees are usually **not** in this group.
7. Put the lab notebooks in the workspace where people will run them:
   - Import everything under `src/notebooks/` into one folder (example: `/Workspace/Users/<you>/AI-Literacy-Workshop` or a shared Users/Shared path).
   - Import PNGs from `src/notebooks/assets/` into `<that-folder>/assets/` as workspace **FILE** objects (not notebooks).
8. Smoke-test once yourself: open notebook **`admin_setup`**, set widgets (including the model value from step 4), Run All. Confirm diagram assets are FILE and preflight PASS. Optional: Run All on Lab 1 once. (PII group may WARN if you are not in the group; that is OK.)
9. **Before class (UI dry-run):** Click through AI Playground tool attach and open a Genie space once. UI labels change; if “Hosted function” or the Genie URL shape differs, note the current clicks for the room.

You do **not** create each attendee’s schema, seed data, or agent. Each user creates **their own schema** when they run Lab 1.

## What participants do (after you finish)

1. Open the shared folder (or their copy of the notebooks).
2. Open **`00_start_here`** (not the admin setup notebook), set widgets (catalog, their schema or blank for auto, LLM model, warehouse).
3. Run All through Labs 1–3. Optional: Lab 4 (Genie). Then Lab 5 (path from lab to a real agent).

Full participant steps: [README.md](README.md) → **Participant path**.

## Checklist you can tick before class

| Done | Item |
|---|---|
| | Catalog ready; participants have `USE CATALOG` + `CREATE SCHEMA` |
| | Model access ready: UC `EXECUTE` on `system.ai…` **and/or** legacy endpoint `CAN QUERY` |
| | You told participants which string to paste into `llm_endpoint` (FQN or legacy name) |
| | SQL warehouse ready; participants have `CAN USE` (Genie optional) |
| | PII account group exists (presenter demo only) |
| | Notebooks imported under one workspace folder |
| | `assets/*.png` imported as **FILE** next to notebooks |
| | You ran **`admin_setup`** successfully once (diagrams FILE + preflight PASS) |
| | Optional: Playground + Genie click-path verified in this workspace |

## Out of scope for admin

- Building or fixing the agent for each user
- Running MLflow evaluation for the room
- Deploying Apps / Agent Bricks / the optional Databricks Asset Bundle (classroom uses notebook Run All)
- Turning on **Enforce Unity Gateway** account-wide (ops decision; lab supports both modes)

## If something fails

| Symptom | Likely fix |
|---|---|
| Preflight fails on catalog/schema | Missing `USE CATALOG` or `CREATE SCHEMA` |
| Preflight fails on `system.ai…` / Gateway | Gateway not enabled or preview off → switch widget to **legacy endpoint** + `CAN QUERY` |
| Preflight fails on legacy endpoint | Wrong endpoint name or missing `CAN QUERY` |
| Diagrams do not show | PNGs imported as notebooks; re-import as FILE into `assets/` |
| Mask/filter demo odd for presenter | Presenter not in (or incorrectly in) the PII group |
| Tool-loop fails on one model | Switch `llm_endpoint` to another chat model (default Llama often works); any chat model with working tools is fine |

## Links for you

- Participant overview: [README.md](README.md)
- Install detail (CLI, widgets, limits): [SETUP.md](SETUP.md)
- Unity Catalog: https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/
- Unity Gateway migration: https://docs.databricks.com/aws/en/ai-gateway/migrate-to-unity-gateway
- Notebook widgets: https://learn.microsoft.com/en-us/azure/databricks/notebooks/widgets
- Images in notebooks: https://learn.microsoft.com/en-us/azure/databricks/notebooks/notebook-media
- AI Playground: https://learn.microsoft.com/en-us/azure/databricks/large-language-models/ai-playground
