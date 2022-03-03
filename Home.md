# Welcome to the muddler wiki

## What is muddler?

Muddler is a build tool for creating Mudlet packages. It creates xml files readable by Mudlet which produce first-class Mudlet citizens listed in the profile editor as opposed to a bunch of temporary ones or deleting/recreating permanent ones using the Mudlet Lua API.

It favors convention over configuration, which means it isn't designed to recreate everything from Mudlet perfectly or give access to every option necessarily, but rather to make it easy to create a Mudlet package if you stick within muddler's capabilities/understanding rather than attempting to bend it to your will.

## Installation

* Download the latest release from <https://github.com/demonnic/muddler/releases>.
* For instance, muddle-shadow-0.7.zip
* Unzip file to a location on your harddrive, then add the bin directory inside muddle-shadow-0.1 directory you just unzipped to your path. For instance, if you unzipped the file to C:, you would add C:\muddle-shadow-0.1\bin to your path.

## Docker image

Some folks may prefer running muddler in a docker image to install java and running the jvm. You can `docker pull demonnic/muddler:latest` to obtain the latest version of muddler. The image is a bit over 100mb if you don't already have adoptoopenjdk/openjdk8:alpine-slim pulled. Then create a script called "muddle" in your path with the following(for linux/osx. You may have to drop the `-u $(id -u):$(id -g)` portion on windows):

```bash
docker run --pull always --rm -it -u $(id -u):$(id -g) -v $PWD:/$PWD -w /$PWD demonnic/muddler
```

This will run muddle from within the docker container, which already has adoptopenjdk's openjdk8 installed. It also has the benefit of automatically pulling updates for you. If you don't want that, remove the `--pull always`.

## GitHub Action

Example is laid out on the marketplace page for the action: <https://github.com/marketplace/actions/build-with-muddler>

## Usage

Once you've got your project setup (see below and sidebars) and muddler setup in your path (see above) then it's as simple as running `muddle` in the root of your project.
You may wish to watch my demonstration at <https://www.youtube.com/watch?v=LNKl0BZud5c>

## Configuration

At the root of your project, you may place a file named `mfile` . This file is a simple json object. Valid keys are:

* package
  * the name of the package.
* title
  * the 'short description' in the package exporter in Mudlet 4.12+
* description
  * the full description of the package.
  * supports Markdown
  * will be autofilled by contents of `README.md` file at the root level of the project if it exists
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
  "version": "1.0.0",
  "author": "Demonnic",
  "icon": "computer.png",
  "dependencies": "Package One,Package Two"
}
```

The package value is used for the final package name. If you don't have an mfile, the package name will default to the same as the basename of the project. So if you have the project in a folder called `mySuperCoolProject` the package name will be `mySuperCoolProject`

## Token Replacement

Muddler provides for simple token replacement in your source files. Supported tokens are:

* `@PKGNAME@`
  * replaced by the package name, in the example above this would be "Recoginator"
  * your packages install directory becomes `getMudletHomeDir() .. "/@PKGNAME@/"`
  * this also means you can require files. To require 'emco.lua' from your package root for instance you could use `require("@PKGNAME@.emco")` and muddler will transform it to `require("Recoginator.emco")` for instance.
* `@VERSION@`
  * replaced by the version set in the mfile
  * if no version is set, is replaced by the empty string.

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

As you can see, not every project will need every directory. In fact, the resources directory was added for testing muddler. A lot of times I get away with just `src/scripts` and `src/resources`

There is a requirement that all items be within a subdirectory of its type designation. Which is to say you have to use `src/aliases/AnimatedTimers` rather than just `src/aliases`. This is due to a bug I simply haven't made the time to track down yet. I'll update this section if/when I do.

As a convention, an item's script can be automatically read from a .lua file of the same name, with spaces replaced by _. So in the example above I can see that there is a script in the `AnimatedTimers` group/folder named `code`.

Similarly, there is an alias named `anitimer demo` in a folder/group named `AnimatedTimers` and the code for it is in the `anitimer_demo.lua` file.

Please see the pages for the individual Mudlet objects on the right bar. Yes, I'm aware you technically can't put comments in json, but it was easiest for documentation purposes at the moment.

## CI/CD with muddler

If you want to automatically install your pkg with muddler you can set the `outputFile` key in the mfile to true, which causes muddler to write a .output file to the root of your project when it is done building. This file contains a simple json object with the package name and the file location.

Then install the Muddler.mpackage from the releases page in Mudlet which allows for reloading muddler packages when they are built, by adding a filewatch for the .output file and using the pkgname/path to drive a basic lifecycle. This lifecycle is intended to be develop time only, which is to say things you want to have happen when testing the package which you don't necessarily want to happen on pkg removal/install on your users machine, such as test object setup or teardown, running test functions, etc. If using this I recommend adding .output to your .gitignore file as it will see some churn during development.

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
    * [[send("test")]]
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