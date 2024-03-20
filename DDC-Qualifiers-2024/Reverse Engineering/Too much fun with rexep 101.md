# Too much fun with rexep 101

Proposed difficulty: `Medium`

27 solves

```text
I'm having fun with regular expressions. Here I played a bit with features like capture groups, lookahead and look behind.

/(?=^(.{4}([2-8CFGLNPRUXY\-]+).)$)(?=^([^5]*)$)(?=DDC\{)(?=.*4LLY)(?=.*C4)(?=.{4}(?<RE>..).{13}(?:\k<RE>))(?=.*P\d)(?=^.{4}[23GPRX]{7}-)(?=^([^3]*3){4}[^3]*$)(?=^.{5}(?<D>\d).{1}(?:\k<D>).{9}(?:\k<D>).{2}(?:\k<D>))(?=.*-(?!=4)([4CN]{3})(?<!\d)-)(?=^[^\-]{11}(?<=2)-[^\-]{3}(?<=N)-83-[34LRY]{6}-FUN)(?=.+R.G.X)(?=.*(?<=N)}$)(?=.*(?<=R)3){2}(?=.*(-..N)){2}^.{30}$/gm

Can you find a string that matches?
```

# Solution

This regex is a bit fun, as almost every part of the query uses positive lookahead.

This means that the regex is non-consuming. Therefore we can split it up into individual regex expressions, and try to find something that matches all of them.

The regex queries can be split up like this:

```re
^(.{4}([2-8CFGLNPRUXY\-]+).)$
^([^5]*)$
DDC\{
.*4LLY
.*C4
.{4}(?<RE>..).{13}(?:\k<RE>)
.*P\d
^.{4}[23GPRX]{7}-
^([^3]*3){4}[^3]*$
^.{5}(?<D>\d).{1}(?:\k<D>).{9}(?:\k<D>).{2}(?:\k<D>)
.*-(?!=4)([4CN]{3})(?<!\d)-
^[^\-]{11}(?<=2)-[^\-]{3}(?<=N)-83-[34LRY]{6}-FUN
.+R.G.X
.*(?<=N)}$
(.*(?<=R)3){2}
(.*(-..N)){2}
^.{30}$
```

I just sat down and looked at each query and saw how it could contribute to what I had at the moment.

# Flag

`DDC{R3G3XP2-C4N-83-R34LLY-FUN}`
