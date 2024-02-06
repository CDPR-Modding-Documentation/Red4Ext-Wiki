# Creating a Custom Native Class

When writing a RED4ext plugin in conjunction with a [Redscript](https://wiki.redmodding.org/redscript/) project, it can be useful to create class members in RED4ext, and access them through Redscript. A similar example in code form [can be seen here](https://github.com/WopsS/RED4ext.SDK/tree/master/examples/native\_class\_redscript).

## RED4ext Snippets

Here we need to create a struct that inherits from `RED4ext::IScriptable` (the class that all classes inherit from in Redscript). The header for this needs to be defined at the top-level, or in a namespace (outside any function):

```cpp
struct CustomController : RED4ext::IScriptable
{
    RED4ext::CClass* GetNativeType();
};
```

A template is then used to create all the necessary CP2077 hooks for the class - the type we pass to the template function is the type we just created, and the argument is the name of our class as a string in Redscript. We'll also need to define `GetNativeType()` that returns a reference to our CP2077 type. The name of `customControllerClass` doesn't matter in this context, as it's pointing to our type in RED4ext.

```cpp
RED4ext::TTypedClass<CustomController> customControllerClass("CustomController");

RED4ext::CClass* CustomController::GetNativeType()
{
    return &customControllerClass;
}
```

Using the class & type we've created, we'll write two hooks (`RegisterTypes` and `PostRegisterTypes`) to register the type with CP2077 at the appropriate times. The first one that gets called sets the `isNative` flag for the class, which will match the `native` descriptor on the Redscript side, and registers the type to CP2077:

```cpp
void RegisterTypes()
{
    RED4ext::CRTTISystem::Get()->RegisterType(&customControllerClass);
}
```

The next block will manually set the parent of our type to `IScriptable`, to match the class.

```cpp
void PostRegisterTypes()
{
   auto rtti = RED4ext::CRTTISystem::Get();
   auto scriptable = rtti->GetClass("IScriptable");
   customControllerClass.parent = scriptable;
}
```

To make all of this work, we then need to hook-in our callbacks, which we do with `RED4ext::RTTIRegistrator::Add` in our main function:

```cpp
RED4EXT_C_EXPORT bool RED4EXT_CALL Main(RED4ext::PluginHandle aHandle,
    RED4ext::EMainReason aReason, RED4ext::RED4ext* aRED4ext)
{
    switch (aReason)
    {
        case RED4ext::EMainReason::Load:
        {
            RED4ext::RTTIRegistrator::Add(RegisterTypes, PostRegisterTypes);
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

## Redscript Snippet

On this side, the declaration is pretty simple, and similar to the headers from [the decompiled scripts](https://codeberg.org/adamsmasher/cyberpunk). Technically the `extends IScriptable` is implied for all classes, and can be omitted here:

```swift
public native class CustomController extends IScriptable {

}
```

Like usual, you can define any non-native member variables and methods here, and use them in Redscript, but out of the box our class doesn't do anything yet. In the following pages, we'll define some functionality with custom native functions.
