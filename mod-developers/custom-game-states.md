# Custom game states

RED4ext offers the possibility to attach custom game states to the game. These can be used to initialize or do some things before or after the game system are available, such as scripting system.

## State functions

Every state has the following functions:

### OnEnter

Called immediately after the state is activated. This function is called once by the game, so be careful what you want to do here. A good example is to initalize some memory that is needed later.

#### Return

* The return value is not taken in consideration. It is recommended to always return `true` for future proofing.

### OnUpdate

Called every frame. This function can contain more complex code.

#### Return

* `false` - if the state did not finish executing.
* `true` - if the state finished executing.

{% hint style="info" %}
If the game / custom state returns `true` it will not be called next frame. For example:

* `InitializationState::OnUpdate` - returns `false`
* `CustomInitState::OnUpdate` - returns `true`

Next frame only `InitializationState::OnUpdate` will be called. Same for the reverse.

The only exception from this rule is the `RunningState` where only the returned value by the game's state is taken into consideration. For example:

* `RunningState::OnUpdate` - returns `true`
* `CustomRunningState::OnUpdate` - returns `false`

Then the frame, `RunningState::OnExit` will be called.
{% endhint %}

### OnExit

Called when the state is ending. This function is called once by the game, so be careful what you want to do here.

#### Return

* The return value is not taken in consideration. It is recommended to always return `true` for future proofing.

## Game's Life Cycle

![](<../.gitbook/assets/game\_state\_life\_cycle (1).svg>)

## Example

```cpp
#include <RED4ext/RED4ext.hpp>

bool BaseInit_OnEnter(RED4ext::CGameApplication* aApp)
{
    return true;
}

bool BaseInit_OnUpdate(RED4ext::CGameApplication* aApp)
{
    static uint32_t i = 0;
    if (i == 3)
    {
        return true;
    }

    i++;
    return false;
}

bool BaseInit_OnExit(RED4ext::CGameApplication* aApp)
{
    return true;
}

bool Shutdown_OnUpdate(RED4ext::CGameApplication* aApp)
{
    static uint32_t i = 0;
    if (i == 3)
    {
        return true;
    }

    i++;
    return false;
}

RED4EXT_C_EXPORT bool RED4EXT_CALL Main(RED4ext::PluginHandle aHandle, RED4ext::EMainReason aReason, const RED4ext::Sdk* aSdk)
{
    switch (aReason)
    {
    case RED4ext::EMainReason::Load:
    {
        RED4ext::GameState initState;
        initState.OnEnter = &BaseInit_OnEnter;
        initState.OnUpdate = &BaseInit_OnUpdate;
        initState.OnExit = &BaseInit_OnExit;

        aSdk->gameStates->Add(aHandle, RED4ext::EGameStateType::BaseInitialization, &initState);
        
        RED4ext::GameState shutdownState;
        shutdownState.OnEnter = nullptr;
        shutdownState.OnUpdate = &Shutdown_OnUpdate;
        shutdownState.OnExit = nullptr;

        aSdk->gameStates->Add(aHandle, RED4ext::EGameStateType::Shutdown, &shutdownState);

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
