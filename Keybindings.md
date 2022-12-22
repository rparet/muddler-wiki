# Keybindings

For the most part, you will describe keys the way you think you would, with a `+` between the portions of the key. However, a capital K is not `K` it is `shift+k` because the keybindings are not reading characters, but the actual keypress events. I have tried to include logic in muddler to automagically fix this for you, but if the keybinding isn't exporting how you expect this might be why.
Please see the Keycodes page on the right sidebar for more information on describing your keybindings.

As with all things muddler, the key binding objects are described using a json file. Note that while you can include lua code in the script key, this is not really how it is meant  to be used. It can be handy for short snippets you don't want to make a new file for though.

## keys.json

```jsonc
[
  {
    "name": "code", // the name of the key object being created
    "isActive": "yes", // is this active? Defaults to yes
    "command": "score", // raw command to send to the mud. Same as typing in the command field in the Key editor in Mudlet
    "keys": "shift+ctrl+g", // the key chord you want to bind to. See sidebar labeled "Keycodes" for more info
    "script": "return true" // you can put lua code in here if you really want. Defaults to reading $name.lua from same folder
  },
  {
  // You would describe the next keybinding in this folder here.
  }
]
```
