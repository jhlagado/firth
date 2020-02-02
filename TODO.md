# TODO

- add `nested` numeric variable for deferring interpretation in cli,
- recusive quotation, proposed syntax `{` `}`,
- formal arguments, proposed syntax `( a b c -- d e )`, named return results
- debugger
- see
- implement most of open firmware forth
- separate buffer used by tokenizer from wordBuf and wordBuf2
- update manual with string examples
- fix .s which currently crashes
- change boolean to 0, \$FFFF
- use restart instead of `call enter`?

## Ideas

- mark & restore `here`
- look into protecting from overwriting with `preserve` which moves here to end.
- look into `forget`, or `mark`

if nesting > 0 then create would cause an error because of collision with memory use
only matters for dictionary items
solutions:

- error, :, variable, constant etc must defined at top level
- defer immediate words... complex
- change memory use
