# Adding a Native Function

## RED4ext Snippets

To give our class some functionality, we'll first need to create a function, which we'll name `GetNumber`. The arguments that this function accepts on the RED4ext side are very specific and unrelated to the arguments it accepts in Redscript, and need to be particular types.

```cpp
void GetNumber(RED4ext::IScriptable* aContext, RED4ext::CStackFrame* aFrame,
    float* aOut, int64_t a4)
{
    RED4ext::Vector4 worldPosition;
    int32_t count;
    
    RED4ext::GetParameter(aFrame, &worldPosition);
    RED4ext::GetParameter(aFrame, &count);
    aFrame->code++; // skip ParamEnd
    
    if (aOut)
    {
        *aOut = 6.25;
    }
}
```

In the example above, the `float` in the header's `float* aOut` is the return type - it can also be a RTTI type like `RED4ext::GameObject`, but it needs to be a pointer. If the function takes no arguments, you can use `void* aOut` in the header instead.

This function takes `Vector4` and `Int32` as arguments on the Redscript side, and to get these values, we have to use `RED4ext::GetParameter` and the stack frame that's passed into our function through RED4ext. We'll retrieve them in-order, passing the function each of our variables' references. Once we've read the last argument, we need to run `aFrame->code++` to let the system know we're done reading the arguments.

From there, we can manipulate & mutate them however we like. At the end of it, we'll need to give `*aOut` a value, being sure to use the pointer. If the function has a `Void` return type, we can omit this line.

Once we have our function declared, we'll need to register it to the class we created on the last page, which will be done in the `PostRegisterTypes` callback:

```cpp
RED4EXT_C_EXPORT void RED4EXT_CALL PostRegisterTypes()
{
   // This section is from the previous page.
   auto rtti = RED4ext::CRTTISystem::Get();
   auto scriptable = rtti->GetClass("IScriptable");
   customControllerClass.parent = scriptable;

   auto getNumber = RED4ext::CClassFunction::Create(&customControllerClass, 
       "GetNumber", "GetNumber", &GetNumber, { .isNative = true });
   getNumber->AddParam("Vector4", "worldPosition");
   getNumber->AddParam("Int32", "count");
   getNumber->SetReturnType("Float");
   customControllerClass.RegisterFunction(getNumber);
}
```

In the `RED4ext::CClassFunction::Create` function, we pass the type we've created, the long & short names of the function as strings (how it'll be referenced in Redscript), and then a reference to the function itself.

Just like the class, we need to add the `isNative` flag to the function. The arguments need to be registered to the function using the name of the type (like it is in Redscript), followed by the argument name. The return's type is set in the same way, using the name of the type as a String.&#x20;

## Redscript Snippets

This header declaration will look similar to other Redscript class declarations, but with the `native` keywords, and the function added without a body (depending on your Redscript version, the semicolon at the end might need to be omitted):

```swift
public native class CustomController extends IScriptable {
    public native func GetNumber(worldPosition: Vector4, count: Int32) -> Float;
}
```

To use this in your project, you can add additional variables & methods to the class and build it out that way, or you can use it just like a normal function in a hook like this:

```swift
public class MyCustomClassPrinter extends ScriptableSystem {
    public func OnAttach() -> Void {
        LogChannel(n"DEBUG", "Hello!");
        let c = new CustomController();
        let vector = new Vector4(1.0, 2.0, 3.0, 4.0);
        let num = 50;
        LogChannel(n"DEBUG", "Number: " + FloatToString(c.GetNumber(vector, num)));
    }
}
```
