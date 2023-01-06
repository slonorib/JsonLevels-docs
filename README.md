
# Json Levels plugin documentation

# Table of content
1. Overview
2. Blueprints
3. C++
4. Examples
5. Support

# Overview
Json Levels is a plugin that helps users to generate text-based (JSON) representations of levels via an editor tool and then construct levels based on the text via a runtime solution. 
Perfect for casual games with multiple levels (e.g. match-3, puzzles, arkanoid), but can be used in other scenarios to dynamically generate actors based on text data.

## JSON -> Level

### C++ Tutorial (a bit more technical)
Add `UJlsGenerator` actor component to one of your game objects (e.g. game mode or game state). 
It has 2 functions:

    /** Parse Json and spawn objects listed in it. Once all objects are spawned, call OnObjectsSpawned callback. 
    * @param Json String representation of a Json that contains data about actors that should be spawned. */
    UFUNCTION(BlueprintCallable, Category = "JsonLevels")  
    void GenerateLevel(const FString& Json);
    
    /** Remove all actors that implement IJlsGameplayActor interface from the scene */  
    UFUNCTION(BlueprintCallable, Category = "JsonLevels")  
    void ClearLevel();
  And `OnObjectsSpawned` delegate that is being called once all objects from JSON are spawned and the level is ready.

    /** Callback that is being called once all actors from Json are spawned by GenerateLevel() */  
    UPROPERTY(BlueprintAssignable, Category="LevelToJson")  
    FOnObjectsSpawned OnObjectsSpawned;
Actors that will be part of JSON data should implement `IJlsGameplayActor` interface and overwrite 2 functions:

    /** Convert AActor into a string of a Json format (serialize). 
     * @return String of a Json format. https://en.wikipedia.org/wiki/JSON */
    UFUNCTION(BlueprintCallable, BlueprintNativeEvent, CallInEditor, Category = "JsonLevels")  
    UJlsObjectWrapper* CreateJsonFromActor();

    /** Configure AActor based on values stored in Json object associated with the actor (deserialize). 
    * @param Json String representation of a Json object associated with the actor. 
    * @return True if everything went well, false otherwise */
    UFUNCTION(BlueprintCallable, BlueprintNativeEvent, CallInEditor, Category = "JsonLevels")  
    bool CreateActorFromJson(UJlsObjectWrapper* Json);
In `CreateJsonFromActor(...)` user should construct JSON object and add fields representing the actor to it.
In `CreateActorFromJson(...)`user should read JSON object and overwrite actor values using values from JSON.

To set and then get values from JSON, use functions from `UJlsObjectWrapper` .
 It has setters / getters for the following types of data:
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

Once you have an object with `UJlsGenerator` actor component and actors implementing `IJlsGameplayActor` you can call `GenerateLevel(...)` function of `UJlsGenerator` using a JSON that we will generate in the **Level -> JSON** part of the tutorial.

### Blueprints Tutorial (a bit less technical)
Add `JlsGenerator` actor component to one of your game objects (e.g. game mode or game state). 
It has functions to generate a level and clear a level. Also the component has a delegate that is being called once level is generated. 
<screenshot 1>
Actors that will be part of JSON data should implement `JlsGameplayActor` interface and overwrite 2 functions.
In `CreateJsonFromActor(...)` user should construct JSON object and add fields representing the actor to it.
In `CreateActorFromJson(...)`user should read JSON object and overwrite actor values using values from JSON.

Imagine, we're making a cool RPG game, and need to create a JSON with information about an enemy:
<screenshot 2>
<screenshot 3>

Once you have an object with `JlsGenerator` actor component and actors implementing `JlsGameplayActor` you can call `GenerateLevel(...)` function of `JlsGenerator` using a JSON that we will generate in the **Level -> JSON** part of the tutorial.

## Level -> JSON
During the previous step you've overwritten `JlsGameplayActor` interface function in an actor(s). Place actors on the scene
<screenshot 4>
You can also notice 'Enemy data' tab in actor details section. These fields were added as a part of RPG enemy example and will be added to JSON during the JSON creation process and then overwritten in the actor during the level creation process.
Open list of editing modes and select **JsonLevels**
<screenshot 5>
Then click 'Create JSON' button. Generated JSON will appear in the text box:
<screenshot 6>
Remember cube Jake? That's how he looks now:

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

# Examples
Plugin has 2 folders with examples:
**C++**: JsonLevels\Source\JsonLevels\Public\Examples\
**Blueprints**: JsonLevels\Content\Examples\

# Support
If you need help, have a feature request or experience troubles, please contact me: https://discord.com/channels/590187986495209472/1060275483603894322
