---
title: Persistence in Unity
description: 
author: 
ms.author: jlyons
ms.date: 2/28/2018
ms.topic: article
keywords: 
---



# Persistence in Unity

**Namespace:** *UnityEngine.WSA.VR.Persistence*\
 **Class:** *WorldAnchorStore*

The WorldAnchorStore is the key to creating holographic experiences where holograms stay in specific real world positions across instances of the application. This lets your users pin individual holograms or a workspace wherever they want it, and then find it later where they expect over many uses of your app.

## How to persist holograms across sessions

The WorldAnchorStore will allow you to persist the location of WorldAnchor's across sessions. To actually persist holograms across sessions, you will need to separately keep track of your GameObjects that use a particular world anchor. It often makes sense to create a GameObject root with a world anchor and have children holograms anchored by it with a local position offset.

To load holograms from previous sessions:
1. Get the WorldAnchorStore
2. Load app data relating to the world anchor which gives you the id of the world anchor
3. Load a world anchor from its id

To save holograms for future sessions:
1. Get the WorldAnchorStore
2. Save a world anchor specifying an id
3. Save app data relating to the world anchor along with an id

### Getting the WorldAnchorStore

We will want to keep a reference to the WorldAnchorStore around so we know we are ready to go when we want to perform an operation. Since this is an async call, potentially as soon as start up, we want to call

```
WorldAnchorStore.GetAsync(StoreLoaded);
```

StoreLoaded in this case is our handler for when the WorldAnchorStore has completed loading:

```
private void StoreLoaded(WorldAnchorStore store)
{
       this.store = store;
}
```

We now have a reference to the WorldAnchorStore which we will use to save and load specific World Anchors.

### Saving a WorldAnchor

To save, we simply need to name what we are saving and pass it in the WorldAnchor we got before when we want to save. Note: trying to save two anchors to the same string will fail (store.Save will return false). You need to Delete the previous save before saving the new one:

```
private void SaveGame()
{
       // Save data about holograms positioned by this world anchor
       if (!this.savedRoot) // Only Save the root once
       {
              this.savedRoot = this.store.Save("rootGameObject", anchor);
              Assert(this.savedRoot);
       }
}
```

### Loading a WorldAnchor

And to load:

```
private void LoadGame()
{
       // Save data about holograms positioned by this world anchor
       this.savedRoot = this.store.Load("rootGameObject", rootGameObject);
       if (!this.savedRoot)
       {
              // We didn't actually have the game root saved! We have to re-place our objects or start over
       }
}
```

We additionally can use store.Delete() to remove an anchor we previously saved and store.Clear() to remove all previously saved data.

### Enumerating Existing Anchors

To discover previously stored anchors, call GetAllIds.

```
string[] ids = this.store.GetAllIds();
for (int index = 0; index < ids.Length; index++)
{
        Debug.Log(ids[index]);
}
```

## See Also
* [Spatial anchor persistence](coordinate-systems.md#spatial-anchor-persistence)