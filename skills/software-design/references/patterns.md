# Gang of Four patterns

A pattern is a named solution to a recurring design problem, and the name is
useful mainly for communicating. Reach for one when its problem is present in
the code today, name its participants in domain terms, and accept its
trade-off knowingly. The slug in each entry is what the `software-design`
MCP's `explain_concept` and `scaffold_pattern` tools accept; `scaffold_pattern`
takes a `names` map to rename roles to your domain, such as
`{ "Product": "Notification" }`.

## Find the pattern by the problem

| the problem in front of you                                            | pattern                 | simpler first                                      |
| ---------------------------------------------------------------------- | ----------------------- | -------------------------------------------------- |
| One thing done several interchangeable ways, chosen at runtime         | Strategy                | A function parameter or a map of functions         |
| Behavior depends on a state with clear transitions                     | State                   | A switch, while there are two trivial states       |
| An algorithm's skeleton is shared, a few steps vary                    | Template Method         | Strategy, if the steps are better composed         |
| One change must notify an unknown number of dependents                 | Observer                | A direct call, while there is one listener         |
| Requests need undo, queuing, logging, or scheduling                    | Command                 | A closure, if there is no undo                     |
| Several objects might handle a request; the handler isn't known upfront | Chain of Responsibility | An ordered list of predicates                      |
| Objects reference each other in a tangled web                          | Mediator                | Observer, if the interaction is one-directional    |
| Undo or checkpoints of an object's state                               | Memento                 | Command with inverse operations                    |
| A stable set of element types needs many unrelated operations          | Visitor                 | A method per operation, if the element set changes |
| A small, stable grammar recurs                                         | Interpreter             | A real parser, if the grammar grows                |
| Walk a collection without exposing its shape                           | Iterator                | The language's built-in iteration protocol         |
| An existing class has the wrong interface for its new client           | Adapter                 | Change the client, if you own it                   |
| Add responsibilities to an object at runtime, stackable                | Decorator               | A flag, while there is one responsibility          |
| One simple entry point over a complex subsystem                        | Facade                  | A well-named function                              |
| Control access: lazy loading, caching, permissions, remoting           | Proxy                   | Do it in the caller, if there is one caller        |
| Behavior varies along two independent dimensions                      | Bridge                  | Strategy, if only one dimension really varies      |
| Part-whole trees treated uniformly                                     | Composite               | A list, if there is no nesting                     |
| Huge numbers of similar objects strain memory                         | Flyweight               | Measure first; it is a performance pattern         |
| The concrete type to create isn't known by the creator                 | Factory Method          | A constructor, or a plain function returning the type |
| Families of related objects must stay consistent                       | Abstract Factory        | Factory Method, if there is one product            |
| Construct a complex object step by step; many optional parts           | Builder                 | Named or keyword arguments, if the language has them |
| Creation is expensive and copies are near-identical                    | Prototype               | A constructor with defaults                        |
| Exactly one instance must coordinate a shared resource                 | Singleton               | Create one instance and pass it in                 |

## Creational

### Factory Method (`factory-method`)
Let subclasses decide which concrete product to instantiate, so the creator
depends only on the product abstraction. Use when a class can't anticipate
the concrete type it must create. Trade-off: a parallel Creator hierarchy
just to vary one instantiation. Related: Abstract Factory, Template Method,
OCP.

### Abstract Factory (`abstract-factory`, "kit")
Create whole families of related products without naming their classes, so a
client switches the entire family (a UI theme) by swapping one factory. Use
when products come in interchangeable families that must stay consistent.
Trade-off: adding a new product kind means changing every factory interface
and implementation. Related: Factory Method, Singleton, DIP.

### Builder (`builder`)
Construct a complex object step by step, separating construction from
representation and avoiding telescoping constructors. Use when an object has
many optional parts or must be built in stages. Trade-off: more moving parts
than a constructor for simple objects. Related: Abstract Factory, KISS.

### Prototype (`prototype`, "clone")
Create new objects by cloning a pre-built instance when construction is
expensive or the concrete class is decided at runtime. Trade-off: deep versus
shallow copy of object graphs is subtle and easy to get wrong. Related:
Abstract Factory, Memento.

### Singleton (`singleton`)
Ensure a class has exactly one instance with a global access point. Use only
when one instance must coordinate access to a shared resource. Trade-off:
hidden global state, hard to test, thread-safety pitfalls; often an
anti-pattern. Prefer creating one instance and passing it in. Related:
Abstract Factory, DIP.

## Structural

### Adapter (`adapter`, "wrapper")
Make an incompatible interface fit the one a client expects, without changing
either side. Use to reuse a class whose interface doesn't match, or to put a
third-party or legacy API behind your own contract. Trade-off: another layer
to trace; overuse hides a mismatched design. Related: Facade, Decorator,
Bridge.

### Bridge (`bridge`)
Split an abstraction from its implementation so both vary independently by
composition, avoiding an M by N subclass explosion. Use when behavior varies
along two independent dimensions (shape and renderer). Trade-off: up-front
indirection; overkill when only one dimension varies. Related: Abstract
Factory, Adapter, composition over inheritance.

### Composite (`composite`)
Treat individual objects and compositions uniformly in part-whole trees, so
clients handle a leaf and a group through the same interface. Use for
files and folders, UI trees, expression trees. Trade-off: a uniform
interface makes leaf-invalid operations such as add and remove awkward.
Related: Decorator, Iterator, Visitor.

### Decorator (`decorator`, "wrapper")
Add responsibilities to an object dynamically by wrapping it in another with
the same interface; a flexible alternative to subclassing. Use when behaviors
should be stackable and chosen at runtime. Trade-off: many small wrappers;
deep stacks are hard to debug and identity-sensitive. Related: Composite,
Adapter, composition over inheritance, OCP.

### Facade (`facade`)
Provide one simple entry point over a complex subsystem, leaving the
internals reachable for advanced use. Use when most clients want one common
path and shouldn't know many internal classes. Trade-off: becomes a god
object if it accretes logic instead of delegating. Related: Adapter,
Mediator, separation of concerns, Law of Demeter.

### Flyweight (`flyweight`)
Share fine-grained objects by splitting state into shared intrinsic state and
passed-in extrinsic state, so huge numbers of objects cost little memory. Use
for glyphs, tiles, particles. Trade-off: trades CPU for memory and adds real
complexity; measure before applying. Related: Factory Method, Singleton,
Composite.

### Proxy (`proxy`)
Stand in for another object with the same interface to add access control,
lazy loading, caching, remoting, or logging without changing the real subject
or its clients. Trade-off: extra indirection and possible latency; can hide
surprising behavior. Related: Decorator, Adapter, Facade.

## Behavioral

### Chain of Responsibility (`chain-of-responsibility`, "cor")
Pass a request along a chain until a handler deals with it, decoupling sender
from receiver. Use for middleware, event handling, approval pipelines.
Trade-off: no guarantee a request is handled; chains are hard to trace.
Related: Command, Composite, Decorator.

### Command (`command`, "action")
Turn a request into a first-class object so it can be parameterized, queued,
logged, and undone. Use when you need undo and redo, scheduling, or logging
of operations. Trade-off: a class per operation; direct calls don't need it.
Related: Chain of Responsibility, Memento, Strategy.

### Interpreter (`interpreter`)
Represent a small language's grammar as classes and evaluate sentences by
walking the tree. Use for simple, stable grammars such as filters and rules.
Trade-off: a class per rule doesn't scale; use a real parser for anything
complex. Related: Composite, Visitor.

### Iterator (`iterator`, "cursor")
Traverse a collection without exposing its representation. Trade-off: most
languages provide iterators natively; rarely hand-rolled today. Related:
Composite, Factory Method.

### Mediator (`mediator`, "controller")
Centralize how a set of objects interact: colleagues talk to the mediator,
not to each other. Use when objects reference each other in a tangled web
(form widgets) or interaction logic is scattered. Trade-off: the mediator
can swell into a god object. Related: Facade, Observer, Command.

### Memento (`memento`, "snapshot")
Capture and restore an object's state through an opaque snapshot without
breaking encapsulation. Use for undo, rollback, checkpoints. Trade-off:
snapshots can be memory-heavy; deciding what to save is fiddly. Related:
Command, Prototype.

### Observer (`observer`, "pubsub")
Notify many dependents automatically when a subject changes; the backbone of
event and reactive systems. Use when one change must update an unknown number
of others with loose coupling. Trade-off: update storms, ordering surprises,
leaks from observers that never detach. Related: Mediator, State, separation
of concerns.

### State (`state`)
Let an object change its behavior when its internal state changes by
delegating to a state object, replacing sprawling state conditionals. Use
when transitions form a clear machine. Trade-off: a class per state;
overkill for two trivial states. Related: Strategy, Observer.

### Strategy (`strategy`, "policy")
Make a family of algorithms interchangeable at runtime behind a common
interface; the go-to way to satisfy OCP for varying behavior. Use when there
are several ways to do one thing (sorting, pricing, routing) and you would
otherwise select with a conditional. Trade-off: clients must know the
strategies to choose; extra objects for trivial cases. In languages with
first-class functions a Strategy is often just a function. Related: State,
OCP, DIP, Command.

### Template Method (`template-method`)
Fix an algorithm's skeleton in a base method and let subclasses fill in the
steps. Use when several algorithms share a structure but differ in a few
steps and the order must not change. Trade-off: inheritance-based, so less
flexible than Strategy's composition. Related: Strategy, Factory Method,
inheritance.

### Visitor (`visitor`)
Add operations to an object structure without changing its classes, by
double dispatch. Use when a stable set of element types needs many unrelated
operations that should live together. Trade-off: adding a new element type
forces changing every visitor; double-dispatch boilerplate; weakens element
encapsulation. Related: Composite, Interpreter, Iterator.
