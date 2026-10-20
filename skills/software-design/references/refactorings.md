# Refactorings

Refactoring is changing the structure of code without changing its
observable behavior, in steps small enough that each one is obviously safe.
The names below follow Martin Fowler's catalog so that a reviewer, a commit
message, and the code agree on what happened.

## The protocol

1. **Confirm the net.** Run the tests. If the code you will touch is not
   covered, write characterization tests first: call it with representative
   inputs, including edge cases and the odd behavior you suspect is a bug,
   and assert the current outputs. You are pinning behavior, not approving
   it. Note any bug you find for a separate `fix:` commit.
2. **One refactoring at a time.** Pick one from the catalog, apply its
   mechanics, run the tests. Green means commit (a `refactor:` commit); red
   means revert that step, not debug it. A step that is hard to revert was
   too big.
3. **Never mix a behavior change in.** If a refactoring reveals a bug or a
   missing feature, finish the refactoring, commit, then make the behavior
   change in its own commit. A reviewer reading a `refactor:` diff should be
   able to check it by eye.
4. **Let the IDE or language server do the mechanical part** where it can:
   rename, extract, move, change signature. Tool-driven refactorings keep
   references consistent across files.
5. **Stop when the pain is gone.** The goal is the sentence you wrote at
   the start, not a perfect score. Leave a note about what you saw but did
   not change.

## The catalog

### Composing functions

**Extract Function.** A block has a clear purpose, or a comment describes
it. Move the block into a new function named for what it does, not how, and
replace the block with a call. Pass what it reads as parameters; if it
needs more than four, extract a smaller piece or introduce a parameter
object. Reverse: Inline Function, when the body is as clear as the name.

**Extract Variable.** An expression is hard to read. Name its parts.
Reverse: Inline Variable, when the name adds nothing.

**Replace Temp with Query.** A local variable holds a computed value that
other functions could use. Turn it into a function so the computation has
one home.

**Split Phase.** One function reads input, computes, and formats output. Cut
it at the boundaries so each phase can be tested and changed alone.

**Replace Loop with Pipeline.** A loop filters, maps, and accumulates at
once. Express it as map, filter, and reduce so each step is visible. Skip it
when the loop is simpler than the pipeline.

**Remove Dead Code.** Unreachable branches, unused parameters, commented-out
blocks. Version control remembers them.

### Simplifying conditionals

**Decompose Conditional.** A long `if` condition and its branches. Extract
the condition and each branch into named functions, so the shape reads as a
sentence.

**Replace Nested Conditional with Guard Clauses.** Error and edge cases are
nested around the happy path. Check them first and return early, so the
main path runs unindented.

**Consolidate Conditional Expression.** Several checks produce the same
result. Combine them into one named condition.

**Replace Conditional with Polymorphism.** The same switch over a type code
recurs in several places. Make each case a type with the behavior as a
method; the switch becomes a dispatch. Needs at least two real variants and
a reason to expect more. Strategy is the version that uses composition
instead of subclasses.

**Introduce Special Case.** Many callers check for null or a sentinel and do
the same thing. Create an object for the special case that carries that
behavior (the Null Object pattern).

### Moving features

**Move Function.** A function uses another module's data more than its
own (feature envy). Move it next to the data it uses.

**Move Field.** A field is used mostly by another class. Move it, then move
the behavior that follows it.

**Extract Class.** A class does two jobs for two actors. Create a new class
for one job, move its fields and functions there, and let the original hold
a reference. Split along the axis of change, not by noun.

**Inline Class.** A class does too little to justify its existence. Fold it
into its one user.

**Hide Delegate.** Callers reach through an object to one of its parts.
Add a method on the object that does what they wanted, so the chain
disappears. Reverse: Remove Middle Man, when an object does nothing but
delegate.

### Organizing data

**Encapsulate Variable.** A field is read and written from many places. Put
access behind functions so an invariant can be enforced in one spot.

**Encapsulate Collection.** A getter returns a mutable list. Return a copy or
a read-only view and add add and remove methods.

**Replace Magic Literal.** A bare number or string carries meaning. Name it.

**Replace Primitive with Object.** A string or number that is really a
concept with rules (money, email, phone). Give it a type that validates once.

**Introduce Parameter Object.** Several parameters always travel together.
Group them into one object and give that object the behavior that uses
them.

### Dealing with inheritance

**Pull Up Method and Field.** Siblings share identical members. Move them to
the parent. Reverse: Push Down, when only one subclass uses them.

**Extract Superclass.** Two classes share behavior but no parent. Create one,
pull the shared parts up. Only when the is-a holds for both.

**Collapse Hierarchy.** A subclass and its parent have become
indistinguishable. Merge them.

**Replace Inheritance with Delegation.** A subclass uses only part of its
parent or breaks its contract (refused bequest). Hold the parent as a field
and forward the calls you need.

**Replace Type Code with Subclasses.** A field holds a kind that drives
behavior. Make each kind a subclass, then Replace Conditional with
Polymorphism. If the kind can change at runtime, use State or Strategy
instead.

**Extract Interface.** Two implementations exist and a client should depend
on neither. Name the operations the client uses, no more. Not before the
second implementation exists.

### Changing interfaces

**Rename.** The cheapest refactoring and the most skipped. A name should say
what the thing is or does; rename variables, functions, and classes as soon
as you understand them better. Keep public API names stable or deprecate
them with a forwarding alias.

**Change Function Declaration.** Add, remove, or reorder parameters. Do it
in steps when there are many callers: add the new signature, migrate
callers, remove the old one.

**Preserve Whole Object.** A caller extracts three fields from an object to
pass them. Pass the object.

**Separate Query from Modifier.** A function returns a value and changes
state. Split it so readers can call the query freely.

**Replace Parameter with Query.** A parameter can be derived from another.
Let the function derive it.

## Order of operations for a typical cleanup

Rename what you now understand, extract the pieces that have names, flatten
the conditionals, move things next to their data, and only then look at
whether a class or an abstraction wants to exist. Doing it in this order
means the bigger structural moves act on code that is already readable, and
often they turn out not to be needed.
