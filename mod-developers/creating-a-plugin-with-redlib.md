---
description: >-
  This guide will show you how you can write a plugin with beautiful code thanks
  to RedLib and RED4ext.SDK. If you haven't, you should learn the fundamentals
  of writing a plugin (see Creating a Plugin).
---

# Creating a plugin with RedLib

## Requirements

You must install RED4ext.SDK in your project like any other plugin (see [Creating a plugin](creating-a-plugin.md)).\
You need to install [RedLib](https://github.com/psiberx/cp2077-red-lib). See the README to configure your CMake project.

## Setup plugin to register types

Now you can change the entry-point of your plugin like this:

```cpp
/// File: src\\main.cpp

#include <RED4ext/RED4ext.hpp>
// You must include RedLib. Note that it introduces an alias
// such as namespace RED4ext can be replaced by Red.
#include <RedLib.hpp>

RED4EXT_C_EXPORT bool RED4EXT_CALL Main(RED4ext::PluginHandle aHandle,
                                        RED4ext::EMainReason aReason,
                                        const RED4ext::Sdk* aSdk) {
  switch (aReason)  {
  case RED4ext::EMainReason::Load: {
    // It will automatically register types declared below.
    Red::TypeInfoRegistrar::RegisterDiscovered();
    break;
  }
  case RED4ext::EMainReason::Unload: {
    break;
  }
  }
  return true;
}

// ...
```

## Declare new classes

We are going to create a zoo, and name our mod... `Zoo`. Now lets create a generic `Animal` class:

```cpp
/// File: src\\Animal.hpp

#include <RED4ext/Scripting/Natives/Generated/Vector4.hpp>
#include <RedLib.hpp>

class Animal : public Red::IScriptable {
public:
  //Animal() = default;

  virtual bool CanFly() const {
    return false;
  }
  virtual bool CanWalk() const {
    return false;
  }

  virtual bool is_carnivore() const {
    return false;
  }
  virtual bool is_herbivore() const {
    return false;
  }

  Red::Vector4 position{};

  RTTI_IMPL_TYPEINFO(Animal);
  RTTI_IMPL_ALLOCATOR();
}

RTTI_DEFINE_CLASS(Animal, {
  // You only need to add an alias when declaring scripts 
  // with 'module' and 'import' keywords.
  RTTI_ALIAS("Zoo.Animal");

  RTTI_ABSTRACT();

  RTTI_METHOD(CanFly);
  RTTI_METHOD(CanWalk);

  // You can use another name to declare instead.
  RTTI_METHOD(is_carnivore, "IsCarnivore");
  RTTI_METHOD(is_herbivore, "IsHerbivore");
  
  RTTI_GETTER(position);
});
```

Now lets add a `Wolf` to our zoo:

```cpp
/// File: src\\Wolf.hpp

#include <RedLib.hpp>

#include "Animal.hpp"

class Wolf : public Animal {
public:
  //Wolf() = default;

  bool CanWalk() const override {
    return true;
  }

  bool IsCarnivore() const override {
    return true;
  }

  bool CanHunt() const {
    return true;
  }

  RTTI_IMPL_TYPEINFO(Wolf);
  RTTI_IMPL_ALLOCATOR();
};

RTTI_DEFINE_CLASS(Wolf, {
  RTTI_ALIAS("Zoo.Wolf");

  RTTI_PARENT(Animal);

  // Redeclare methods you override.
  RTTI_METHOD(CanWalk);
  RTTI_METHOD(is_carnivore, "IsCarnivore");

  RTTI_METHOD(CanHunt);
});
```

If you tried to declare a class with only RED4ext.SDK so far, it sure looks better now with RedLib! Now compile your project and install your plugin in the game's directory:

> \<Cyberpunk 2077>\red4ext\plugins\Zoo\Zoo.dll

We also need to declare native types so we can use them in scripts:

```swift
/// File: scripts\\Zoo.reds

// Again, declaring native types within a module
// is not required.
module Zoo

public abstract native class Animal extends IScriptable {
  public native func CanFly() -> Bool;
  public native func CanWalk()-> Bool;

  public native func IsCarnivore() -> Bool;
  public native func IsHerbivore() -> Bool;

  public native func GetPosition() -> Vector4;
}

public native class Wolf extends Animal {
  // You don't need to redeclare methods of the parent,
  // even when you override them.

  // But you have to declare new methods.
  public native func CanHunt() -> Bool;
}
```

Lets create our zoo somewhere when the game starts:

```swift
/// File: scripts\\ZooTest.reds

import Zoo.*

public class ZooSystem extends ScriptableSystem {
  private let m_animals: array<ref<Animal>>;

  private func OnAttach() {
    // Create a pack of wolves.
    ArrayPush(this.m_animals, new Wolf());
    ArrayPush(this.m_animals, new Wolf());
    ArrayPush(this.m_animals, new Wolf());
    ArrayPush(this.m_animals, new Wolf());

    for animal in this.m_animals {
      LogChannel(n"Info", s"Class name: s\(animal.GetClassName())");
      LogChannel(n"Info", "");
      LogChannel(n"Info", s"Can fly? s\(animal.CanFly())");
      LogChannel(n"Info", s"Can walk? s\(animal.CanWalk())");
      LogChannel(n"Info", s"Is carnivore? s\(animal.IsCarnivore())");
      LogChannel(n"Info", s"Is herbivore? s\(animal.IsHerbivore())");
      let wolf = animal as Wolf;

      LogChannel(n"Info", "");
      LogChannel(n"Info", s"Can hunt? \(wolf.CanHunt())");
      LogChannel(n"Info", "");
    }
  }

  private func OnDetach() {
    ArrayClear(this.m_animals);
  }

}
```

Install scripts in your game's directory:

> \<Cyberpunk 2077>\r6\scripts\Zoo\Zoo.reds
>
> \<Cyberpunk 2077>\r6\scripts\ZooTest.reds

Run the game, you should see some outputs in the Game Log when using CET.

## Conclusion

This is a simple introduction to RedLib and how it can make your life easier when writing plugin with [RED4ext.SDK](https://github.com/WopsS/RED4ext.SDK). You should definitively go to the [GitHub repository](https://github.com/psiberx/cp2077-red-lib/) and read through the entire README to learn about all the features it provides.

Happy coding!
