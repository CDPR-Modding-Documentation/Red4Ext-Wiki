# Creating a Plugin

To create a new plugin you can whatever build systen / toolchain you would like, as long as the output is compatible with x86-64 architecture and with `__fastcall` calling convention.

Some examples you can use one of the following:

* [CMake](https://cmake.org/)
* [Premake](https://premake.github.io/)
* [Visual Studio](https://visualstudio.microsoft.com/)

You can use one of the following project as a starter for your own plugin (all of the projects below are already configured for RED4ext plugin development):

* [CMake](https://github.com/WopsS/RED4ext.Example.CMake)
* [Premake](https://github.com/WopsS/RED4ext.Example.Premake)
* [Visual Studio](https://github.com/WopsS/RED4ext.Example.VisualStudio)

After you decide what build systen or toolchain you would like to use make sure to add [RED4ext.SDK](https://github.com/WopsS/RED4ext.SDK) to project's include paths. This dependency is necessary since it provides the API that RED4ext is using to load the plugin, as well as the game's structures that you might need.

Your mod should export few functions in order to be loaded correctly (see below which functions needs to be exported), to export a function from C/C++ you can use RED4ext's helpers, `RED4EXT_C_EXPORT` (equivalent of `extern "C" __declspec(dllexport)`) and `RED4EXT_CALL` (equivalent of `__fastcall`).

{% hint style="info" %}
**Good to know**: You must place your plugin in `<game_directory>/red4ext/plugins` or in a sub-directory (e.g. `<game_directory>/red4ext/plugins/<your_plugin's_name>`).

If your plugin needs to load other dynamic libraries (DLLs) you can place them in the same folder where your plugin is located.
{% endhint %}

{% hint style="warning" %}
If you keep your MSVC environment up to date, it will update Visual C++ Redistributable on your system. Players may not be on the same, latest version of VC Redist. In such case, the game is likely to crash. Make sure to inform players to download the latest version when troubleshooting. You can redirect them to this [link](https://aka.ms/vs/17/release/vc\_redist.x64.exe).
{% endhint %}

## Exported functions

### Supports

Specify the API version of the plugin.

#### Return

Returns the support API version supported by your plugins.

{% hint style="info" %}
To support the latest version, return `RED4EXT_API_VERSION_LATEST`.

To support a specific version, return `RED4EXT_API_VERSION_<VERSION>`, e.g. `RED4EXT_API_VERSION_0`.
{% endhint %}

### Query

This function is used to fill your plugin information, like name, author, supported game version, etc..

#### Parameters

* **RED4ext::PluginInfo\*** - The pointer to the structure that contains information about your plugin.

{% hint style="warning" %}
**Do not** do anything in this function yet (like creating a hook or allocating a trampoline).

Runtime version is the game's version that your plugins support, if your plugin has runtime set to `1.21` your plugin will be loaded **only** if the game's version is `1.21`, else it would not be loaded. It is recommended to set it to `RED4EXT_RUNTIME_LATEST` to target the latest game's version that the SDK defined.

If you want to use RED4ext only as a loader and you do not care about game's version use `RED4EXT_RUNTIME_INDEPENDENT`.
{% endhint %}

### Main

The entry-point of the plugin. This equivalent of `DllMain` except that it is called after RED4ext make sure that your plugin is compatible with the game's version.

Here you can attach hooks and register RTTI types.

{% hint style="warning" %}
Memory allocators are not initialized yet. If you need to use RTTI system (call functions for example), see [Custom game states](custom-game-states.md) to execute your code when RTTI system is ready.
{% endhint %}

#### Parameters

* **RED4ext::PluginHandle** - The unique identifier of the plugin.
* **RED4ext::EMainReason** - The reason why the main is called.
* **RED4ext::Sdk\*** - The pointer to the SDK structure.

#### Return

* `false` - if the plugin did not initialize properly.
* `true` - if the plugin initalized properly.

#### Remarks

If the entry-point function returns `false` following a `Load` notification, it receives an `Unload` notification and the plugin is unloaded immediately. However, if the `Load` code throws an exception, the entry-point function will not receive the `Unload` notification.

## Example

```cpp
#include <RED4ext/RED4ext.hpp>

RED4EXT_C_EXPORT bool RED4EXT_CALL Main(RED4ext::PluginHandle aHandle, RED4ext::EMainReason aReason, const RED4ext::Sdk* aSdk)
{
    switch (aReason)
    {
    case RED4ext::EMainReason::Load:
    {
        // Attach hooks, register RTTI types, add custom states or initalize your application.
        // DO NOT try to access the game's memory at this point, it is not initalized yet.
        break;
    }
    case RED4ext::EMainReason::Unload:
    {
        // Free memory, detach hooks.
        // The game's memory is already freed, to not try to do anything with it.
        break;
    }
    }

    return true;
}

RED4EXT_C_EXPORT void RED4EXT_CALL Query(RED4ext::PluginInfo* aInfo)
{
    aInfo->name = L"Plugin's name";
    aInfo->author = L"Author's name";
    aInfo->version = RED4EXT_SEMVER(1, 0, 0); // Set your version here.
    aInfo->runtime = RED4EXT_RUNTIME_LATEST;
    aInfo->sdk = RED4EXT_SDK_LATEST;
}

RED4EXT_C_EXPORT uint32_t RED4EXT_CALL Supports()
{
    return RED4EXT_API_VERSION_LATEST;
}
```
