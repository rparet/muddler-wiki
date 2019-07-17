## Scripts
As with all things muddler, the timer objects are described using a json file. Note that while you can include lua code in the script key, this is not really how it is meant  to be used. It can be handy for short snippets you don't want to make a new file for though.

## timers.json
```
[
  {
    "name": "animate timers", # the name of the timer object being created
    "isActive": "yes", # is this active? Defaults to yes
    "command": "score", # if you want the timer to fire off a command (like the command field in the editor) put it here
    "hours": "0", # same as editing the hours section in Mudlet. defaults to 0
    "minutes": "0", # same as above, but minutes. defaults to 0
    "seconds": "0", # same as seconds in Mudlet, defaults to 0
    "milliseconds": "5", # same as milliseconds in Mudlet. Defaults to 0.
    "script": "return true" # you can put lua code in here if you really want. Defaults to reading $name.lua from same folder. Spaces are changed to _
    ]
  },
  {
  # You would describe the next timer in this folder here.
  }
]
```