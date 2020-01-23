# TODO, Plans, ideas etc

- `word` to write at location pointed to by `here`
- test `state === 0` interpret else compile,
- allows recursive compilation of quotations
- definitions `:` and `;` should set `state` to 1 and 0 respectively
- recusive quotation, proposed syntax `:(` `):`,
- rather than Joy's square brackets `[` `]` which are already used in Forth for other things
- in interpret mode strings and quotations write to `here` pointer,
- overwriting the previous top level string or quotation declaration?
- item can be protected from overwriting with `preserve` which moves here to end.
- look into `forget`, or `mark`
- in interpret, `here` is stored and items get added and `here` moves.
- a blank line moves `here` back to last colon word definition.
- a colon definition moves `here` back as well.
