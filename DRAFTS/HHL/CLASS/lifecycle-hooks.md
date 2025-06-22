# DRAFT: Lifecycle Hooks with Annotations

## Status

Draft ☑ Experimental ☐ Proposal ☐ Accepted ☐ Rejected ☐

## Summary

This draft defines lifecycle hooks as methods annotated with `@lifecycle("stage")`, enabling explicit and flexible control over key phases of an object's lifecycle: initialization, dependency injection, and destruction.

---

## Hook Annotation Syntax
```php
@lifecycle("init")
fn initialize() { ... }

@lifecycle("inject")
fn afterInjection() { ... }

@lifecycle("destroy")
fn cleanup() { ... }
```
- The annotation argument `"stage"` defines when the hook is called.
- Supported stages:
    - `"init"` — after instance creation and normal field initialization
    - `"inject"` — after all `@inject` dependencies are assigned
    - `"destroy"` — before object destruction or resource release

---
## Semantics

- Hooks are **optional**; if the method is missing, it is simply skipped.
- Methods annotated with `@lifecycle` must have no parameters and must not throw exceptions.
- Hooks are invoked automatically by the runtime or DI container at the appropriate lifecycle stage.
- Method names are arbitrary; the lifecycle stage is defined by the annotation.
- Multiple hooks with the same lifecycle stage can exist; invocation order is deterministic but unspecified.
---
### Example:
```php
cl Database
{
    @lifecycle("init")
    fn connect()
    {
        println("Database connected")
    }

    @lifecycle("destroy")
    fn disconnect()
    {
        println("Database disconnected")
    }
}

cl Service
{
    @inject db: Database

    @lifecycle("inject")
    fn onDependenciesInjected()
    {
        println("Service dependencies injected")
    }

    fn run()
    {
        println("Service running")
    }
}
```
## Lifecycle Flow on Object Creation and Destruction
1. Object allocated and regular fields initialized
2. All methods annotated with `@lifecycle("init")` called
3. Dependency injection performed (fields with `@inject`)
4. All methods annotated with `@lifecycle("inject")` called
5. Object used normally
6. Before destruction or release, all methods annotated with `@lifecycle("destroy")` called

## Example Console Output
```php
Database connected
Service dependencies injected
Service running
Database disconnected
```