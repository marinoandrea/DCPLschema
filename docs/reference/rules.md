# Rules

DCPL considers the distinction of **transformational** and **reactive** rules, proceeding along LPS[^1].

## Transformational Rules

Transformational rules follow the template: `a => b`, meaning that if `a` holds, then `b` holds too.
Interpreting the “if” as “as long as”, the transformation aspect (of `a` into `b`) becomes explicit.

!!! example

    Let's define a very simplistic [deontic frame](positions.md#deontic-frames) for the concept of `borrowing` which assumes the agreement has already been finalized between some well-defined parties:

    ```
    duty {
        holder: borrower
        counterparty: lender
        action: #return_item { item: item }
        violation: now() > return_time
    } as d1
    ```

    We can define another simple rule that creates a [power](positions.md#power-frames) for the lender to `#fine` the `borrower` as long as `d1` is violated (i.e., the `borrower` has not performed `#return_item`).

    ```
    +d1.violation => +power {
        holder: d1.counterparty
        action: #fine {
            borrower: d1.holder
        }
        consequence: +duty {
            holder: d1.holder
            counterparty: d1.counterparty
            action: #pay { amount: 100.0 }
        }
    }
    ```

## Reactive Rules

Reactive rules in DCPL are in the form: `#f => #g`, meaning that the occurrence of a `#f` event triggers subsequently a `#g` event.
Causal effects become explicit with production events.

!!! example

    If it rains, the floor becomes wet.

        #rain => +wet

[^1]: Kowalski, R., Sadri, F.: _A logic-based framework for reactive systems_. In: International Workshop on Rules and Rule Markup Languages for the Semantic Web. pp. 1–15. Springer (2012)
