# Claude project context — Vexoryna AI

Follow the shared instructions in [AGENTS.md](AGENTS.md). They are the
repository-wide engineering rules for all assistants and contributors.

This project is a school-canteen SaaS scaffold. Start with
[README.md](README.md) and [PROJECT_STATUS.md](PROJECT_STATUS.md); use the six
documents in `docs/` as the current product and workflow specifications.

Build the first complete flow incrementally: approved supplier receipt,
stock ledger, cashier sale, cash session, and daily reconciliation. Enforce
tenant/branch permissions throughout. An uploaded invoice or AI response
must never change stock without validated human approval.

Use real persistent implementation for features marked complete. Clearly
identify fixtures, simulations, missing integrations, and untested paths.
The 250-product AIS demonstration is planned, fictional, and unaffiliated;
it has not been seeded by this scaffold.

No application dependencies, launch commands, credentials, migrations, or
automated tests exist yet. Establish and document these during the
foundation milestone. Preserve existing files and unrelated work, and
update the shared status file with concrete changes and verification.
