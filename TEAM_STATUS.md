# Tuck Studios — EaglerXR Team Status

Maintained by Hark (Project Manager). GitHub is the source of truth; this file is the shared status board.
Last updated: 2026-11-21 12:43 CST.

**Rule:** this file on `main` is the canonical status board. Status edits made on a member's branch get merged to `main` (Hark keeps it in sync), so always read it from `main`.

Mission: **Eaglercraft Revolutionizing.** Tucker is Executive Director and has final say on direction, architecture and releases.

## Team

| Member | Role | Status |
| --- | --- | --- |
| Tucker | Executive Director / CEO, final decisions | Active |
| Hark | Project Manager, second-in-command, primary developer, repo/build monitoring | Active. Synced. |
| Claude | Secondary developer (limited usage: give it substantial, well-scoped tasks) | Connected. GitHub linked (2026-11-21), works on its own branches. Briefed. Static review of PR #1 stereo math done (2026-11-20). |
| ChatGPT | Backup / local + on-device developer, debugging, independent verification | Connected. GitHub linked (2026-11-21). Briefed (handoff received). |
| Gemini | Research agent (research and recommendations, no implementation unless assigned) | Connected via API. Briefed. Free-tier quota: Pro models unavailable, Flash only, no web-search grounding yet. |

## Current state of EaglerXR (verified from the repo, not from hardware)

Nothing below has been tested on a headset. "Builds" means CI compiled it and produced an artifact.

- **main** (`6700095`): WebXR session/pose bridge (`platform-teavm/.../webxr/WebXRBridge.java`, `xrjs/eaglerxr-webxr.js`), `immersive-vr` detection, companion-tab pose link. CI workflow `Build EaglerXR HTML` (`.github/workflows/build-eaglerxr.yml`) produces the `eaglerxr-html` artifact.
- **Draft PR #1** `webxr-stereo-rendering` → main: in-tab stereo into `XRWebGLLayer` (level rendered per XR view, WebXR projection spliced with reversed-Z, eye atlas). WebGL2 renderer only.
- **Draft PR #2** `webxr-controller-pointing` → #1: controller `targetRaySpace` pick ray; mine/place with the controller.
- **Draft PR #3** `webxr-vr-controls-menus` → #2: Visor-style Touch controls (reimplemented, Visor is MPL-2.0, no code copied), snap turn, look-follows-head, floating VR GUI/HUD with laser cursor.
- **Draft PR #5** `webxr-quest-load-fix` → #3: Quest load fix (lazy/defensive VR startup, headset-safe Wasm compile, on-screen error overlay).
- **Latest CI**: run #10 on `webxr-quest-load-fix` (`4fffd6b`) succeeded 2026-11-20; run #9 on the same branch also passed. Run #8 on `webxr-vr-controls-menus` (`a4e8dd0`) succeeded, artifact `eaglerxr-html` (~48 MB). Run #6 failed (javadoc terminator), fixed in #7.

## Incomplete features (from docs/WEBXR.md)

- Eye images rendered at canvas resolution, then scaled (blurry on Quest).
- No room-scale head position (only IPD offset).
- No first-person hand / held item / controller models in the headset.
- No VR keyboard, VR settings screen, haptics, teleport, climbing, roomscale swing, radial hotbar, seated mode, world scale, two-handed play.
- Lasers are 1 px with no depth test; targeted entity not highlighted.
- No Fabric, ViaFabricPlus or Bedrock work exists yet.

## Blockers

1. **No hardware verification.** Needs Tucker (or ChatGPT on-device) to load the run #10 artifact (PR #5) in Quest Browser and report console logs. Checks listed in docs/WEBXR.md "Untested / known limitations".
2. **Stacked draft PRs (#1 → #2 → #3 → #5)** await Tucker's review and merge decision.
3. **Gemini quota**: free tier blocks Pro models and search grounding, so its sources must be checked before we act on them.

## Assigned tasks

| Task | Owner | Status |
| --- | --- | --- |
| First team sync + this status board | Hark | Done |
| Sync report and top research topics | Gemini | Done (summary below) |
| Quest hardware test of run #10 artifact (PR #5) | Tucker / ChatGPT | Open |
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

1. Tucker: test run #10 (PR #5) on Quest; decide on PRs #1–#3 and #5, and on the Fabric strategy.
2. Hark: scope a branch to render eyes at the XR layer's resolution (fix the blur), once Tucker approves.
3. Claude and ChatGPT are connected and briefed. Claude has delivered a PR #1 review report.
