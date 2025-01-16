# Angular Change Detection Guide
_Based on Real-World Examples from ThriveDesk Frontend_

## Core Concepts

### When Change Detection Happens Automatically
- **HTTP/XHR Requests**
- **User Events** (click, input, etc.)
- **Timers** (`setTimeout`, `setInterval`)
- **Promise resolutions**
- **Observable emissions** from Angular's built-in features

### When Manual Change Detection is Needed
- **WebSocket Events**
- **Third-party Library Callbacks**
- **External API Events**
- **Manual Property Updates Outside Zone.js**

---

## Real Examples from ThriveDesk

### WebSocket Scenarios (Need `detectChanges()`)
- **Real-time updates via WebSocket**

```typescript
websocketService.onReady((bus) => {
  bus.listen('.notification.new', (data) => {
    this.notifications.push(data);
    this.cdr.detectChanges(); // Required
  });
});
```

### Loading State Updates (Need `detectChanges()`)
- **From `thread-actions.component.ts`**

```typescript
sendReply(): void {
  this.isSaving = true;
  this.changeDetectorRef.detectChanges(); // Required for immediate UI feedback
}
```

### Active Inbox Changes (Need `detectChanges()`)
- **From `header.component.ts`**

```typescript
this.activeInboxSub = this.inboxService.activeInboxUpdate.subscribe((inbox) => {
  this.activeInbox = inbox;
  this.selectedInboxId = inbox.id;
  this.cdr.detectChanges(); // Required for critical UI updates
});
```

### HTTP Calls (No `detectChanges()` Needed)
- **From `create-conversation.component.ts`**

```typescript
this.contactService.load(1, value).subscribe(({ contacts }) => {
  this.recipients = contacts.map(c => ({
    value: c.email,
    label: `${c.name} <${c.email}>`
  }));
  // No detectChanges needed - Angular handles HTTP responses
});
```

---

## Quick Decision Guide

### Use `detectChanges()` When:
- Real-time WebSocket updates
- Loading state changes before async operations
- Critical UI updates that need immediate reflection
- External library callbacks
- Manual property updates that need instant UI reflection

### Skip `detectChanges()` When:
- HTTP calls
- User interactions (clicks, inputs)
- Angular form updates
- Router navigation
- Regular Observable emissions from Angular services

---

## Performance Tips
- **Use OnPush strategy** for components with infrequent updates
- **Batch multiple changes** before calling `detectChanges()`
- Consider using `markForCheck()` with OnPush strategy
- Avoid calling `detectChanges()` in loops
- **Use async pipe** where possible to avoid manual subscription management

---

## Common Patterns in ThriveDesk
- WebSocket services require `detectChanges()`
- HTTP service calls don't need `detectChanges()`
- Loading state updates need `detectChanges()`
- List updates from HTTP don't need `detectChanges()`
- Real-time presence updates need `detectChanges()`

---

This guide serves as a practical reference for Angular change detection implementation based on real-world scenarios from the ThriveDesk codebase.
```
