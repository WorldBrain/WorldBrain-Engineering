# Web extension fun

## Simulating production extension lifecycle events in development

Certain events that happen as part of the normal lifecycle of a production extension running in a user's browser are not so clear when it comes to reproducing them in a development environment.

You might want to do this to for testing purposes, invoking our on-update or on-install logic:
https://github.com/WorldBrain/Memex/blob/de4b55bfd0fa803b2bac3609d05b275312a1ee55/src/background-script/index.ts#L147-L165

### Extension install event

This is the [`runtime.onInstalled`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/onInstalled) event, emitted with the `details.reason` param set to `'install'`.

- The only known way to trigger this event on both browsers is by installing a copy of the extension in a browser.
- If already installed, you need to uninstall it - or differiate the manifest so that the browser thinks you're installing a different extension.
- **Of course, you will lose all your data upon uninstall**.

### Extension update event

This is the [`runtime.onInstalled`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/onInstalled) event, emitted with the `details.reason` param set to `'update'`.

- **Chrome:** Go to `chrome://extensions` and press the reload-looking button on the extension's card.
    - This results in the BG script reloading and emitting the `runtime.onInstalled` event very soon after.
    - All other open Options script pages will be closed.
    - Content scripts **do not** get unloaded, just like in a production update, resulting in two instances of them running on each open page after our on-update logic injects them (until the page is refreshed or navigated away from).
- **Firefox:** Unfortunately, we don't know of any way to simulate this event here.

### Other actions that don't trigger these events

- Disabling then re-enabling the extension.
    - Disabling results in the BG script, and any open Options script pages, being closed. **Content scripts are not removed.**
    - Re-enabling results in the BG script starting again. **Content scripts are not re-injected.**
- Reloading the BG script (cmd/ctrl + R in the BG script dev tools).
    - This simply results in the BG script reloading and nothing else.
    - Any existing script using the `runtime.sendMessage` API to talk to the BG script (all of our `src/util/webextensionRPC`-related logic) will still be able to talk to the new BG script instance.
    - Any existing script using the `runtime.connect` API to talk to the BG script (currently only the imports feature UI in the Options script) will **no longer work** and need to be reloaded.
