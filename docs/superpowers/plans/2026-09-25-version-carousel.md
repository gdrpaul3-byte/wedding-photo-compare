# Four-or-more Version Carousel Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development or superpowers:executing-plans. Steps use checkbox syntax for tracking.

**Goal:** Show one photo at a time only for groups with at least four versions, keeping two/three-version comparisons unchanged.

**Architecture:** Extend the two existing self-contained HTML viewers without changing manifests or assets. Normalize with panelsFor(pair), gate carousel behavior on panels.length >= 4, and keep per-card version selection separate from shooting-cut navigation. Reuse lightbox zoom/pan state across version changes.

**Tech Stack:** Native HTML/CSS/JavaScript, Node test runner, CUA browser verification, existing GitHub Pages.

---

## Task 1: Test and implement conditional carousel

Files: modify `shared-photo-comparison/index.html` and `글렌셀렉/보정사진_비교.html`; create `tests/photo-carousel.test.mjs`; preserve existing `tests/photo-retouch-comparison.test.mjs` and `tests/github-pages-package.test.mjs` unless a deliberately changed accessible label requires an assertion update.

- [ ] Write behavior tests against the real inline application script in an isolated DOM harness, or existing available DOM runtime. No production-only test exports. Verify 2/3 panels remain visible, 4 and synthetic 5 panel groups show one, ordered wrapping, version label/count, selection handed to lightbox, close synchronization, and cut navigation reset. Add pointer tests for horizontal threshold, vertical/cancel/multitouch suppression and post-swipe click suppression; zoomed drag pans without changing version.
- [ ] Run `node --test tests/photo-carousel.test.mjs` and observe feature-related assertion failures before changing production code.
- [ ] Keep `panelsFor(pair)` unchanged. Add `isCarousel(pair)` returning `panelsFor(pair).length >= 4`; maintain a selected-version map by current-tab cut index, cleared on tab switch. Keep existing two/three panel markup and CSS.
- [ ] Add a single-column modifier only for carousel containers. Render one selected panel, stable previous/next buttons with Korean names, a polite version counter, and full un-cropped images. Version controls remain focusable during rerenders. Prefer retaining panel elements and toggling `hidden` with explicit `[hidden]` CSS if that simplifies focus, but hide offscreen panels from accessibility and load the selected image explicitly.
- [ ] Attach a bounded horizontal gesture only to carousel image stages. Use pointer capture, primary-pointer tracking, minimum 48px displacement and horizontal dominance (abs(dx) > abs(dy)*1.25). Vertical gestures, canceled pointers, and more than one active pointer never navigate. Suppress the synthetic click following a drag; do not suppress keyboard activation. In the lightbox, only enable version swipe at 100%; retain existing pan gestures while zoomed.
- [ ] Add lightbox version controls outside the transformed stage, displayed only for >=4. Arrow keys navigate versions for >=4 and retain existing cut navigation for <4. Explicit previous/next shooting-cut buttons always navigate cuts and reset zoom/pan/version. Version navigation never resets zoom/pan. Opening uses the selected card version; closing leaves its selected version synchronized and restores focus safely.
- [ ] Update short Korean help text without changing total counts. Preserve the offline `원본 열기` control's existing lightbox behavior and OneDrive recovery text; preserve public asset paths and network recovery text. Do not convert the existing button into a new external-file link. No asset rebuilds, external dependency, autoplay, or photo edits.
- [ ] Run all tests: `node --test tests/photo-retouch-comparison.test.mjs tests/github-pages-package.test.mjs tests/photo-carousel.test.mjs`. Inspect manifests for exact preservation and run `git -C shared-photo-comparison diff --check`.
- [ ] Independent spec review, then code-quality review. Fix and retest any concrete problems.

## Task 2: Browser verification and deploy

Files: no image changes. Only fix viewer/test issues demonstrated by checks.

- [ ] Serve workspace via a loopback-only static HTTP server and verify public HTML with CUA, desktop and phone-size viewport. Verify 2/3 layouts unchanged and both 4-panel groups show one active image. Check all four versions, forward/backward wrapping, keyboard, zoom/pan preservation, close focus, cut reset and responsive controls. Use documented CUA gesture APIs if available; otherwise explicitly distinguish DOM-harness gesture coverage from real browser input.
- [ ] Re-run automated tests after any fixes. Confirm 21 groups, 53 files, 54 references; both manifests unchanged and no full-resolution photos staged.
- [ ] Commit only reviewed public HTML and design/plan documentation in the nested repository; retain local viewer/tests in their existing workspace locations. Push to the existing Pages branch under the user's prior public-deployment authorization.
- [ ] Verify the matching GitHub Pages workflow succeeds, reload the actual published page, and check carousel operation there. Report the existing site URL and the >=4-only rule.
