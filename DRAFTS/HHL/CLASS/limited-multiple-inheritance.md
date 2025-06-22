# DRAFT: Limited Multiple Inheritance (up to two base classes)

## Status
Draft ☑ Experimental ☐ Proposal ☐ Accepted ☐ Rejected ☐

## Summary
Allow class definitions (`cl`) to inherit from up to two parent classes. This enables controlled multiple inheritance with clearly defined method resolution order and strict conflict rules.

---

## Syntax

```php
cl Animal
{
    fn speak()
    {
        println("...")
    }
}

cl Walker
{
    fn move()
    {
        println("walking")
    }
}

cl Dog : Animal, Walker
{
    fn speak()
    {
        println("Woof!")
    }
}
```
- Multiple inheritance is declared using a comma-separated list after `:`.
- Only **2** base classes are allowed: primary and secondary.

## Rules

1. **Only up to two base classes**
    - `cl C : A, B` is valid
    - `cl C : A, B, D` → compile-time error
        
2. **No state inheritance conflict**
    - If `A` and `B` both define the same field, it's a hard error.
    - If only method conflict is resolved via MRO.
        
3. **Method Resolution Order (MRO)**
	Given:
    ```ns
    cl C : A, B
    ```
	Resolution priority is:
   ```ns
    C → A → B
    ```
So:
- Own methods override all
- If not found in `C`, look in `A`
- If not found in `A`, look in `B`  

4. **Explicit disambiguation** (planned)
```php
self.A::method()
```
To resolve ambiguity manually if both `A` and `B` have `method()`.

___
Example
```php
cl A
{
    fn x()
    {
        println("A::x")
    }
}

cl B
{
    fn x()
    {
        println("B::x")
    }
}

cl C : A, B
{
    fn test()
    {
        self.x()         # → A::x
        self.B::x()      # → B::x (explicit)
    }
}
```