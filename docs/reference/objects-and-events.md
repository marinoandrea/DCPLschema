# Objects & Events

At a fundamental level, DCPL relies on the common-sensical distinction of **objects** (-types) from **events** (-types), an ontological ground confirmed e.g. in LKIF-core[^1].

## Objects

Objects are persistent data within a DCPL program instance and they are used to represent [agents](#agents) and facts about the state of the program.
Everything that doesn’t actively change the system is an object.
All objects (including states) are denoted as literals (e.g. `user`).
Objects can be active or inactive, allowing them to also be used as boolean expressions in [rule conditions](rules.md#transformational-rules).

### Agents

An object becomes an agent when it is granted the ability to perform one or more actions (as discussed in [normative positions](positions.md)).
Agents will often act as specifiers, indicating the type or group of individuals that a frame attribute applies to.
A special type of agent is `*`, which represents all agents.

!!! example

    If a [duty](positions.md#deontic-positions) is held by the agent `teacher`, every agent that fulfils the `teacher` requirement has that [duty](positions.md#deontic-positions).

## Events

Events actively change the system (i.e. they represent a transition), and they are denoted with prefixed literals (e.g. `#borrow`).
A special case of transition events are those concerning the creation or removal of objects. For instance, the activation of a raining state is denoted with `+raining`, whereas its disactivation as `-raining`.

!!! tip

    When transient, events should rather be modeled as objects (e.g. `borrowing` or `raining`), to denote for their ongoing state.

## Refinements

Objects and events can have internal properties that can be specified or refined via the program.
For instance, an action (or action-type) could be specified further referring to the thematic roles of verbs used in linguistics (agent, patient, recipient, instrument, etc.).

For the description of internal components, DCPL follows a JSON-like syntax. Possible internal properties may be accessed as in OOP (e.g. `user.online`).

!!! example

    This is how we model that `person` objects should have a `spouse` property:

        person {
            spouse {}
        }

To reify the informational model, certain objects (e.g. the [normative positions](positions.md)) come with fixed parameters.

## Descriptors

Extensional semantics applied in formal languages are based on set-theory and consider
classes or types as sets of instances.
This means that they (theoretically) maintain access to an abstract data structure, used e.g. to enlist all instances, and then to check whether an instance belongs to it.
Rather than sets of objects centrally maintained, DCPL considers the association of several descriptors to each object.

As an analogy, consider how people generally keep in their own wallets id-cards, library cards, discount coupons, membership cards, etc.
These are examples of several “identity” certificates (unique or not) operational in different contexts, which have been issued by an authority, and of which no other copy may exist, except the one people keep with them.

DCPL aims to bring the qualification act in the foreground, and it does so by means of the `in` operator.

!!! example

    This is how we express that `card_holder` agents qualify as a `member`:

        card_holder in member

[^1]: Hoekstra, R., Breuker, J., Di Bello, M., Boer, A., et al.: The LKIF core ontology of basic legal concepts. LOAIT 321, 43–63 (2007)
