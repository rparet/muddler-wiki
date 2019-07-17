## Scripts
As with all things muddler, the alias objects are described using a json file. Note that while you can include lua code in the script key, this is not really how it is meant  to be used. It can be handy for short snippets you don't want to make a new file for though.

## aliases.json
```
[
  {
    "name": "code", # the name of the alias object being created
    "isActive": "yes", # is this active? Defaults to yes
    "command": "score", # raw command to send to the mud. Same as typing in the command field in the alias editor in Mudlet
    "regex": "^s\\w$", # the regular expression to match for the alias. Note that the \ had to be escaped here.
    "script": "return true" # you can put lua code in here if you really want. Defaults to reading $name.lua from same folder
    ]
  },
  {
  # You would describe the next alias in this folder here.
  }
]
```