# Angular Change Detection Guide

This guide provides a practical overview of Angular change detection mechanisms, illustrated with real-world examples from the ThriveDesk frontend application.

## Core Concepts

Angular's change detection system efficiently keeps the component view synchronized with the underlying data model. It automatically detects changes in various scenarios:

* **HTTP/XHR Requests:** When an HTTP request resolves, Angular triggers change detection to update the component view with the fetched data.
* **User Events (click, input, etc.):** User interactions like clicks, form input, and button presses trigger change detection to reflect the changes in the view.
* **Timers (setTimeout/setInterval):** When timer callbacks from `setTimeout` or `setInterval` functions are invoked, Angular triggers change detection to update the view accordingly.
* **Promise resolutions:** Asynchronous operations initiated using Promises trigger change detection upon resolution.
* **Observable emissions from Angular's built-in features:** Observables emitted by Angular services or directives can trigger change detection when subscribed to within a component.

## When Manual Change Detection is Needed

While Angular automates change detection in most cases, manual intervention is necessary in specific scenarios:

* **WebSocket Events:** Real-time updates received via WebSockets don't automatically trigger change detection. You need to call the `detectChanges` method on the component's `ChangeDetectorRef` to update the view manually.
* **Third-party Library Callbacks:** Callbacks from external libraries that are not integrated with Angular's change detection mechanism might require manual `detectChanges` calls.
* **External API Events:** Events emitted from external APIs that are not part of Angular's framework might necessitate manual change detection.
* **Manual Property Updates Outside Zone.js:** If you directly modify component properties outside of Angular's Zone.js context, you might need to trigger change detection manually.

## Real Examples from ThriveDesk

The following examples from the ThriveDesk codebase illustrate scenarios where manual change detection is required:

**WebSocket Scenarios (Need detectChanges)**

```typescript
websocketService.onReady((bus) => {
  bus.listen('.notification.new', (data) => {
    this.notifications.push(data);
    this.cdr.detectChanges(); // Required
  });
});
