# Logging

RED4ext provides a logger for every plugin through the SDK class. First time a message is logged RED4ext will create a log file with the plugin's name in `<game_directory>/red4ext/logs` directory.

The logger settings, such as rotation, file size, etc., are based on the user's configuration file.

## Example

```cpp
#include <RED4ext/RED4ext.hpp>

RED4EXT_C_EXPORT bool RED4EXT_CALL Main(RED4ext::PluginHandle aHandle, RED4ext::EMainReason aReason, const RED4ext::Sdk* aSdk)
{
    switch (aReason)
    {
    case RED4ext::EMainReason::Load:
    {
        aSdk->logger->Trace(aHandle, "Hello World!");
        aSdk->logger->TraceF(aHandle, "Hello %s!", "World");

        break;
    }
    case RED4ext::EMainReason::Unload:
    {
        break;
    }
    }

    return true;
}
```
