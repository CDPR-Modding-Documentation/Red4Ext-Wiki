---
description: >-
  This guide will show you how you can quickly install a plugin in your game's
  folder after building it.
---

# Installing a plugin with red-cli

## Introduction

[red-cli](https://github.com/rayshader/cp2077-red-cli) is command line interface tool to improve your experience as a scripting modder. It allows you to run commands from a terminal to quickly install your plugin in the game folder. It is also convenient to make an archive with your plugin, ready to release to users on Nexus Mods.

{% hint style="info" %}
This tool is also compatible with Redscript. See [this guide](https://app.gitbook.com/s/-McniwB8YOK2HnJ7SYg\_/getting-started/setting-up-redscript-and-vscode#id-4.-bundle-with-red-cli) if you are also writing scripts in addition to a RED4ext plugin.
{% endhint %}

## Setup red-cli

This tool requires a `red.config.json` file to be present in the root directory of your project. Basically, it should be in the same directory of your `CMakeLists.txt` file.

You can find everything you need to install / configure red-cli through its README.

## Setup CMake

You can add a command in your `CMakeLists.txt` file to execute `red-cli` after building in **Debug** or in **Release** mode. We will use the function `add_custom_command` of CMake to do this.

We can ask CMake to execute the command `red-cli install` in **Debug** mode and the command `red-cli pack` in **Release** mode:

```cmake
## Debug mode: install scripts (+ tests) and plugin in game folder.
## Release mode: create archive with bundled scripts and plugin.
add_custom_command(
        TARGET ${CMAKE_PROJECT_NAME}
        POST_BUILD
        WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
        COMMENT "$<$<CONFIG:Debug>:Install scripts with red-cli>" "$<$<CONFIG:Release>:Build archive with red-cli>"
        COMMAND "$<$<CONFIG:Debug>:red-cli;install>" "$<$<CONFIG:Release>:red-cli;pack>"
        COMMAND_EXPAND_LISTS
)
```

Now, every time you compile your project in **Debug** mode, it will install the DLL plugin and the scripts in the game's folder. You're ready to run the game and test your mod.

When your project is ready, you can compile your project in **Release** mode, it will create an archive with your DLL plugin and a bundle of your scripts. You could extract the archive in your game's folder, and test it to make sure everything is in order prior to releasing it on Nexus Mods.
