# Describing your keybindings in muddler

For the most part, keybinding descriptions work how you would expect them to. You provide a string which is your modifier keys and the key they modify, glued together with the `+` character. Thus, "shift+ctrl+v" is a valid description for a keybinding. There are a few things of note.

## "+"

Since we're using the + character as the delimiter for our keys, if you need to include the + key, just use the word "plus". Muddler will handle it from there.

## Capital letters, and the shift key

So mudlet's keybindings work against keypress events, not reading the eventual character produced. This means that in order to bind to, say, a capital `K` you should use `shift+k`. As a result you're best sticking to lowercase for your keybinding definitions; however, I try to handle this with logic in muddler.

## The keypad

There is probably still some work to do around the keypad. I need to figure out exactly how these keys register so I can document them, preferably with pictures. The numlock key acts like a capslock for the number pad. By this I mean, if numlock is on, the center key in the keypad is 5. But if it is not on, then the center key is defined as `clear`. This is important for keybindings, because as noted above `K` is not defined in keybindings as `K` but as `shift+k`. For the number pad though it means that `shift+keypad+5` would be holding shift and hitting the keypad 5 when numlock was **off**. Likewise, `shift+keypad+clear` would be the same key combination when numlock is on.

## The 'F' keys.(F1, F2, etc)

I only deal with these up  to F18. If that becomes a problem we will revisit it.

## Named keys

The named keys muddler currently understands are:

* 'shift'
* 'ctrl'
* 'alt'
* 'keypad'
* 'minus'
* 'asterisk'
* 'slash'
* 'plus'
* 'backspace'
* 'return'
* 'enter'
* 'ins'
* 'del'
* 'print'
* 'clear'
* 'home'
* 'end'
* 'left'
* 'up'
* 'right'
* 'down'
* 'pgup'
* 'pgdn'
* 'caps'
* 'num'
* 'scroll'
