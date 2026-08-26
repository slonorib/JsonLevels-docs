# Json Levels plugin documentation

## Table of Contents
1. [Overview](#overview)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [JSON → Level](#json--level)
   - [C++ Tutorial](#c-tutorial-a-bit-more-technical)
   - [Blueprints Tutorial](#blueprints-tutorial-a-bit-less-technical)
5. [Level → JSON](#level--json)
6. [Examples](#examples)
7. [Support](#support)

## Overview
Json Levels is a plugin that helps you generate text-based (JSON) representations of levels via an editor tool, then construct levels from that text at runtime.

It's perfect for casual games with multiple levels (e.g. match-3, puzzles, arkanoid), but you can use it in any scenario where you need to dynamically generate actors from text data.

## Requirements
- **Supported Unreal Engine versions:** 4.27, 5.0–5.8
- **Platforms:** Windows, Mac (OSX), Linux, iOS, Android

## Installation
1. Get the plugin from [Fab](https://www.fab.com/) (or add it directly to your project's `Plugins/` folder).
2. Open your project, then go to **Edit → Plugins** and make sure **Json Levels Tools** is enabled.
3. Restart the editor if prompted.

## JSON → Level

### C++ Tutorial (a bit more technical)
Add a `UJlsGenerator` actor component to one of your game objects (e.g. game mode or game state).
It has 2 functions:

    /** Parse Json and spawn objects listed in it. Once all objects are spawned, call OnObjectsSpawned callback.
    * @param Json String representation of a Json that contains data about actors that should be spawned. */
    UFUNCTION(BlueprintCallable, Category = "JsonLevels")
    void GenerateLevel(const FString& Json);

    /** Remove all actors that implement IJlsGameplayActor interface from the scene */
    UFUNCTION(BlueprintCallable, Category = "JsonLevels")
    void ClearLevel();

It also exposes an `OnObjectsSpawned` delegate, which fires once all objects from the JSON have been spawned and the level is ready:

    /** Callback that is being called once all actors from Json are spawned by GenerateLevel() */
    UPROPERTY(BlueprintAssignable, Category="LevelToJson")
    FOnObjectsSpawned OnObjectsSpawned;

Actors that will be part of the JSON data should implement the `IJlsGameplayActor` interface and override 2 functions:

    /** Convert AActor into a string of a Json format (serialize).
     * @return String of a Json format. https://en.wikipedia.org/wiki/JSON */
    UFUNCTION(BlueprintCallable, BlueprintNativeEvent, CallInEditor, Category = "JsonLevels")
    UJlsObjectWrapper* CreateJsonFromActor();

    /** Configure AActor based on values stored in Json object associated with the actor (deserialize).
    * @param Json String representation of a Json object associated with the actor.
    * @return True if everything went well, false otherwise */
    UFUNCTION(BlueprintCallable, BlueprintNativeEvent, CallInEditor, Category = "JsonLevels")
    bool CreateActorFromJson(UJlsObjectWrapper* Json);

In `CreateJsonFromActor(...)`, construct a JSON object and add fields representing the actor to it.
In `CreateActorFromJson(...)`, read the JSON object and overwrite the actor's values using values from the JSON.

To set and get values from JSON, use the functions on `UJlsObjectWrapper`. It has getters/setters for the following data types:
* Transform
* Transform array
* Vector
* Vector array
* Bool
* Bool array
* Number (float)
* Number (float) array
* String
* String array
* Object
* Object array

Once you have an object with a `UJlsGenerator` actor component and actors implementing `IJlsGameplayActor`, you can call the `GenerateLevel(...)` function of `UJlsGenerator`, passing it the JSON generated in the [Level → JSON](#level--json) part of this tutorial.

### Blueprints Tutorial (a bit less technical)
Add a `JlsGenerator` actor component to one of your game objects (e.g. game mode or game state).
It has functions to generate a level and clear a level. The component also has a delegate that fires once the level is generated.

![JlsGenerator component with GenerateLevel and ClearLevel functions, and the OnObjectsSpawned delegate](https://github.com/slonorib/JsonLevels-docs/blob/main/Screenshots/blueprint-tutorial-1.png?raw=true)

Actors that will be part of the JSON data should implement the `JlsGameplayActor` interface and override 2 functions.
In `CreateJsonFromActor(...)`, construct a JSON object and add fields representing the actor to it.
In `CreateActorFromJson(...)`, read the JSON object and overwrite the actor's values using values from the JSON.

Say you're making a cool RPG and need to create a JSON with information about an enemy:

![CreateJsonFromActor implementation for an RPG enemy actor, building a JSON object](https://github.com/slonorib/JsonLevels-docs/blob/main/Screenshots/blueprint-tutorial-2.png?raw=true)
![CreateActorFromJson implementation reading enemy fields back out of the JSON object](https://github.com/slonorib/JsonLevels-docs/blob/main/Screenshots/blueprint-tutorial-3.png?raw=true)

Once you have an object with a `JlsGenerator` actor component and actors implementing `JlsGameplayActor`, you can call the `GenerateLevel(...)` function of `JlsGenerator`, passing it the JSON generated in the [Level → JSON](#level--json) part of this tutorial.

## Level → JSON
In the previous step, you implemented the `JlsGameplayActor` interface functions on an actor. Place that actor on the scene:

![Enemy actor placed in the level, with an Enemy Data category visible in its Details panel](https://github.com/slonorib/JsonLevels-docs/blob/main/Screenshots/blueprint-tutorial-4.png?raw=true)

Notice the **Enemy Data** tab in the actor's Details panel. These fields were added as part of the RPG enemy example — they'll be written to JSON during the JSON creation step, then read back into the actor during level creation.

Open the list of editing modes and select **JsonLevels**:

![Editor Modes dropdown with JsonLevels mode selected](https://github.com/slonorib/JsonLevels-docs/blob/main/Screenshots/blueprint-tutorial-5.png?raw=true)

Then click **Create JSON**. The generated JSON appears in the text box:

![JsonLevels editor mode panel showing the generated JSON in its text box](https://github.com/slonorib/JsonLevels-docs/blob/main/Screenshots/blueprint-tutorial-6.png?raw=true)

Remember the cube named Jake? Here's how he looks now:

    {
        "Transform":
        {
            "Location":
            {
                "x": -140.72625732421875,
                "y": -74.195877075195312,
                "z": 120.40150451660156
            },
            "Rotation":
            {
                "x": 0,
                "y": 0,
                "z": 0
            },
            "Scale":
            {
                "x": 1,
                "y": 1,
                "z": 2
            }
        },
        "Health": 50,
        "Name": "Jake",
        "Loot":
        {
            "Gold": 10,
            "Items":
            [
                "Potato",
                "Carrot",
                "Fork"
            ]
        }
    }

## Examples
The plugin ships with 2 folders of examples:
- **C++:** `Source/JsonLevels/Public/Examples/`
- **Blueprints:** `Content/Examples/`

## Support
If you need help, have a feature request, or run into trouble, reach out on [Discord](https://discord.com/channels/590187986495209472/1060275483603894322).
