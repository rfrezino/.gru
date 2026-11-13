---
type: Feature
title: Admin panel
description: Global-admin back-office for entities, members, news, daily messages, themes and languages.
tags: [feature, admin]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
resource: /apps/web/app/admin
---

* Access by the `admin` custom claim ([constitution](/decisions/constitution.md)); per-entity delegation (`entityRoles`) is reserved, not implemented.
* List pages are MUI tables with dedicated create/edit pages. Deleting is soft with a confirm.
* Granting admin: `scripts/grant-global-admin.ts <uid>` (`npm run grant:admin`).
* No audit trail yet, though the vision requires one for AI-assisted content.
* Forms use [translation](/features/content-translation.md) and [themes](/features/reusable-themes.md).
