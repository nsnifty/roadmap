# DRAFT: Dependency Injection via Attributes (Annotations)

## Status
Draft ☑ Experimental ☐ Proposal ☐ Accepted ☐ Rejected ☐

## Summary
This draft proposes a declarative Dependency Injection (DI) mechanism using attributes (`@inject`) on class (`cl`) fields. The goal is to enable automatic provisioning of dependencies at compile-time or runtime. Fields marked for injection must be typed with an interface to ensure safe, predictable resolution.

---

## Syntax Overview

```php
interface ILogger
{
    fn log(message: String)
}

cl ConsoleLogger : ILogger
{
    fn log(message: String)
    {
        println("[LOG] " + message)
    }
}

cl Service 
{
    @inject logger: ILogger

    fn run() {
        self.logger.log("Running service")
    }
}
```
- `@inject` marks a field to be automatically filled by a DI container or compiler logic.
- The field **must be typed with an interface** to qualify for injection.
---
## Injection Behavior
### When resolving an instance of a class:

1. The runtime (or compiler) scans for all fields annotated with `@inject`.
2. For each field, it checks:
    - If the type is an interface → proceed
    - If not → **safe throw** at compile-time or runtime
3. It resolves an instance that implements the specified interface.
4. The instance is assigned to the field before the object is considered constructed.
___
## Type Safety: Interface Requirement
Any field marked with `@inject` **must** use an interface type:
✅ Correct:
```php
@inject logger: ILogger
```
❌ Compile Error:
```php
@inject logger: ConsoleLogger  # Error: not an interface
```
If the field is not an interface, a **safe throw** occurs with a diagnostic like:
```php
Error: Field 'logger' marked as @inject must refer to an interface type. Got: ConsoleLogger
```

## Advanced Features (Planned)

| Feature                  | Description                               |
| ------------------------ | ----------------------------------------- |
| `@inject(optional)`      | Allows null if no dependency is available |
| `@inject(named = "...")` | Resolves a specifically named instance    |
| Lazy injection           | Defers construction until first access    |
| Scoped injection         | Singleton / Transient / Request scope     |
## Error Handling

- **Safe throw** occurs if:
    - Field type is not an interface
    - No binding is registered for the required interface
    - Multiple ambiguous candidates are found without disambiguation
        
- All errors should include:
    - Field name
    - Declared type
    - Attempted resolution path