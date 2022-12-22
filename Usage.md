# Usage

Once you've installed muddler, you'll need to get a project setup and start using it. You may wish to watch my demonstration at <https://www.youtube.com/watch?v=LNKl0BZud5c> after reading over this page.

## Quickstart

Muddler now comes with a couple of runtime options to help you set up your project.

* `--generate`
  * Will ask you a series of questions to guide you through creating the basic skeleton for your project
  * Recommended way to create a new project. Generates your mfile and folder structures for you to get started with.
  * `muddle --generate` in your terminal
* `--default`
  * Generates a full project with a default name and every type of Mudlet object that muddle supports
  * Mostly used for generating a template repository to serve as an example of what muddler can do.
  * Will remove and recreate the folder `TemplateProject` within your current directory.
  * `muddle --default` in your terminal

## Package Configuration

At the root of your project, you may place a file named `mfile` . The mfile controls the configuration of your packages metadata. That is to say the things which show up in the package manager. It contains a simple json object, with the following valid keys

* package
  * the name of the package.
  * If not provided, it defaults to the name of the directoy the project is in. For example if your project is in `/home/demonnic/projects/CoolProject/` then the default value will be `CoolProject`
* title
  * the 'short description' in the package exporter in Mudlet 4.12+
* description
  * the full description of the package.
  * supports Markdown
  * will be autofilled by contents of the `README.md` file at the root level of the project if it exists and the value is not provided in the mfile.
* version
  * the version of the package.
* author
  * that would be you probably
* icon
  * the name of the file in src/resources/ which should be used for your package's icon file.
  * this file is copied into place rather than moved, as I don't want to assume you won't use it for other things and a 512px by 512px image isn't very large
* dependencies
  * This is a comma separated list of other packages which should be installed for this one to work properly
  * Currently no enforcement or resolution around this, though there may be down the line with Mudlet's package repository

An example mfile with all the options might look like this

```json
{
  "package": "Recoginator",
  "title": "The recognition maker",
  "description": "Manages your rp recognitions for Lusternia. I normally leave this to the README. rather than the mfile",
  "version": "1.0.0",
  "author": "Demonnic",
  "icon": "computer.png",
  "dependencies": "Package One,Package Two"
}
```

## File Structure

At the root of the package you have the mfile, and the `src` directory. The `src` directory will hold all the other pieces which are used to construct your Mudlet package. Within the `src` directory you may have the following sub directories:

* aliases
  * will be scanned for further subdirectories with an aliases.json file. See [[Aliases]]
* keys
  * will be scanned for further subdirectories with a keys.json file. See [[Keybindings]]
* resources
  * these files are copied to the root of your package, the same as the folder you drag things into in the package exporter in Mudlet to include with your package, so your image and other support files.
  * these files eventually wind up in `getMudletHomeDir() .. "/@PKGNAME@/"`.
  * valid Mudlet xml in this directory will be imported when the package is imported.
  * this is also where your icon file should be placed if you've set one, with a name matching the icon value in your mfile
* scripts
  * will be scanned for further subdirectories with a scripts.json file. See [[Scripts]]
* timers
  * will be scanned for further subdirectories with a timers.json file. See [[Timers]]
* triggers
  * will be scanned for further subdirectories with a triggers.json file. See [[Triggers]]

You can look at the included recreation of my animated timers under testmuddler in the muddler source for an example, where the project structure looks a bit like this:

```txt
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

Not every project will need every directory/type. In the example above, the resources directory was added for testing muddler honestly. A lot of times I get away with just `src/scripts`.

As a convention, an item's scripting can be automatically read from a .lua file of the same name, with spaces replaced by _. So in the example above the `code.lua` file will be read into a script object named `code` in Mudlet.

Similarly, there is an alias named `anitimer demo` in a folder/group named `AnimatedTimers` and the code for it is in the `anitimer_demo.lua` file.

Please see the pages for the individual Mudlet objects below or on the right bar for a complete description of the json you can use to describe each time.

* [Triggers](Triggers.md)
* [Aliases](Aliases.md)
* [Scripts](Scripts.md)
* [Timers](Timers.md)
* [Keybindings](Keybindings.md)

## Token Replacement

Muddler provides for simple token replacement in your source files. Supported tokens are:

* `@PKGNAME@` or `__PKGNAME__`
  * replaced by the package name, in the example mfile above this would be "Recoginator"
  * your packages install directory becomes `getMudletHomeDir() .. "/@PKGNAME@/"`
  * this also means you can require files. To require 'emco.lua' from your package root for instance you could use `require("@PKGNAME@.emco")` and muddler will transform it to `require("Recoginator.emco")` for instance.
* `@VERSION@` or `__VERSION__`
  * replaced by the version set in the mfile
  * if no version is set, is replaced by the empty string.

## Building your project

Once you've got your project setup (see below and sidebars) and muddler setup in your path (see above) then it's as simple as running `muddle` in the root of your project.

## Continuous Integration/Deployment with muddler

There are two ways to set things up

* [Locally](#locally)
  * For doing development on your computer
  * Removes and reinstalls your package in an open profile every time you build the package
* [On Github](#on-github)
  * For use with github workflows

## Locally

If you want to automatically install your pkg in your local Mudlet with muddler you can set the `outputFile` key in the mfile to true, which causes muddler to write a .output file to the root of your project when it is done building. This file contains a simple json object with the package name and the file location.

Then install the Muddler.mpackage from the releases page in Mudlet which allows for reloading muddler packages when they are built. This is intended to be develop time only, which is to say things you want to have happen when testing the package which you don't necessarily want to happen on pkg removal/install on your users machine, such as test object setup or teardown, running test functions, etc. If using this I recommend adding .output to your .gitignore file as it will see some churn during development.

The most basic of these might look like this:

```lua
myCIhelper = myCIhelper or Muddler:new({
  path = "/home/demonnic/gitbox/MDK"
})
```

And is enough to cause Mudlet to uninstall and reinstall the MDK mpackage created when I run muddler in `/home/demonnic/gitbox/MDK`. If you're using this on windows, still use / as the path separator, IE `"C:/Users/demonnic/gitbox/MDK"`

All available options are:

* path
  * the path to the muddler project. `"/home/demonnic/gitbox/MDK"` for example
* watch
  * determines if it will start the watcher on instantiation, or can be checked to see if it is active
* preremove
  * runs before pkg removal
  * function or codestring like tempTimer/etc
    * `[[send("test")]]`
    * function() send("test") end
* postremove
  * like preremove, but after pkg removal
* preinstall
  * like preremove, but before pkg install
* postinstall
  * like preremove, but after pkg install

The actual helper for MDK I'm using right now is setup like this

```lua
local function killMDK()
  for pkgName, _ in pairs(package.loaded) do
    if pkgName:find("MDK") then
      debugc("Uncaching lua package " .. pkgName)
      package.loaded[pkgName] = nil
    end
  end
end
local function create_helper()
  if MDKhelper then MDKhelper:stop() end
  MDKhelper = Muddler:new({
    path = "/home/demonnic/gitbox/MDK",
    postremove = killMDK,
  })
end

if not MDKhelper then
  registerAnonymousEventHandler("sysLoadEvent", create_helper)
end
```

And the output in the error console 

![image](https://user-images.githubusercontent.com/3660/122658910-60f96000-d140-11eb-9629-28b4413dbae8.png)

### On Github

If you are hosting your project on github and want to build the package with muddler as part of your PR process, I provide a couple of github actions for working automatically building and testing your packages.

* [build-with-muddler](https://github.com/marketplace/actions/build-with-muddler)
  * uses muddler to build your project and create an mpackage
* [test-in-mudlet](https://github.com/marketplace/actions/run-busted-tests-in-mudlet)
  * allows you to run unit tests in mudlet in a github action using the [Busted test framework](https://lunarmodules.github.io/busted/).

And here is my MDK workflow for example. It builds the mpackage with Muddler, uploads it as a build artifact for download, and runs unit tests.

```yaml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Muddle
        uses: demonnic/build-with-muddler@v1.3

      - name: Upload MPackage
        uses: actions/upload-artifact@v2
        with:
          name: MDK-package
          path: build/tmp/

      - name: Run Busted tests
        uses: demonnic/test-in-mudlet@v1.2
        with:
          pretestPackage: ${{ github.workspace }}/build/MDK.mpackage
```