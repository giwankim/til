# The World's Best Intro to TDD, Level 1

These are some personal notes that I will be taking while taking J. B. Rainsberger [The World's Best Intro to TDD, Level 1: TDD Done Right
](https://online-training.jbrains.ca/p/wbitdd-01).

## Four Stages of TDD

Different stages of adopting TDD practices based on the current level of practice.

### Stage 1: Test-First Programming

- Bottleneck: **bugs**
- Only One Rule: always start with a failing test
- Goal: reduce the cost of defects by reducing the time to discover them
- Don't need to change anything else about your practice
- When bug rates go down, begin to notice other things that are not quite right when write test first...

### Stage 2: Test-Driven Development

- Bottleneck: **design**
- One More Technique: refactoring
- Reduce the cost of changing the design as the needs change
- Avoid throw large sections away to rewrite them
- Avoid feeling imprisoned by past design decisions

### Stage 3: Predictability

- Bottleneck: **volatility**, especially in design
- New Focus: tiny steps | New Principle: YAGNI
- Relentless pursuit of simplicity
- Avoid big refactoring tasks by refactoring continuously
- Release features sooner by doing just enough design

### Stage 4: Virtuosity?

- Bottleneck: **changing context** (language, framework, greenfield vs legacy)
- Different strategies for different situations
- Evolutionay Design Without* Tests
- More judicious use of scarce resources
- Trust yourself to add tests when you need them

## How to use this model

JBrains will present ideas and demonstrate a wide range of techniques. Should decide which things to practice now and defer the rest.

## Sample Practive Strategies

- Stage 1: build the habit of writing tests first
- Stage 2: build the habit of refactoring after every new test passes
- Stage 3: build the habit of writing the smallest test that might fail
- Stage 4: build your own version of the course!

## In the course...

1. Start with Stage 1: writing tests first
1. Focus on Stage 2 (most of the course): design and refactoring
1. Show some of Stage 3: tiny steps
