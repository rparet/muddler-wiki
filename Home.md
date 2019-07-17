# Welcome to the muddler wiki!

## Installation
* Download the latest release from https://github.com/demonnic/muddler/releases.
* For instance, muddle-shadow-0.1.zip
* Unzip file to a location on your harddrive, then add the bin directory inside muddle-shadow-0.1 directory you just unzipped to your path. For instance, if you unzipped the file to C:, you would add C:\muddle-shadow-0.1\bin to your path.

## Configuration
At the root of your project, you may place a file named `mfile` . This file is a simple json object, formatted thusly:
```
{
  "package": "animatedtimers",
  "version": "3.1"
}
```
These will be concatenated as `$package-$version` for the final mpackage value which is stored in config.lua for the zipped mudlet package. 

You can look at the included recreation of my animated timers under testmuddler in the muddler source for some guidelines, but the project structure should look something like this:
```
C:\ANITIMERS
│   mfile
│
└───src
    ├───aliases
    │   └───AnimatedTimers
    │           aliases.json
    │           anitimer_demo.lua
    │
    ├───resources
    │       test1.jpg
    │       test5.jpg
    │
    ├───scripts
    │   └───AnimatedTimers
    │           code.lua
    │           scripts.json
    │
    └───timers
        └───AnimatedTimers
                animate.lua
                timers.json
```
In the above example, I can see by looking there is a script object named 'code' in an AnimatedTimers group. The lua code which would go in the big script box resides in code.lua. scripts.json describes the rest of the attributes of the script objects in this folder. If the object name has spaces, the lua filename with have them replaced by _, so `anitimer demo` became `anitimer_demo.lua` in the above example.

The resources directory holds any additional files you want to be included in the zipped mpackage.

Please see the pages for the individual Mudlet objects on the right bar. Yes, I'm aware you technically can't put comments in json, but it was easiest for documentation purposes at the moment.
