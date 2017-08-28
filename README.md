# Zenject-DynamicObject

Zenject-DynamicObject is a tool that allows to automatically inject/bind dependencies into the prefabs instantiated at runtime by the [standard](https://docs.unity3d.com/ScriptReference/Object.Instantiate.html) Unity 3D instantiation flow.

As the additional side product, it's also allowed to use all power of [Zenject](https://github.com/modesttree/Zenject) framework in [Service Locator Pattern](#service-locator-pattern) style.

### What it's all about

If you want to instantiate a prefab at runtime and have any MonoBehaviour's automatically injected, Zenject framework [force](https://github.com/modesttree/Zenject/blob/master/README.md#general-guidelines--recommendations--gotchas--tips-and-tricks) you to instantiate game object throw [factory](https://github.com/modesttree/Zenject/blob/master/README.md#creating-objects-dynamically) or using the special [DiContainer.InstantiatePrefab](https://github.com/modesttree/Zenject/blob/master/README.md#dicontainer-methods-instantiate) method. In most cases, it works fine, but sometimes especially if you use 3d-party solutions, it can become a problem.

This tool solves this problem allowing you to use standard [Object.Instantiate](https://docs.unity3d.com/ScriptReference/Object.Instantiate.html) method and will ensure any MonoBehaviour's fields that are marked with the ```[Inject]``` attribute are filled in properly, and all ```[Inject]``` methods within the prefab are called as it happens when prefab instantiated in Zenject recommended way.

As an addition, this tool automatically binds all components configured in ```ZenjectBinding``` MonoBehaviour attached to the instantiated prefabs.

### How to use

To inject dependencies in runtime instantiated prefab you only need to add a ```ZenjectDynamicObject``` MonoBehaviour to the root game object of the prefab.

After instantiating of the prefab, all dependencies will be automatically resolved, by doing a lookup in the most 'local' container towards to the instantiated game object, and injected on Awake() phase before game object MonoBehaviour's will have their Awake() method called.

<img src="Documentation/dynamic_foo.png?raw=true" alt="Dynamic GameObject"/>

Moreover if you also need to bind game object MonoBehaviour's to the Zenject container, you can do this by adding a ```ZenjectBinding``` MonoBehaviour to the prefab and [configuring](https://github.com/modesttree/Zenject/blob/master/README.md#scene-bindings) it in a usual way.

<img src="Documentation/dynamic_foo_with_binding.png?raw=true" alt="Dynamic GameObject with binding"/>

###  <a name="installation"></a> Installation

To install the tool you need to follow next steps:

1. [Install](https://github.com/modesttree/Zenject/blob/master/README.md#installation) Zenject framework v5.2.0 or higher.
2. Clone this repo and copy the UnityProject/Assets/Plugins/ZenjectDynamicObject directory to your own Unity3D project.


##  <a name="service-locator-pattern"></a> Service Locator Pattern

_“Dear Friend, the author fully understands that use of the [Service Locator Pattern](https://en.wikipedia.org/wiki/Service_locator_pattern) contradicts ideas of dependency injections but this tool is not positioned as the replacement for the regular Zenject flow but as an opportunity to sometimes cut off a corner and pays more attention to the delivering happiness to players.”_

### Registering services

Service could be registered in a project container

```c#
public class SomeService : MonoBehaviour, ISomeService
{
    private void Awake()
    {
        ZenjectLocator.Container.Bind<ISomeService>().FromInstance(this).AsSingle();
        ...
    }
    ...
}
```

or in the most 'local' container of specified context type to the target scene, game object or component.

```c#
public class SomeService : MonoBehaviour, ISomeService
{
    private void Awake()
    {
        ZenjectLocator.GetContainer(this.gameObject).Bind<ISomeService>().FromInstance(this).AsSingle();
        ...
    }
    ...
}
```

### Locating services

Similarly to services registration service could be located in a project container

```c#
public class Foo : MonoBehaviour
{
    private void OnCollisionEnter(Collision collision)
    {
        ZenjectLocator.Container.Resolve<ISomeService>.PerformTask(this.gameObject);
        ...
    }
}
```

or in the most 'local' container.

```c#
public class Foo : MonoBehaviour
{
    private void OnCollisionEnter(Collision collision)
    {
        ZenjectLocator.GetContainer(collision.gameObject).Resolve<ISomeService>.PerformTask(this.gameObject);
        ...
    }
}
```

###  License
Zenject-DynamicObject is distributed under the MIT license. Please keep the existing headers.

###  Attribution
Developer Alexandr Pereverzev - <a.v.pereverzev@gmail.com>
