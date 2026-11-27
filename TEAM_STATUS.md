# Tuck Studios — EaglerXR Team Status

Maintained by Hark (Project Manager). GitHub is the source of truth; this file is the shared status board.
Last updated: 2026-11-25 08:10 CST.

**Rule:** this file on `main` is the canonical status board. Status edits made on a member's branch get merged to `main` (Hark keeps it in sync), so always read it from `main`.

Mission: **Eaglercraft Revolutionizing.** Tucker is Executive Director and has final say on direction, architecture and releases.

## Team

| Member | Role | Status |
| --- | --- | --- |
| Tucker | Executive Director / CEO, final decisions | Active |
| Hark | Project Manager / coordinator only (no coding, as of 2026-11-21) | Stepping down for about a month from 2026-11-27 (usage). ChatGPT is interim PM. |
| Claude | Secondary developer (limited usage: give it substantial, well-scoped tasks) | Connected. GitHub linked (2026-11-21), works on its own branches. Briefed. Static review of PR #1 stereo math done (2026-11-20). |
| ChatGPT | Backup / local + on-device developer, debugging, independent verification | Connected. GitHub linked (2026-11-21). Briefed (handoff received). |
| Gemini | Research & Legal (research, recommendations, and license/attribution checks on code we reuse; no implementation unless assigned; not real legal advice) | Connected via API. Briefed. Free-tier quota: Pro models unavailable, Flash only, no web-search grounding yet. |

## PM handoff (2026-11-27): Hark to ChatGPT, interim, about a month

ChatGPT is interim PM while Hark is out. Same rules: the User (Tucker) has final say; PRs open as drafts; nothing merges without the User; post PR comments only when the User asks; keep this board current on `main`.

**VisorQuest PR #6** (DeepSeek, draft, head `19a48879`). Claude and ChatGPT reviews (2026-11-26): request changes, keep draft. Fix order:
1. `DownloadUtils.download()`: fresh temp file/stream per retry (retries currently append to a partial file); exponential backoff with jitter, honor `Retry-After`; stop retrying 404/permanent errors at the Installer level too (3 x 5 retries per asset today); dedupe assets by hash; in `Logger`, notify the listener outside the lock; narrow `catch (Throwable)`; make interrupts abort the install. Check `StreamDL` for retry byte double-counting (could leave Play disabled). Add JUnit tests.
2. Re-run CI on the head and confirm the JNI sources compile. `VK_getFpsAddress` in `egl_bridge.c` is an always-0 stub and unmentioned scope: document it or split it out; add a `.gitattributes` EOL rule.
3. Fix both docs: `1.21.11-ASSESSMENT.md` says the Visor 0.6.1/AtumVR imports were never pushed (they are, `80602d35`/`ea213418`, unpatched upstream); `CRASH-CHECKING-ASSETS.md` LFS claim contradicts the 2026-11-24 fix, remove `D:\AndroidSdk`, mark "verified" as desktop JVM only.
4. Then port coah80's Quest patches to Visor 0.6.1 for 1.20.1 only. Consider splitting PR #6 into Pojlib fix / Visor port / docs.

**Test build:** newest APK artifact is `visorquest-apk` from `hark/ci-quest-apk` (`af86b4c3`, 2026-11-26 9:17 PM CST); likely without DeepSeek's asset fix. The User is testing it.

**Open with the User:** confirm LGPL-3.0; PR #4 notices say VisorEssentials is "verified" (only the LGPL-3.0 text header is confirmed; -only/-or-later open); LightThinWrapper (PolyForm Shield) parked until the first public APK; LWJGLX license unknown; missing `lwjgl-openxr-quest-3.3.3.jar`; no Java 25 Android JRE; merge decisions on VisorQuest #4-#6 and EaglerXR #1-#3, #5.

## VisorQuest (new project, 2026-11-23)

QuestCraft with Visor + VisorEssentials instead of Vivecraft, based on coah80's port. Repo: `twanye/VisorQuest` (public). `main` = coah80/QCXR-XR-Wrapper; draft PRs #1 Visor, #2 Pojlib, #3 AtumVR import each component to its own branch. Targets: 1.20.1, 1.20.5, 1.20.6, 1.21.1, 1.21.5, 1.21.7, 1.21.11, all 26.x (1.20.7/1.20.8 on hold: not real releases). Repo now public. Nothing built or tested. Architecture doc + version matrix: draft PR #4 (Hark's research merged with Claude's review, 2026-11-24, commit 6462ae8; awaiting Tucker review). Team: OpenCode (DeepSeek) is the active coder (build audit + Visor 0.6.1 Quest port for 1.20.1, branch deepseek/visor-0.6.1-quest, not pushed yet); Codex back 2026-12-06; Claude review/docs only for now. ChatGPT codes via mobile chat mode (Python file edits): Pojlib Phase 0 (version table, pinned Fabric loader, fixDataPermissions) is draft PR #5 into import/pojlib, untested; review notes in PR. Key points: Pojlib's JDK 22 already covers 1.20.5-1.21.11; 26.x needs Java 25 + LWJGL 3.4.x; 26.3 (SDL3) is highest risk; coah's Quest patches must be ported to Visor 0.6.1. Blockers: missing lwjgl-openxr-quest-3.3.3.jar, no Java 25 Android JRE (FCL-Team/Android-OpenJDK-Build Build_JRE_25 is reference only: it has no license, so write our own build from OpenJDK source or ask FCL-Team for one). 2026-11-25: PR #4 docs now reference QuestCraftPlus+ (working 26.2 on Quest) for the LWJGL 3.4 OpenXR fix. 2026-11-26: LGPL-3.0 recommended for VisorQuest, pending the User's confirmation; draft THIRD_PARTY_NOTICES.md added to PR #4 from Gemini's license review. Don't vendor FCL's unlicensed JRE scripts; upstreaming coah80's patches to Visor (now MPL-2.0) needs his consent. 2026-11-25 3:01 PM CST: Pojlib now builds green in CI ("Build Pojlib" workflow, JDK 17) on `import/pojlib` (PR #2) and on Codex's `chatgpt/pojlib-version-table` branch (no PR yet). 2026-11-26 6:45 PM CST: DeepSeek pushed `deepseek/visor-0.6.1-quest` (merges import branches + Pojlib 'Checking assets' infinite-loop fix + two diagnosis docs; Quest patch port to Visor 0.6.1 not yet on it); draft PR #6 into main, CI build green, awaiting Tucker review. Resolved 2026-11-24: Hark uploaded Pojlib's 13 native .so Git LFS objects (from coah80/Pojlib QuestCraft-6.0.0, SHA-256 matched to our pointers) to VisorQuest's LFS store; `git lfs pull` on `import/pojlib` now gets real binaries.

## Current state of EaglerXR (verified from the repo, not from hardware)

Only the load path below has been tested on a headset (see the hardware result). "Builds" means CI compiled it and produced an artifact.

- **main** (`6700095`): WebXR session/pose bridge (`platform-teavm/.../webxr/WebXRBridge.java`, `xrjs/eaglerxr-webxr.js`), `immersive-vr` detection, companion-tab pose link. CI workflow `Build EaglerXR HTML` (`.github/workflows/build-eaglerxr.yml`) produces the `eaglerxr-html` artifact.
- **Draft PR #1** `webxr-stereo-rendering` → main: in-tab stereo into `XRWebGLLayer` (level rendered per XR view, WebXR projection spliced with reversed-Z, eye atlas). WebGL2 renderer only.
- **Draft PR #2** `webxr-controller-pointing` → #1: controller `targetRaySpace` pick ray; mine/place with the controller.
- **Draft PR #3** `webxr-vr-controls-menus` → #2: Visor-style Touch controls (reimplemented, Visor is MPL-2.0, no code copied), snap turn, look-follows-head, floating VR GUI/HUD with laser cursor.
- **Draft PR #5** `webxr-quest-load-fix` → #3: Quest load fix (lazy/defensive VR startup, headset-safe Wasm compile, on-screen error overlay).
- **Latest CI**: run #11 on `webxr-quest-load-fix` (`fb7a2d9`, Enter VR fix: `updateRenderState` returns undefined, not a Promise) succeeded 2026-11-21 14:53 CST. Runs #9 and #10 on the same branch also passed.
- **First hardware result (Tucker, 2026-11-21 14:13 CST, PR #5 build on Quest)**: the game now loads (memory fix confirmed). Enter VR failed with `Cannot read properties of undefined (reading 'then')`; fixed in `fb7a2d9`, not yet retested. Run #8 on `webxr-vr-controls-menus` (`a4e8dd0`) succeeded, artifact `eaglerxr-html` (~48 MB). Run #6 failed (javadoc terminator), fixed in #7.

## Incomplete features (from docs/WEBXR.md)

- Eye images rendered at canvas resolution, then scaled (blurry on Quest).
- No room-scale head position (only IPD offset).
- No first-person hand / held item / controller models in the headset.
- No VR keyboard, VR settings screen, haptics, teleport, climbing, roomscale swing, radial hotbar, seated mode, world scale, two-handed play.
- Lasers are 1 px with no depth test; targeted entity not highlighted.
- No Fabric, ViaFabricPlus or Bedrock work exists yet.

## Blockers

1. **No hardware verification.** Needs Tucker (or ChatGPT on-device) to load the run #11 artifact (PR #5) in Quest Browser, try Enter VR, and report console logs. Checks listed in docs/WEBXR.md "Untested / known limitations".
2. **Stacked draft PRs (#1 → #2 → #3 → #5)** await Tucker's review and merge decision.
3. **Gemini quota**: free tier blocks Pro models and search grounding, so its sources must be checked before we act on them.

## Assigned tasks

| Task | Owner | Status |
| --- | --- | --- |
| First team sync + this status board | Hark | Done |
| Sync report and top research topics | Gemini | Done (summary below) |
| Quest hardware test of PR #5: load | Tucker | Done: loads (2026-11-21) |
| Quest hardware test of run #11: Enter VR | Tucker / ChatGPT | Open |
| Decide Fabric strategy: build-time Mixin transform vs a lightweight browser mod API | Tucker | Open (decision needed) |

## Gemini sync report (2026-11-20, summary)

Recommended research order:
1. WebXR frame loop + framebuffer resolution scaling (`framebufferScaleFactor` / native scale) to fix eye blur and frame pacing. Feasibility: high.
2. Controller ray, hand/item rendering, pose math. High.
3. VR HUD/GUI, lasers, on-screen keyboard. High.
4. ViaFabricPlus protocol translation over WebSockets in the browser. Moderate.
5. Fabric loader / Mixin in TeaVM: very low at runtime (no custom ClassLoaders or runtime bytecode); moderate as a build-time transform.

Hark's review: PR #1 already uses the XR session rAF, `makeXRCompatible()` before the layer, and `layer.getViewport(view)`, so part of Gemini's #1 is already done. The useful new piece is rendering eyes at the layer's recommended resolution. Gemini's sources were not search-grounded this round and still need checking.

Gemini needs: Quest console logs, the class that drives the main rAF loop, and Tucker's Fabric strategy decision.

## Next steps

1. Tucker: redeploy run #11 (PR #5) and test Enter VR on Quest; decide on PRs #1–#3 and #5, and on the Fabric strategy.
2. Render eyes at the XR layer's resolution (fix the blur): Hark scopes it and hands it to Claude or ChatGPT to build, once Tucker approves.
3. Claude and ChatGPT are connected and briefed. Claude has delivered a PR #1 review report.
