## Scripts
As with all things muddler, the script objects are described using a json file. Note that while you can include lua code in the script key, this is not really how it is meant  to be used. It can be handy for short snippets you don't want to make a new file for though.

## Scripts.json
```
[
  {
    "name": "code", # the name of the script object being created
    "isActive": "yes", # is this active? Defaults to yes
    "eventHandlerList": [ #list of registered event handlers for this script object. 
      "sysInstall",
      "gmcp.Char.Vitals"
    ],
    "script": "return true" # you can put lua code in here if you really want. Defaults to reading $name.lua from same folder
    ]
  },
  {
  # You would describe the next script in this folder here.
  }
]
```