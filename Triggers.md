# Triggers

As with all things muddler, the trigger objects are described using a json file. Note that while you can include lua code in the script key, this is not really how it is meant  to be used. It can be handy for short snippets you don't want to make a new file for though.

The `\` character in json strings is the escape character, so for regex and the like you need to escape it in order to get the actual backslash. IE `^You have (\d+) coins\.$` would be `"^You have (\\d+) coins\\.$"` in your json file.

## triggers.json

```jsonc
[
  {
    "name": "Prompt", // the name of the trigger object being created
    "isActive": "yes", // is this active? Defaults to yes
    "isFolder": "no", // is this a folder? Defaults to no. Set to yes if you want to define a trigger folder with no patterns in triggers.json
    "command": "score", // send a command when the trigger fires. like putting something in command in the editor
    "multiline": "no", // same as checking the AND / Multi-line (delta) checkbox. Defaults to no
    "multielineDelta": "0", // Integer, same as the box beneath the multiline checkbox
    "matchall": "no", // Like checking the "match all" checkbox. Defaults to no
    "filter": "no", // Like checking "only pass matches" in the trigger editor. defaults to no.
    "fireLength": "0", // Integer, like the fire length spinner in the trigger editor. Default to 0
    "soundFile": "C:\\sound.wav", // full path to the sound file. Like checking "play sound" and picking a file. Note the double \
    "highlight": "no", // Like checking "highlight" in the editor. Defaults to no
    "highlightFG": "#ff0000", // Hex value for foreground color to highlight. Ignored if highlight is not turned on. 
    "highlightBG": "#ffff00", // Hex value for background color to highlight. Ignored if highlight is not turned on.
    "patterns": [ // the list of trigger patterns as maps containing two keys, "pattern" and "type" valid types are
      {
        "pattern": "(New York):", // The actual pattern. This is used for regex and all substring patterns, and lua code for lua trigger types
        "type": "startOfLine"     // accepted trigger types are "substring", "regex", "startOfLine", "exactMatch", "lua", "spacer", "color", "colour", and "prompt"
      },
      {
        "pattern": "5", // spacer triggers take the number of lines to wait
        "type": "spacer"
      },
      {
        "pattern": "13,3", // Uses ansi colour codes. FG,BG , accepts 0-255. 0-15 are the first screen in the editor, the rest settable under "more colors" in the editor
        "type": "color"
      },
      {
        "type": "prompt" // Prompt triggers require no pattern, and anything you might put in one will be ignored
      }
    ],
    "script": "return true" // you can put lua code in here if you really want. Defaults to reading $name.lua from same folder
    "children": [ //A list of any child triggers. 
      {
        // you would describe the first child trigger here, with the same json fields as above
      }, 
      {
        // and the second child trigger, and so on and so forth.
      }
    ]
  },
  {
  // You would describe the next trigger in this folder here.
  }
]
```
