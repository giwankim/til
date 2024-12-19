# Item 36: Prefer composition over inheritance

When all we need is a simple code extraction or reuse, inheritance should be used with caution; instead, we should prefer a lighter alternative: class composition.

Important downsides of this approach:

- __We can only extend one class.__
- __When we extend, we take everything from a class__
- __Using superclass functionality is much less explicit.__
