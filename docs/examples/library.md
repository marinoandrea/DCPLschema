# Library Regulation

Library regulation is a classic example used in the normative systems literature, and we will use it here as well to illustrate the main features of the language.

- Student or staff can register as member of the library by using their id card (example of act creating a new qualification).

```
power {
    holder: student | staff
    action: #register { instrument: holder.id_card }
    consequence: holder in member
}
```

- Any member can borrow a book for at max 1 month (act creating a composite object).

```
power {
    holder: member
    action: #borrow { item: book }
    consequence: +borrowing {
        lender: library
        borrower: holder
        item: book
        timeout: now() + 1m
    }
}
```

- By borrowing, the borrower can be requested in any moment to return the item.
  The borrower has the duty to return the item within the given date.
  If the borrower does not return the item, they may be fined (example of composite object, with power, duty and violation constructs).

```
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
```
