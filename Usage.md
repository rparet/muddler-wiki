# Usage

Once you've installed muddler, you'll need to get a project setup and start using it. You may wish to watch my demonstration at <https://www.youtube.com/watch?v=LNKl0BZud5c> after reading over this page.

## Quickstart

Muddler now comes with a couple of runtime options to help you set up your project.

* `--generate`
  * Will ask you a series of questions to guide you through creating the basic skeleton for your project
  * Recommended way to create a new project. Generates your mfile and folder structures for you to get started with.
  * `muddle --generate` in your terminal
* `--default`
  * Generates a full project with a default name and every type of Mudlet object that muddler supports
  * Mostly used for generating a template repository to serve as an example of what muddler can do.
  * Will remove and recreate the folder `TemplateProject` within your current directory.
  * `muddle --default` in your terminal

## Package Configuration

At the root of your project, you may place a file named `mfile` . The mfile controls the configuration of your packages metadata. That is to say the things which show up in the package manager. It contains a simple json object, with the following keys

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
* outputFile
  * true/false, if true will create a .output file for use with the local CI setup when you build the project.

An example mfile with all the options might look like this

```json
{
  "package": "Recoginator",
  "title": "The recognition maker",
  "description": "Manages your rp recognitions for Lusternia. I normally leave this to the README. rather than the mfile",
  "version": "1.0.0",
  "author": "Demonnic",
  "icon": "computer.png",
  "dependencies": "Package One,Package Two",
  "outputFile": true
}
```

## File Structure

At the root of the project you have the mfile, and the `src` directory. The `src` directory will hold all the other pieces which are used to construct your Mudlet package. Within the `src` directory you may have the following sub directories:

* aliases
  * will be scanned for further subdirectories with an aliases.json file. See [Aliases](Aliases)
* keys
  * will be scanned for further subdirectories with a keys.json file. See [Keybindings](Keybindings)
* resources
  * these files are copied to the root of your package, the same as the folder you drag things into in the package exporter in Mudlet to include with your package, so your image and other support files.
  * these files eventually wind up in `getMudletHomeDir() .. "/@PKGNAME@/"`.
  * valid Mudlet package xml files in this directory will be imported when the package is imported.
  * this is also where your icon file should be placed if you've set one, with a name matching the icon value in your mfile
* scripts
  * will be scanned for further subdirectories with a scripts.json file. See [Scripts](Scripts)
* timers
  * will be scanned for further subdirectories with a timers.json file. See [Timers](Timers)
* triggers
  * will be scanned for further subdirectories with a triggers.json file. See [Triggers](Triggers)

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

Again, please see the pages for the individual Mudlet objects below or on the right bar for a complete description of the json you can use to describe each time.

* [Triggers](Triggers)
* [Aliases](Aliases)
* [Scripts](Scripts)
* [Timers](Timers)
* [Keybindings](Keybindings)

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

## CI/CD or "How do I reinstall it every time I build it?"

Once you've built your package you'll probably want to test it, either in your local copy of Mudlet or in GitHub.

To find out more about the options for doing this, take a look [HERE](CI)
