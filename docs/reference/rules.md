# Rules

DCPL considers the distinction of **transformational** and **reactive** rules, proceeding along LPS[^1].

## Transformational Rules

Transformational rules follow the template: `a -> b`, meaning that if `a` holds, then `b` holds too.
Interpreting the “if” as “as long as”, the transformation aspect (of `a` into `b`) becomes explicit.

!!! example

    As long as deontic frame `d1` is violated, the following power holds.

        +d1.violation => +power {
            holder: lender
            action: #fine
            consequence: +fine(borrower, lender)
        }

## Reactive Rules

Reactive rules in DCPL are in the form: `#f => #g`, meaning that the occurrence of a `#f` event triggers subsequently a `#g` event.
Causal effects become explicit with production events.

!!! example

    If it rains, the floor becomes wet.

        #rain => +wet

[^1]: Kowalski, R., Sadri, F.: _A logic-based framework for reactive systems_. In: International Workshop on Rules and Rule Markup Languages for the Semantic Web. pp. 1–15. Springer (2012)
