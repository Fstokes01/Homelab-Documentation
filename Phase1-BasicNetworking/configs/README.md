# Phase 1 — Router Configs

This folder stores MikroTik hEX configuration backups and export files.

---

## Files to Add Here

| Filename | How to Create | Description |
|----------|---------------|-------------|
| `hex-backup-phase1.backup` | Winbox → Files → Backup | Full binary backup of the router (includes passwords) |
| `hex-export-phase1.rsc` | Winbox → New Terminal → `/export file=hex-export-phase1` | Plain-text RSC export (human-readable, no passwords) |

---

## How to Create a Backup

### Binary Backup (Winbox)
1. Open Winbox and connect to the hEX.
2. Navigate to **Files**.
3. Click **Backup**.
4. Give it a name (e.g., `hex-backup-phase1`).
5. Click **Backup**.
6. Download the `.backup` file from the Files list and save it here.

### Text Export (Terminal)
1. Open Winbox and connect to the hEX.
2. Navigate to **New Terminal**.
3. Run:
   ```
   /export file=hex-export-phase1
   ```
4. Navigate to **Files**, download `hex-export-phase1.rsc`, and save it here.

---

## Notes

- The `.backup` file is a full snapshot (use this to fully restore the router).
- The `.rsc` export is plain text and useful for reviewing or version-controlling config changes.
- **Do not commit files containing passwords** to a public repository. Use the `.rsc` export for version control; keep `.backup` files in a private/offline location if they contain credentials.
