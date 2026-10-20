# Principles

Each entry gives the slug the `software-design` MCP's `explain_concept` tool
accepts, the one-line idea, the signals that it applies, and the trade-off
that stops you over-applying it. The trade-off is not a footnote: every
principle below has a balancing principle, and most bad refactors apply one
without the other.

## SOLID

### Single Responsibility (`single-responsibility`, "srp")

A module should have one reason to change. Each class or module answers to
one actor or concern; when unrelated responsibilities share a class, a change
requested by one stakeholder risks breaking another's feature.

- Signals: a class mixes concerns (business rules plus persistence plus
  formatting); you keep editing the same file for unrelated reasons; a
  method name needs an "and".
- Trade-off: "responsibility" is a judgment call, defined by who requests the
  change, not by nouns. Over-splitting creates anemic, scattered classes and
  navigation overhead. Split along the axes of change.
- Related: separation of concerns, OCP, Facade.

### Open/Closed (`open-closed`, "ocp")

Open for extension, closed for modification. New behavior should be added as
a new implementation of an abstraction, not as another branch in a growing
switch over tested code.

- Signals: a switch or if-else grows a new arm every time a variant is
  added; new behavior could be a new implementation of an existing interface.
- Trade-off: abstracting for variation that never comes is speculative
  (YAGNI). Indirection makes the straight-line path harder to read. Apply
  once the second variant exists.
- Related: Strategy, DIP, YAGNI, Factory Method.

### Liskov Substitution (`liskov-substitution`, "lsp")

Subtypes must be usable anywhere their base type is expected: no strengthened
preconditions, no weakened postconditions, no surprising exceptions. If
callers must check the concrete type, the hierarchy is broken.

- Signals: a subclass overrides a method to throw "not supported"; callers
  use `instanceof` to special-case a subtype; Square extends Rectangle.
- Trade-off: sometimes composition beats inheritance. Don't force an is-a
  that isn't.
- Related: composition over inheritance, ISP, polymorphism.

### Interface Segregation (`interface-segregation`, "isp")

Don't force clients to depend on methods they don't use. Prefer several
small, role-specific interfaces over one fat one, so a change to an unrelated
method can't ripple into a client that never called it.

- Signals: implementers stub methods they don't need; one interface serves
  several unrelated client roles.
- Trade-off: too many micro-interfaces is ceremony. Balance cohesion against
  granularity.
- Related: SRP, DIP.

### Dependency Inversion (`dependency-inversion`, "dip")

Depend on abstractions, not concretions. High-level policy shouldn't import
low-level detail; both depend on an abstraction, and the detail (a specific
database, HTTP client, clock) is injected and swappable.

- Signals: business logic imports a concrete database, HTTP, or file class
  directly; you want to unit-test policy without the real dependency.
- Trade-off: an interface with exactly one implementation forever is
  needless indirection. The principle needs no DI container; passing a
  collaborator into a constructor is enough.
- Related: OCP, Strategy, Abstract Factory, abstraction.

## OOP pillars

### Encapsulation (`encapsulation`, "information-hiding")

Bundle state with behavior and hide the internals. Expose behavior, not data;
mutate fields only through methods that preserve invariants.

- Signals: public setters let callers put an object into an invalid state;
  an invariant is enforced at many call sites instead of by one owner.
- Trade-off: plain data-transfer objects legitimately have no behavior to
  hide.
- Related: abstraction, SRP, Law of Demeter.

### Abstraction (`abstraction`)

Expose the essential contract; hide the mechanism. Callers program to what a
thing does, not how, and stay insulated from implementation churn.

- Signals: callers need one concept but several interchangeable
  implementations; you want to defer or swap a mechanism such as storage or
  transport.
- Trade-off: leaky or premature abstractions add cost without insulation.
- Related: encapsulation, DIP, polymorphism.

### Inheritance (`inheritance`)

Derive a specialized type from a general one. Powerful for genuine is-a
relationships that also need polymorphism; easily abused as a code-reuse
shortcut.

- Signals: a true is-a where subtypes are substitutable; you want polymorphic
  dispatch over a family of types.
- Trade-off: deep hierarchies are fragile and rigid. Reuse alone never
  justifies inheritance; compose instead.
- Related: polymorphism, LSP, composition over inheritance.

### Polymorphism (`polymorphism`)

One interface, many interchangeable implementations. Callers invoke an
operation on an abstraction and the right concrete behavior runs. It is the
mechanism behind OCP, Strategy, and DIP.

- Signals: behavior varies by a type or kind and you would otherwise switch
  on it; you want to add variants without touching callers.
- Trade-off: dispatch you can't see obscures control flow. Keep the set of
  implementations discoverable.
- Related: OCP, Strategy, abstraction, inheritance.

## General guidelines

### DRY (`dry`)

Every piece of knowledge has one authoritative representation. DRY is about
knowledge, not text: two identical-looking blocks that encode the same
decision should be unified; two that merely look alike but change for
different reasons should stay apart.

- Signals: the same business rule is copy-pasted and must be edited in
  lockstep; a magic value is duplicated across the codebase.
- Trade-off: coupling unrelated code because it looks similar is worse than
  duplication. Rule of three: tolerate two copies, extract on the third.
- Related: SRP, KISS.

### KISS (`kiss`)

Prefer the simplest design that solves the problem. Complexity is a cost
paid on every future read; favor boring solutions and add structure only when
a concrete need justifies it.

- Signals: layers or indirection with no present-day payoff; a one-liner
  replaced by a framework.
- Trade-off: simple is not the same as easy. Real simplicity sometimes takes
  design effort.
- Related: YAGNI, DRY.

### YAGNI (`yagni`)

Don't build for requirements you don't have yet. Speculative generality is
inventory you pay to carry: more to read, test, and maintain for a future
that may never arrive.

- Signals: config flags, hooks, or abstractions added "just in case";
  generalizing before a second concrete use case exists.
- Trade-off: not a license to ignore obvious, cheap seams that keep options
  open.
- Related: KISS, OCP.

### Meaningful names (`meaningful-names`)

Names should reveal intent; reach for a word before a comment. A name is the
first documentation a reader meets.

- Signals: a variable needs a comment to explain what it holds; you reach for
  `data`, `tmp`, `mgr`, `x2`; a single-letter name is used far from where it
  was declared.
- Trade-off: over-long names hurt as much as cryptic ones. Conventional short
  names beat forced verbosity (`i` in a loop, `e` for an event). Keep
  established public API names stable.
- Related: KISS, SRP.

### Clean code (`clean-code`)

Code is read far more than it is written; optimize for the reader. An
umbrella habit rather than a rule: intention-revealing names, small
single-purpose units, no duplication, the simplest thing that works.

- Signals: you hesitate to touch a file because you don't understand it;
  reviewers keep asking "what does this do?"; reading a change takes longer
  than making it.
- Trade-off: "clean" is contextual; a throwaway script and a core library
  deserve different bars. Endless renaming and extracting has diminishing
  returns. Clean code can still be wrong; tests decide behavior.
- Related: meaningful names, SRP, DRY, KISS.

### Composition over inheritance (`composition-over-inheritance`)

Assemble behavior from parts rather than inheriting it. Inheritance is tight,
compile-time coupling to a base class's internals; composing objects that
delegate to collaborators is more flexible and lets behavior change at
runtime.

- Signals: a hierarchy is deep or exists only to share code; behavior must
  vary along more than one axis.
- Trade-off: more small objects and wiring. Inheritance is fine for a true
  is-a plus polymorphism.
- Related: Strategy, Decorator, LSP.

### Law of Demeter (`law-of-demeter`, "lod")

Talk to friends, not strangers. A method should only call methods of itself,
its parameters, objects it creates, and its direct fields. Chains like
`a.getB().getC().doThing()` couple you to a structure two objects away.

- Signals: train-wreck chains; a change to a distant class breaks callers
  that never named it.
- Trade-off: fluent builders and pipelines are legitimate chains. Target
  reaching through objects, not fluency.
- Related: Facade, SRP.

### Separation of concerns (`separation-of-concerns`, "soc")

Keep distinct concerns in distinct places: UI, domain, persistence,
transport. The macro-scale sibling of SRP.

- Signals: presentation logic entangled with business rules or SQL; you want
  layers that can be tested and swapped independently.
- Trade-off: too many layers for a tiny app is ceremony. Match structure to
  scale.
- Related: SRP, Facade.
