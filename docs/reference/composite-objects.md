# Composite Objects

More complex institutional concepts (e.g. ownership) can be described as a
composition of primitive ones.
DPCL allows for combining the objects above into reusable, parameterizable compounds.

!!! example

    Here we define the concept of _borrowing_:

        borrowing(lender, borrower, item, timeout) {
            power {
                holder: lender
                action: #request_return
                consequence: +duty {
                    holder: borrower
                    counterparty: lender
                    action: #return { item: book }
                }
            }
            duty d1 {
                holder: borrower
                counterparty: lender
                action: #return { item: book }
                violation: now() > timeout
            }
            +d1.violation => +power {
                holder: lender
                action: #fine
                consequence: +fine(borrower, lender)
            }
        }

!!! note

    If there is nothing else than duty and power frames in compounds, the institutional/non-institutional knowledge decomposition is evident. We could in principle use any other means (ontologies, logic programs, etc.) for the domain and/or terminological knowledge.
