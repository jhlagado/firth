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
bl
+ ( n1 n2 -- n1+n2 )
- ( n1 n2 -- n1-n2 )
* ( n1 n2 -- n1*n2 ) \ Signed multiplication
/ ( n1 n2 -- n1/n2 ) \ Signed division
mod ( n1 n2 -- n1%n2 )
/mod ( n1 n2 -- n1%n2 n1/n2 )
and ( n1 n2 -- n1&n2 )
or ( n1 n2 -- n1|n2 )
xor ( n1 n2 -- n1^n2 )
lshift ( n1 n2 -- n1<<n2 )
<< ( n1 n2 -- n1<<n2 ) \ Synonym for lshift
rshift ( n1 n2 -- n1>>n2 ) \ Zero-extends
>> ( n1 n2 -- n1>>n2 ) \ Synonym for rshift
>>a ( n1 n2 -- n1>>n2 ) \ Sign-extends
abs ( n -- u ) \ Absolute value
max ( n1 n2 -- max ) \ Signed maximum
min ( n1 n2 -- min ) \ Signed minimum
umax ( u1 u2 -- max ) \ Unsigned maximum
umin ( u1 u2 -- min ) \ Unsigned minimum
invert ( n -- ~n ) \ Bitwise logical inversion
negate ( n -- -n ) \ Arithmetic negation, i.e. 0-n
1- ( n -- n-1 ) \ Decrement by 1
1+ ( n -- n+1 ) \ Increment by 1
2/ ( n -- n/2 ) \ Divide by 2
2* ( n -- n*2 ) \ Multiply by 2

dup ( a -- a a )
drop ( a -- )
swap ( a b -- b a )
over ( a b -- a b a )

recursive
recurse

l@ ( adr -- l ) \ Fetch a 32-bit value
l! ( l adr -- ) \ Store a 32-bit value
w@ ( adr -- w ) \ Fetch a 16-bit value
<w@ ( adr -- w ) \ Fetch a 16-bit value, sign extending
w! ( w adr -- ) \ Store a 16-bit value
c@ ( adr -- b ) \ Fetch a 8-bit value
c! ( b adr -- ) \ Store a 8-bit value

, ( n -- ) \ Allocate data space for a number and put n there
c, ( b -- ) \ Allocate data space for a byte and put b there
w, ( w -- ) \ Allocate data space for 16 bits and put w there
l, ( w -- ) \ Allocate data space for 32 bits and put l there
allot ( n -- ) \ Allocate data space for n bytes (uninitialized)

na+ ( adr n -- adr' ) \ Add n times the size of a number to adr
ca+ ( adr n -- adr' ) \ Add n times the size of a byte to adr
wa+ ( adr n -- adr' ) \ Add n times the size of a 16-bit word to adr
la+ ( adr n -- adr' ) \ Add n times the size of a 32-bit longword to adr

h#
d#
```
