---
name: browser-dom-form-fallback
description: Recover browser form automation when browser_type/browser_click element refs fail or snapshots go stale by inspecting DOM and interacting via browser_console JavaScript.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [browser, qa, fallback, forms, local-dev]
---

# Browser DOM Form Fallback

Use this when browser automation is partially working but normal element-ref actions fail, especially on local dev apps or pages that re-render quickly.

## Trigger conditions

- `browser_type` fails with ref parsing/selector errors
- snapshot refs appear valid but interactions do nothing
- page transitions from a loading state to login/app state and refs become stale
- you need to log into a local dev/admin page reliably

## Procedure

1. **Refresh the current DOM state**
   - Call `browser_snapshot(full=true)` after navigation or any click that may re-render.
   - Do not trust old refs from a previous snapshot.

2. **Inspect the actual DOM with `browser_console`**
   Use an expression like:
   ```js
   (() => ({
     title: document.title,
     bodyText: document.body.innerText.slice(0, 300),
     inputCount: document.querySelectorAll('input').length,
     inputs: Array.from(document.querySelectorAll('input')).map((el, i) => ({
       i,
       id: el.id,
       type: el.type,
       name: el.name,
       placeholder: el.placeholder,
       autocomplete: el.autocomplete,
       outer: el.outerHTML.slice(0, 200),
     })),
     buttons: Array.from(document.querySelectorAll('button')).map((b, i) => ({
       i,
       text: (b.textContent || '').trim(),
     })),
   }))()
   ```
   This reveals stable selectors like `#email`, `#password`, button text, etc.

3. **Set input values using native value setters**
   For Vue/React-controlled inputs, use the element prototype setter and dispatch events:
   ```js
   (() => {
     const setNativeValue = (el, value) => {
       const setter = Object.getOwnPropertyDescriptor(el.__proto__, 'value')?.set
       if (setter) setter.call(el, value)
       else el.value = value
       el.dispatchEvent(new Event('input', { bubbles: true }))
       el.dispatchEvent(new Event('change', { bubbles: true }))
     }

     const email = document.querySelector('#email')
     const password = document.querySelector('#password')
     const button = Array.from(document.querySelectorAll('button')).find(
       b => (b.textContent || '').includes('登录')
     )

     if (!email || !password || !button) {
       return { ok: false, reason: 'missing elements' }
     }

     setNativeValue(email, 'user@example.com')
     setNativeValue(password, 'secret')
     button.click()
     return { ok: true, path: location.pathname, title: document.title }
   })()
   ```

4. **Verify success immediately**
   - Run `browser_snapshot(full=true)` again.
   - Confirm the page changed to the expected app/admin UI.
   - If needed, check `browser_console()` for JS errors.

## Notes

- This fallback is especially useful on local Nuxt/Vue dev pages where snapshots are briefly stale or component wrappers confuse ref-based actions.
- Prefer stable DOM selectors (`#id`, `input[type=password]`, exact button text) discovered via `browser_console`.
- If `document.querySelectorAll('input')` returns 0 unexpectedly, the page likely re-rendered or navigated; refresh with `browser_snapshot` or `browser_navigate` first.

## Verification checklist

- [ ] Page snapshot shows the login form or target form you expect
- [ ] DOM inspection reveals actual input/button selectors
- [ ] Native setter + input/change events used for controlled inputs
- [ ] Post-submit snapshot confirms successful navigation or logged-in state
