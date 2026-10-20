# Code smells

A smell is a surface sign that a design principle might be strained. It is a
hint to look closer, never a verdict: a long function that reads top to
bottom as one story is fine, and a short one that mixes three concerns is
not. For each smell below: how to recognize it, which principle it usually
strains, and the refactoring from
[refactorings.md](refactorings.md) that addresses it.

## Bloaters

Things that have grown too large to work with.

| smell                | recognize it by                                                                 | strains                | refactoring                                                   |
| -------------------- | ------------------------------------------------------------------------------- | ---------------------- | ------------------------------------------------------------- |
| Long method          | You scroll to read it; comments mark its sections                               | SRP                    | Extract Function per section, named after the step            |
| Large class          | Many methods and fields serving different actors; edited for unrelated reasons  | SRP                    | Extract Class along the axis of change                        |
| Long parameter list  | Five or more parameters; several always travel together                         | SRP, encapsulation     | Introduce Parameter Object; Preserve Whole Object             |
| Data clumps          | The same three fields appear together in many signatures and classes            | encapsulation          | Extract Class for the clump, then move behavior onto it       |
| Primitive obsession  | Money as float, email as string, status as int, with validation at every use    | encapsulation          | Replace Primitive with Object; Replace Type Code with Subclass |
| Deep nesting         | `if` inside `for` inside `if` inside `try`; the happy path is hidden            | KISS                   | Replace Nested Conditional with Guard Clauses; Extract Function |

## Object-orientation abusers

Places where the object model is misused.

| smell                        | recognize it by                                                            | strains                | refactoring                                                  |
| ---------------------------- | -------------------------------------------------------------------------- | ---------------------- | ------------------------------------------------------------ |
| Switch on type code          | The same switch over a kind appears in several methods                      | OCP, polymorphism      | Replace Conditional with Polymorphism; Strategy              |
| Refused bequest              | A subclass overrides inherited methods to throw or no-op                    | LSP                    | Replace Inheritance with Delegation; Push Down Method        |
| Type checks                  | `instanceof` or `typeof` branches before calling a method                   | LSP, polymorphism      | Move the behavior into the types                             |
| Temporary field              | A field set only during one operation and null otherwise                     | encapsulation, SRP     | Extract Class for the operation; Introduce Special Case      |
| Parallel inheritance         | Adding a subclass in one hierarchy forces one in another                     | composition            | Move Method and Move Field until one hierarchy can go        |
| Alternative classes, different interfaces | Two classes do the same job with different method names        | ISP, abstraction       | Rename Method; Extract Interface or Adapter                  |

## Change preventers

One change requires many edits.

| smell            | recognize it by                                                         | strains                 | refactoring                                            |
| ---------------- | ----------------------------------------------------------------------- | ----------------------- | ------------------------------------------------------ |
| Divergent change | One class is changed for many different reasons                          | SRP                     | Extract Class per reason to change                     |
| Shotgun surgery  | One kind of change touches many classes                                  | SoC, SRP                | Move Function and Move Field into one place; Inline Class |
| Speculative generality | Abstract classes, hooks, and parameters nothing uses                | YAGNI, KISS             | Collapse Hierarchy; Inline Function; Remove Dead Code  |

## Dispensables

Things whose absence would make the code clearer.

| smell               | recognize it by                                                        | strains           | refactoring                                      |
| ------------------- | ---------------------------------------------------------------------- | ----------------- | ------------------------------------------------ |
| Duplicated code     | The same decision encoded in two or more places; fixed in one, not the other | DRY           | Extract Function; Pull Up Method; Form Template Method |
| Dead code           | Unreachable branches, unused parameters, commented-out blocks           | KISS              | Remove Dead Code                                 |
| Lazy class          | A class that does so little it isn't worth the file                     | KISS              | Inline Class; Collapse Hierarchy                 |
| Data class          | Only getters and setters, with the logic that uses them elsewhere       | encapsulation     | Move Function onto the data; Encapsulate Variable |
| Comments as deodorant | A comment explaining what a block does instead of a name               | meaningful names  | Extract Function; Rename                         |

## Couplers

Things that know too much about each other.

| smell                  | recognize it by                                                   | strains              | refactoring                                     |
| ---------------------- | ----------------------------------------------------------------- | -------------------- | ----------------------------------------------- |
| Feature envy           | A method uses another object's data more than its own             | encapsulation, SRP   | Move Function to where the data lives           |
| Inappropriate intimacy | Two classes reach into each other's private parts                  | encapsulation        | Move Function and Move Field; Hide Delegate     |
| Message chain          | `a.getB().getC().doX()`                                           | Law of Demeter       | Hide Delegate; Extract Function on the neighbor |
| Middle man             | A class whose methods all just delegate                            | KISS                 | Remove Middle Man; Inline Function              |
| Concrete dependency    | Business logic constructs its own database, HTTP client, or clock  | DIP                  | Inject the collaborator; Extract Interface if a fake is needed |
| Global state           | Singletons and module-level mutable variables read from everywhere | DIP, encapsulation   | Pass the instance in; Encapsulate Variable      |

## What the MCP detector measures

The `software-design` MCP's `check_smells` tool scans source text with
language-agnostic heuristics. It does not parse the language, so it favors
obvious findings and misses exotic syntax. Each finding carries the
principle it hints at and a suggested refactoring. Every threshold can be
overridden per call.

| detector id       | default threshold | principle                | severity escalates at |
| ----------------- | ----------------- | ------------------------ | --------------------- |
| `long-method`     | body over 40 lines         | single-responsibility    | twice the threshold   |
| `large-class`     | more than 12 methods       | single-responsibility    | twice the threshold   |
| `too-many-params` | more than 4 parameters     | single-responsibility    | threshold plus 2      |
| `deep-nesting`    | control depth over 4       | kiss                     | threshold plus 2      |
| `duplication`     | a non-trivial line repeated 3 or more times | dry     | threshold plus 2      |
| `large-file`      | over 400 lines             | separation-of-concerns   | twice the threshold   |

Use the output as a list of places to look, then apply the judgment in the
tables above. Three findings on a file do not mean three refactorings; they
often share one cause.
