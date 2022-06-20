# IF YOU NEED ACCESS TO THINGS IN ROR2.DLL, THIS IS MORE COMPLICATED THAN IT NEEDS TO BE FOR YOU #

See https://github.com/BepInEx/BepInEx.NuGetUpload.Service/wiki#how-to-download-game-assembly-packages

Use RiskOfRain2.GameLibs

Everything that was private will be as though its public

## Guide

This guide goes thru how to reference private, internal, protected and readonly fields, properties and methods via Reflection.

## The basics

> Reflection provides objects (of type Type) that describe assemblies, modules and types. You can use reflection to dynamically create an instance of a type, bind the type to an existing object, or get the type from an existing object and invoke its methods or access its fields and properties.

Simply put, reflection allows you to reference object behaviours, properties, and fields when they are otherwise out of scope. Some examples of these might be as the title suggests, but you may also use reflection when the Type of the object is ambiguous in order to attempt to invoke a method of a specific type, if the object conforms to that TypeInfo, MethodInfo, or so on.

## Types
TypeInfo:

> Represents type declarations for class types, interface types, array types, value types, enumeration types, type parameters, generic type definitions, and open or closed constructed generic types.

TypeInfo can be thought of as a signature for types, their unique identifier that differentiates them from all other types. Given a specific TypeInfo, we can perform comparisons in order to determine, and therefor invoke the correct methods, reference the correct fields and so on for a given object.

    var obj = "asdf";
    if(typeof(obj) == typeof(string)) {//true}

## Static vs Instance members

For static members you must specify the type like so

    typeof(MyStaticClass).GetXXX<int>("myStaticInt");

For instance members

    myInstance.GetXXX<int>("myInt");

## Fields, Properties, and invoking methods.

Given an instance of an object, we can use reflection to reference properties and behaviours of that object, even ones that would normally be out of scope for us. We pass the return type of the field via <T> and the fieldinfo / name via parameter:

**Fields**

    HealthComponent hc = new HealthComponent();
    var rhc = hc.GetFieldValue<float>("regenAccumulator");

`Note: For structs you must use an alternate method: GetStructFieldValue<> / SetStructFieldValue<>`

**Properties**

At their core, Properties are basically just methods. Under the hood, the method names are prepended with get_ and set_ respectively, however reflection does provide specific methods that interface with them specifically, however they can also be invoked via methods:

    HealthComponent hc = new HealthComponent();
    var alive = hc.GetPropertyValue<bool>("alive");

**Methods**

Methods return void, or the return type specified, and are invoked via passing `args[]` as parameter. Properties take no parameters for get_, however they take only one parameter for set_, which will match the return type.

    HealthComponent hc = new HealthComponent();
    hc.InvokeMethod<bool>("set_godMode", true);

or for static types that return void (no type argument required)

    typeof(SurvivorCatalog).InvokeMethod("Init");

Note that both static / instance methods and void / methods that return are supported (see above `Static vs Instance members` above)

## Private subtypes, and their properties/methods

Private subtypes can be referred to through reflection from their parent class/object, and can cascade down to their properties and methods:

    HealthComponent hc = new HealthComponent();
    var rhc = hc.GetType().GetNestedTypeCached("RepeatHealComponent").GetFieldCached("reserve");

When reflecting upon a type, rather than an object instance, we can use the Cached getter methods provided by R2API.Utils in order to speed up any future references to the same instance. 

## MSDN
If anything has not been covered, or more info is required, please read over MSDN: https://docs.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/viewing-type-information#memberinfo-methodinfo-fieldinfo-and-propertyinfo