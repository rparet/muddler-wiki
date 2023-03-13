# Continuous Integration/Deployment with muddler

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

## On Github

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
      - uses: actions/checkout@v3.3.0

      - name: Muddle
        uses: demonnic/build-with-muddler@v1.3

      - name: Upload MPackage
        uses: actions/upload-artifact@v3.1.2
        with:
          name: MDK-package
          path: build/tmp/

      - name: Run Busted tests
        uses: demonnic/test-in-mudlet@v1.2
        with:
          pretestPackage: ${{ github.workspace }}/build/MDK.mpackage
```
