---
name: software-design
description: >-
  Apply software-design judgment when refactoring, reviewing, or structuring
  code: SOLID, the OOP pillars, DRY, KISS, YAGNI, composition over inheritance,
  the Law of Demeter, the 23 Gang of Four patterns, and the code-smell to
  refactoring catalog, with a strong bias toward the smallest change that
  removes a concrete pain. Use this whenever the user asks to refactor, clean
  up, simplify, restructure, decouple, or "make this cleaner", says a class,
  function, or module is too big or a mess, asks which design pattern to use or
  whether to use one at all, asks for a design, architecture, or code-quality
  review, or mentions SOLID, DRY, KISS, YAGNI, clean code, code smells, tech
  debt, or design patterns, even when they only want a quick opinion.
license: MIT
metadata:
  author: qwertymuzaffar
  version: "1.0.0"
---

# Software design

Design principles are a vocabulary for diagnosing why code is hard to change,
not a checklist to satisfy. Every principle has a cost, usually indirection,
and that cost is paid by every future reader. So the question is never "does
this code follow SOLID?" but "what concretely hurts here, and what is the
smallest change that stops it?" The most common way an agent makes code worse
is answering the first question instead of the second: patterns nobody asked
for, interfaces with one implementation, five files where there was one.

## Workflow

### 1. Name the pain before the principle

Write down, in one sentence, what is concretely hard: "every new payment
provider touches four files", "this function can't be tested without a real
database", "the same discount rule exists in three places and two already
disagree". If no such sentence exists, there is nothing to refactor. Say so
plainly and stop. "It could be cleaner" is not a pain.

### 2. Get a safety net

Refactoring means changing structure while preserving behavior, and you only
know behavior is preserved if something checks it. Run the existing tests
first and note what they cover. If the code you will touch has no tests,
write characterization tests that pin its current outputs, including the odd
ones, before changing anything. Without a net you are rewriting, not
refactoring, and should say so.

### 3. Diagnose

Match the pain to a smell, the smell to the principle it strains, and the
principle to a refactoring. The table below covers the common cases; the
full catalogs are in the references.

If the `software-design` MCP server is connected, run `check_smells` on the
code for measurable hints (long method, large class, long parameter list,
deep nesting, duplication, large file) and `explain_concept` for the
canonical definition of anything you cite. Treat smell output as hints, never
verdicts: a 45-line function that reads top to bottom is fine. The server
installs with `claude mcp add software-design -- npx -y mcp-software-design`.
Without it, use [references/code-smells.md](references/code-smells.md).

| pain                                                   | smell                        | principle strained    | first move                                                                   |
| ------------------------------------------------------ | ---------------------------- | --------------------- | ---------------------------------------------------------------------------- |
| Can't test without a real database, clock, or network  | concrete dependency in logic | DIP                   | Inject the collaborator; add an interface only when a fake is needed         |
| Every new variant edits the same switch                | switch on a type code        | OCP, polymorphism     | Replace the conditional with polymorphism or Strategy, once two variants exist |
| A function needs scrolling to read                     | long method                  | SRP                   | Extract functions named after the steps                                      |
| Six arguments, usually passed together                 | long parameter list          | SRP, encapsulation    | Introduce a parameter object                                                 |
| Same rule fixed in one place, still broken in another  | duplicated knowledge         | DRY                   | Extract one function or constant; leave look-alike code alone                |
| `if` inside `for` inside `if` inside `try`             | deep nesting                 | KISS                  | Guard clauses, then extract the inner block                                  |
| A class edited for unrelated reasons                   | large class                  | SRP                   | Extract a class along the axis of change                                     |
| One change fans out across many files                  | shotgun surgery              | SoC, SRP              | Move the functions and fields that change together into one place           |
| `a.getB().getC().doX()`                                | message chain                | Law of Demeter        | Hide the delegate; ask the neighbor to do it                                 |
| A subclass overrides a method to throw "not supported" | refused bequest              | LSP                   | Replace inheritance with delegation or split the hierarchy                   |
| Implementers stub methods they never use               | fat interface                | ISP                   | Split the interface by client role                                           |
| Callers check `instanceof` before acting               | type checks                  | LSP, polymorphism     | Move the varying behavior into the types                                     |
| Public setters let callers break an invariant          | exposed internals            | encapsulation         | Encapsulate the field behind intent-revealing methods                        |
| A variable needs a comment to say what it holds        | cryptic name                 | meaningful names      | Rename                                                                       |

### 4. Pick the smallest move that removes the pain

Work up this ladder and stop at the first rung that resolves the sentence
from step 1:

1. Rename, so the code says what it does.
2. Extract or inline a function or variable.
3. Guard clauses, decompose a conditional, split a phase.
4. Introduce a parameter object; move a function or field to where its
   data lives.
5. Extract a class along the axis of change.
6. Replace a conditional with polymorphism, or a Strategy.
7. Introduce a named pattern.

Each rung costs more indirection than the one before. Rungs 1 to 4 are
almost always right. Rungs 5 to 7 need the gate below.

### 5. The pattern gate

Before introducing an interface, an abstraction, or a GoF pattern, answer
three questions:

- Is there a second concrete variant today, in the code or in a requirement
  you were given? An imagined one does not count. One implementation means
  no abstraction yet.
- Can you name the pattern's participants in domain terms (`PricingRule`,
  not `AbstractStrategy`)? If the names stay generic, the pattern is not
  modeling anything.
- Does the pattern's known trade-off apply here? Each entry in
  [references/patterns.md](references/patterns.md) lists it. Visitor makes
  adding element types expensive, Singleton is global state, Decorator
  stacks are hard to debug.

Three yeses, proceed. Otherwise take the simpler rung and note the seam you
would open when the second variant arrives.

### 6. Change in small, verified steps

One refactoring at a time, tests green after each, and each step committed
separately from any behavior change. A `refactor:` commit never changes
behavior; if behavior must change, that is a `fix:` or `feat:` commit first.
Small steps make a wrong turn cheap to undo and keep the diff reviewable.
Rewriting a module from scratch is the last resort, not the first, and needs
the user's say-so.

### 7. Report what you did and what you left alone

State the pain, the change, the trade-off you introduced, and the things you
deliberately did not touch and why. A reviewer should be able to disagree
with the trade-off, which means it has to be visible.

## Reviewing a design without changing it

Same diagnosis, different output. For each finding give the principle at
stake, why it applies here rather than in the abstract, the concrete cost of
leaving it, and the minimal fix. Rank by cost of change, not by how many
principles it touches. If the code is sound, say so. A review that invents
problems to fill space is worse than none.

## Designing new code

Start with the plainest structure that satisfies the requirement in front of
you. List the axes of change you actually know about, from the requirement,
the roadmap, or a second variant that already exists, and put seams only
there. Everything else gets refactored when the real need shows up, which is
cheaper than carrying speculative structure. Match ceremony to scale: a
script, a service, and a shared library deserve different amounts.

## Where principles pull against each other

Most bad refactors come from applying one principle without the one that
balances it.

- **DRY vs. KISS.** DRY is about knowledge, not text. Two blocks that encode
  the same decision should be one; two that look alike but change for
  different reasons should stay apart. Tolerate two copies, extract on the
  third.
- **OCP vs. YAGNI.** Open for extension is worth paying for once a second
  variant exists. Before that it is speculation.
- **SRP vs. cohesion.** A responsibility is defined by who asks for the
  change, not by splitting on nouns. Over-splitting yields anemic classes
  and a request that spans six files.
- **DIP vs. indirection.** Inject what you need to swap or fake in tests.
  An interface with one implementation forever is ceremony.
- **Composition vs. inheritance.** Inherit for a true is-a that also needs
  polymorphism. For reuse alone, compose.
- **Encapsulation vs. data.** Data-transfer objects legitimately have no
  behavior to hide. Objects with invariants do.

## Signs you have gone too far

- An interface or abstract class with exactly one implementation.
- Classes named Manager, Helper, Util, Service, or a bare pattern name with
  no domain word in front.
- The same behavior spread over more files than before, with no new seam
  actually used.
- A reader must open four files to follow one request.
- Tests had to be rewritten rather than re-run.

## References

Read the one that matches the task:

- [references/principles.md](references/principles.md): SOLID, the OOP
  pillars, DRY, KISS, YAGNI, naming, composition, Demeter, separation of
  concerns. Signals, trade-offs, and related concepts for each.
- [references/patterns.md](references/patterns.md): the 23 GoF patterns
  indexed by problem, with the trade-off and the simpler alternative for
  each.
- [references/code-smells.md](references/code-smells.md): the smell
  catalog, what each hints at, the refactoring that fixes it, and the
  thresholds the MCP detectors use.
- [references/refactorings.md](references/refactorings.md): the safe
  refactoring protocol and the core refactoring catalog with mechanics.
