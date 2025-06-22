# DRAFT: Custom Event Hooks (User-defined Hooks)

## Status

Draft ☑ Experimental ☐ Proposal ☐ Accepted ☐ Rejected ☐

## Summary

Custom hooks allow developers to define event handlers bound to named events, with optional priority, enabling flexible event-driven programming and extensibility.

---
## Hook Annotation Syntax
```php
@hook("event.name", priority=10)
fn handlerFunction() {
    // handle event
}

@hook("event.name")
fn anotherHandler() {
    // handle same event, default priority
}
```
- `"event.name"` — string identifying the event to listen for.
- `priority` (optional) — integer, higher value = higher priority, default priority = 0.
- Multiple handlers can subscribe to the same event.
- Handlers without explicit priority default to 0.
---
## Optional: Hook Type Extension
If finer control over hook invocation time is needed, an optional `type` argument can be provided:
```php
@hook("event.name", type="before", priority=20)
fn beforeHandler() {
    // executed before main event action
}

@hook("event.name", type="after")
fn afterHandler() {
    // executed after main event action
}
```
- Supported types include `"before"`, `"after"`, `"around"` (future extension).
- If omitted, handlers are treated as default type (e.g., "normal").
---
## Semantics

- Hook methods must have no parameters (or optionally support event data — TBD).
- At runtime, when event `"event.name"` is triggered, all handlers registered for that event and hook type are invoked in order of descending priority.
- Handlers with the same priority are invoked in deterministic but unspecified order.
- Hook types define when the handler runs relative to the main event action (`before`, `after`, `around`).
- Handlers can optionally stop propagation (future extension).
- Hook methods must not throw exceptions to maintain event dispatch stability.
---
### Example: Basic usage without hook type
```php
cl UserModule
{
    @hook("user.registered", priority=10)
    fn sendWelcomeEmail()
    {
        println("Sending welcome email")
    }

    @hook("user.registered")
    fn logRegistration()
    {
        println("Logging registration event")
    }
}
```
### Example: Usage with hook types
```php
cl UserModule
{
    @hook("user.registered", type="before", priority=20)
    fn validateUser()
    {
        println("Validating user data")
    }

    @hook("user.registered", type="after")
    fn logRegistration()
    {
        println("Logging registration event")
    }
}
```
### Example: Usage with event context and propagation control
```php
@hook("user.registered", type="before")
fn validateUser(ctx: EventContext)
{
    let data = ctx.data // read-only
    if (!data.isValid)
    {
        println("User data invalid, stopping propagation")
        ctx.stopPropagation()
    }
}
```

---
## Usage (Runtime/Event Dispatcher)

When user registration occurs:
```php
triggerEvent("user.registered")
```
When user registration occurs with event data:
```php
let eventData = {
	isValid: true,
	userName: "Alice"
}
triggerEvent("user.registered", eventData)
```

Runtime calls handlers in priority order:
```php
Sending welcome email
Logging registration event
```
For **usage with hook types**, handlers are called in this order:
```php
Validating user data
... main event action ...
Logging registration event
```
---
## Notes
- This system is **orthogonal** to lifecycle hooks — it allows reacting to arbitrary events beyond object lifecycle.
- Priorities give fine control over execution order.
- Hook types allow flexible timing around the event.