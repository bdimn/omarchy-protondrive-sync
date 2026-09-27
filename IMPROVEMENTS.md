# Recommended Improvements for Proton Drive Sync

This file captures the next improvements worth making after the startup health and retry improvements.

## 1) Better startup health and visibility

The plugin should clearly show why it is not running when startup is blocked.

### Suggested changes
- Surface explicit states like:
  - CLI not installed
  - not authenticated
  - local folder missing
  - remote folder missing
  - daemon failed to start
- Show the exact daemon stderr or exit reason in the panel
- Distinguish between "paused", "stopped", "not configured", and "crashed"
- Provide a manual "restart daemon" action in the UI

### Why this matters
The current startup flow can leave the plugin looking idle even when the user expects sync to be active.

---

## 2) More robust daemon restart behavior

The daemon should recover more gracefully from transient crashes and startup failures.

### Suggested changes
- Retry startup automatically a few times on unexpected exit
- Keep a small retry-backoff schedule instead of immediate repeated restarts
- Keep retry state visible in the UI
- On repeated failure, show a clear final error instead of silently staying dead

### Why this matters
A temporary problem during launch should not leave the plugin permanently in a non-running state.

---

## 3) Safer remote folder validation

The plugin should warn more aggressively when the configured remote path is too broad or risky.

### Suggested changes
- Warn if the remote is `/my-files` instead of a dedicated subfolder
- Validate that the remote path exists and is writable before starting
- Show a warning if the folder is extremely large or contains unrelated files
- Consider forcing a subfolder-specific remote path for the default configuration

### Why this matters
Using `/my-files` as the sync root can cause the daemon to sync an unnecessarily large amount of unrelated content.

---

## 4) Richer conflict reporting

The current conflict handling is safe, but the UI can do more to explain what happened.

### Suggested changes
- Include local and remote hashes in each conflict event
- Show whether the file was resolved as:
  - local kept
  - remote kept
  - renamed local copy
- Add a summary of recent conflict counts
- Provide a way to inspect the most recent conflict details in the panel

### Why this matters
Users should be able to understand exactly why a file was preserved and what option won.

---

## 5) Dry-run / preview mode

Add a mode that reports what would happen without actually making changes.

### Suggested changes
- Show a preview of pending uploads/downloads/deletes
- Include a dry-run toggle in the UI or CLI
- Log the exact action plan before applying it
- Allow users to review large operations before they run

### Why this matters
This helps users understand the sync engine without risking accidental destructive changes.

---

## 6) Better rename / move detection

The current design admits that move detection is limited.

### Suggested changes
- Detect same-content moves between paths within a single reconcile pass
- Avoid unnecessary re-upload/re-download of files that were just renamed
- Track directory renames more intelligently
- Handle empty-folder cleanup more predictably after moves

### Why this matters
This would reduce unnecessary work and make large folder reorganizations much faster.

---

## 7) More metadata, better debugging

The sync state could be richer and easier to inspect.

### Suggested changes
- Store last modified timestamps
- Store a more detailed per-file history
- Include reasons for each sync decision in the state or debug output
- Expose a debug panel or command that prints the current tracked state

### Why this matters
The more the daemon can explain its choices, the easier it is to debug edge cases and conflicts.

---

## 8) Health dashboard for the plugin

The bar widget should act as a concise operational dashboard, not just a toggle.

### Suggested changes
- Show daemon status: running / paused / stopped / retrying
- Show last reconcile time
- Show tracked file count
- Show whether local file watching is active
- Show whether Proton Drive auth is valid

### Why this matters
This makes the plugin feel reliable and transparent to the user.

---

## 9) Better user safety prompts for destructive actions

Before a destructive action is applied, show a clearer explanation.

### Suggested changes
- Warn when a delete is being propagated because the surviving side still matches the last sync
- Surface the exact path and reason for a delete in the UI
- Provide a way to inspect pending destructive actions before they happen

### Why this matters
This preserves the project’s safety-first model while making it easier for users to trust the engine.

---

## 10) More polished panel UX

The current UI already works, but it can be made easier to understand.

### Suggested changes
- Clarify the difference between setup errors and runtime sync errors
- Use more consistent status text for "paused", "stopped", and "not ready"
- Highlight the current root paths more clearly
- Add a small hint when a folder is configured but not yet synced

### Why this matters
A clean status model makes the plugin feel stable and intentional.

---

## Priority order

If you want to keep the roadmap simple, the best next priorities are:

1. Better startup health message visibility ✅
2. Startup retry handling ✅
3. Safer remote folder validation
4. Better conflict reporting
5. Rename / move detection

These give the most practical benefit with the least conceptual risk.
