# TODO

- implement POSTPONE, COMPILE
- debugger
- see - decompiler
- separate buffer used by tokenizer from wordBuf and wordBuf2
- change boolean to 0, \$FFFF
- implement most of open firmware forth
- update manual with string examples
- save last line buffer
- simple editor
- replace Z80 routines with more Forth ones

## Ideas

- formal arguments, proposed syntax `( a b c -- d e )`, named return results
- mark & restore `here`
- look into `forget`, or `mark`

if nesting > 0 then create would cause an error because of collision with memory use
only matters for dictionary items
solutions:

- error, :, variable, constant etc must defined at top level
- defer immediate words... complex
- change memory use

## words to add

```
recursive
recurse
```
