# Publish Review — Self-Audit Before Submitting

Supernote reviews every plugin submission before it goes live. This reference turns that policy
into a checklist you can actually run against a repo — grep it, read it, verify it — before the
developer submits, instead of finding out after rejection. Source of truth for the policy itself
is Supernote's own published Plugin Review Process & Publishing Requirements; treat this file as
the actionable checklist derived from it, and defer to Supernote's current published policy if the
two ever disagree.

## The review pipeline

`Submit → Basic inspection → Function verification → Security review → Publish`

Reviewers judge the plugin's **actual runtime behavior**, not just its listing copy:

- Installs and runs normally.
- Actual function is basically consistent with the plugin's description.
- Requested permissions match actual function (no over-asking, no under-declaring).
- No abnormal file or data operations.
- No unauthorized data upload or leakage.
- No obvious malicious behavior or security risk.
- No behavior that destabilizes the device or interferes with normal use.

Passing does not mean "bug-free forever" — Supernote can re-check, suspend, or pull a version
post-publish based on user reports or new findings. Treat review as a gate, not a one-time
guarantee, and be ready to ship fixes if something surfaces later.

## Permission matrix

| Permission | Covers |
|---|---|
| `plugin.permission.FILE:READ` | Read `Document`, `EXPORT`, `INBOX`, `MyStyle`, `Note`, `SCREENSHOT` under shared storage |
| `plugin.permission.FILE:WRITE` | Write/modify those same six directories |
| `plugin.permission.FILE:DELETE` | Delete content within those six directories |
| `plugin.permission.INTERNET` | Any network request — `sn-plugin-lib`, RN, Android, or native C/C++ sockets alike |

Least privilege is the rule reviewers apply and the one to self-apply: a plugin that only touches
UI shouldn't request `FILE:READ`; a plugin reading the current note for AI summarization should
request exactly the file + network permissions that function needs, no more. Every permission
listed in `uses-permissions` should map to a real `hasPermission`/`requestPermission` call site —
see Pattern 17 in `patterns.md` for the runtime-gate mechanics. Declared-but-unused and
used-but-undeclared are both things a reviewer (and `adb logcat`'s `PluginSec: DENY` lines, see
gotcha #38) will catch.

## Self-review checklist

Run these against the repo before submitting. Each maps to a concrete check, not a vibe:

1. **Permission diff** — list every `FILE:*`/`INTERNET` gated call site (`hasPermission`,
   `requestPermission`, and any native socket/file API that requires them) and diff it against
   `uses-permissions` in `PluginConfig.json`. Mismatch either way is a flag.
2. **Delete/overwrite audit** — grep native and JS source for delete, batch-delete, overwrite, or
   clear operations on user files or notes. Every one must be reachable only through an explicit,
   traceable user action (a button press, a confirmed dialog) — never on startup, in a background
   task, or as a side effect of an unrelated action.
3. **Network inventory** — list every network call site (`fetch`, `XMLHttpRequest`, `axios`,
   native `Socket`/`OkHttp`/etc.). For each: what data crosses it, and — if it's user data (note
   content, handwriting, PDFs/EPUBs, images, OCR output, user input, file info) — is that clearly
   disclosed in the plugin's description? Undisclosed transmission of any of these is an automatic
   review failure, not a gray area.
4. **No suspicious hardcoded destinations** — grep for hardcoded IPs, hostnames, or URLs used as
   connection targets or defaults. Flag these even when you know they're harmless: a fixed,
   unexplained destination address is structurally identical to a C2/backdoor pattern from a
   reviewer's vantage point, and "it's just my own test server" won't be visible to them. Prefer
   neutral defaults (`127.0.0.1`, an empty field, or an obviously-a-placeholder value) and let the
   user supply their own real target.
5. **Description accuracy** — reread `PluginConfig.json`'s `desc` against what the plugin actually
   does at runtime. No overclaiming ("works offline" when it silently phones home), no undisclosed
   extra functionality bundled in beyond what's described.
6. **Listing name sanity** — confirm `name` in `PluginConfig.json` is the friendly string you want
   in Settings → Apps → Plugins, not a leftover raw npm-style package slug (`sn-my-plugin`) from
   the scaffold default — see the `PluginConfig.json` section in `SKILL.md`.
7. **End-to-end install/run** — actually install the built `.snplg` on a device and exercise the
   core function. Basic inspection and function verification are manual on Supernote's side;
   `npm test`/`tsc` passing tells you nothing about on-device behavior (see the SDK-version note
   in `SKILL.md` for a concrete example of a change that passed every local check and still
   crashed at runtime).
8. **Source code (optional)** — Supernote doesn't require it, and withholding it doesn't block
   publishing. But shipping it (or a public repo link) gives the reviewer a faster, more confident
   path through security review — worth doing when it costs little.

## What fails review

Straight from Supernote's stated criteria — if the checklist above surfaces any of these, fix it
before submitting, don't submit and hope:

- Malicious deletion or destruction of user data.
- Unauthorized collection or leakage of user data.
- Anything that clearly bypasses the permission mechanism (e.g. reaching shared storage or the
  network through a path that sidesteps `hasPermission`/`requestPermission`).
- Malicious code, backdoors, or hidden functionality.
- Behavior that seriously destabilizes the device or blocks normal use.

A plugin being small, simple, or "just like other plugins" is explicitly **not** a rejection
reason on its own — don't over-engineer features to look more "substantial" for review purposes.
