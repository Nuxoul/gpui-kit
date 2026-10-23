# Downstream notification extensions

This fork preserves focused, additive GPUI Component changes used by NxTools.
It is not an upstream release, a GPUI platform fork, or a guarantee of live UI/native acceptance.
Upstream history and licenses remain intact; no version or backend pin is changed.

## Public API

### gpui-component
- `Notification::duration(self, duration: Duration) -> Self`: snapshots an optional in-app timeout into the existing official ToastManager at admission. Unconfigured callers retain five seconds; `autohide(false)` remains persistent; OS delivery remains platform-owned.
- `Notification::header_action(self, action: impl Fn(&mut Self, &mut Window, &mut Context<Self>) -> AnyElement + 'static) -> Self`: optional compact action immediately before original close. It shares hover visibility, stays visible while either header control has keyboard focus, is omitted during exit, and does not change automatic dismissal. This focus rule avoids GPUI keyboard-modality hover suppression removing the button before Enter/Space release. Callers retain action state and stop click propagation when appropriate.

## Provenance and boundaries

- The original published Component 0.8.0 used by NxTools maps to upstream commit `1676826d9d49b90f4d7b6808b69924f58796f6f1` (`crates/component`).
- These patches were ported onto fork main `627ba35afc08ffb49c34affd0af587b2ccf8d836`, not copied as whole older files. In particular, the upstream `fully_visible_bounds().size` correction remains.
- Duration and header-action extensions are separate commits so downstream users can inspect/cherry-pick them independently.
- Copy-to-clipboard business logic is deliberately absent. Applications choose the text, platform write boundary, result handling, localization and layout reservation; the generic component only exposes a header slot. No global keyboard hook or clipboard transport is introduced by this fork.
- No GPUI/pre, theme, editor, package or dependency-version changes are made here.

## Related external Windows patch

NxTools also carries a separate downstream patch to the external crates.io package `gpui-pre-windows = 0.3.9`: `WM_MOUSEACTIVATE` reads live `GWL_EXSTYLE`, returns `MA_NOACTIVATE` when `WS_EX_NOACTIVATE` is set, and otherwise retains `MA_ACTIVATE`. It belongs to GPUI/Zed, not a GPUI Kit crate. This fork does not activate or embed that backend and does not claim it as upstream Kit code. Its reproducible source replacement and verified release identity are archived under `downstream/external/` solely to preserve the user's local modification record.

## Verification

The source-equivalent notification patch passed 46 NxTools production GPUI notification-related tests, including pointer/keyboard copy-once, exact copy-only platform payload/result, failure/retry/timeout/disconnect/stale generation, original remaining-time pause and passive window lifecycle. These use synthetic input/platform queues and do not mutate the real Windows clipboard. The standalone fork workspace was not fully built/tested, and physical/native/visual acceptance is not claimed. Existing upstream tests and the additive builder regression remain with the source.
