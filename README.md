# Amnesia: The Dark Descent custom stories
## Open content standard
Anyone is free to follow this standard.

**Quick selection:**

* [File naming](#file-naming)
* [extra_english.lang](#extra_englishlang)
    * [Template](#template)
    * [Comments](#comments)
    * [Category and Entry](#category-and-entry)
    * [Indentation](#indentation)
    * [Spacing](#spacing)
* [HPS Files](#hps-files)
    * [Template](#template-1)
    * [Banners](#banners)
        * [Special banners](#special-banners)
    * [Module variables](#module-variables)
    * [Functions](#functions)
    * [Debug actions](#debug-actions)
    * [For loop](#for-loop)
    * [Switch statements](#switch-statement)
    * [Private functions](#private-functions)
    * [Calling functions with long signatures](#calling-functions-with-long-signatures)
    * [Using enums](#using-enums)
    * [Event initialization](#event-initialization)
## Custom Story structure
The directory of a custom story should contain all non-empty folders needed for the full and intended playthrough of the mod.

These folders must (according to this standard) mirror Amnesia: The Dark Descent's internal folder structure. That is **lowercase letters of the English alphabet** and an underscore `_` for word separation.

### File naming
:warning: **Failure to understand this section might lead to game-breaking bugs!**

When loading custom stories, Amnesia follows the alphabetical order of the custom story's folder names.
Let's assume we have the following custom story folders inside Amnesia's `custom_stories` directory:

`A_CustomStory`
`B_CustomStory`
`C_CustomStory`

No matter what their name inside `custom_story_settings.cfg` is, they will be loaded in that order. That is Alphabetically according to their folder names.

If `A_CustomStory` has a map called `Cellar.map` and `B_CustomStory` has a map of the same name inside its folder, `A_CustomStory/maps/Cellar.map` might be loaded even for `B_CustomStory`.

To avoid this issue, a unique custom story prefix is recommended for all custom assets. This prefix can be derived from the project's name, or it can be completely random, it does, however, need to be unique enough to minimize a chance of a different custom story having files of the same name.

An example prefix for a custom story called `The Adventures of Phill` might be `TAoP` and the custom files might be something like `TAoP_Village.map`, `TAoP_Dialogue01_Kate_00.ogg`, etc.

This standard demands a use of a single prefix for **all custom files and maps.**

## extra_english.lang
The "extra_english.lang" file should adhere to the common XML schema rules. The main tag names must be typed in all capital letters.

### Template

A bare template of this file is as follows:

```xml
<LANGUAGE>
    <RESOURCES />
    
    <CATEGORY Name="CustomStoryMain">
        <Entry Name="Description">No Description</Entry>
    </CATEGORY>

</LANGUAGE>
```

Notice the `RESOURCES` tag. While optional, leaving it out generates a warning in the logs, therefore, this standard considers it mandatory.

### Comments

Comments should aways be in the following format

```xml
<!-- My Comment -->
```

### Category and Entry

New categories and entries should be in the following format:

```xml
    <CATEGORY Name="CategoryName">
        <Entry Name="EntryName">Entry Content</Entry>
    </CATEGORY>
```

### Indentation

With each new depth the indentation increases by one `TAB`.

### Spacing

No space should be left between Entries in a single Category. An exception to this rule are tags forming pairs. These pairs of Entry tags should be separated by an empty line.

```xml
   <Entry Name="Note_HiddenNote_Name">A hidden note</Entry>
   <Entry Name="Note_HiddenNote_Text">...</Entry>

   <Entry Name="Note_OtherNote_Name">A different note</Entry>
   <Entry Name="Note_OtherNote_Text">...</Entry>
```

This is done to visually separate pairs.

Another exception is a space between Entries of different type belonging to the same Category. For example `Notes`, `Diaries` and `Quests` share the same Category (`Journal`) there should be an empty line between the last Diary and the first Quest. No line should be left blank between individual Quests, however.

## HPS Files

The ".hps" files are the script files executing when a map is launched. The engine dictates the name is exactly the same as the map it's for, the only difference being the ".hps" extension. The following recommendation aims to create an easy to navigate and understand code environment.

### Template
```cs
// ---------------------------
// Debug - dbg
// ---------------------------

// ---------------------------
// Main - g
// ---------------------------

void Preload()
{
    // TODO:
    // Implement preload logic.
}

void OnEnter()
{
    Preload();
}

void OnLeave()
{

}

void OnStart()
{
    
}
```

### Banners

Due to the engine not supporting multiple files, this standard uses banners to separate modules and code sections.

A banner can be divided into three parts.

* Decoration

* Name

* Prefix

For example the following banner:

```cs
// ---------------------------
// Oil Lab - ol
// ---------------------------
```

`Oil Lab` is the name of this banner,

`ol` is the Prefix

and the rest is decoration.

Banners only mark a beginning of a section, they never close a section and no closing banners should be created.

#### Special banners

This standard uses two reserved banner names and prefixes for the common modules. `Debug - dbg` and `Main - g`.

These special banners have its own purpose and should be defined in every .hps file as part of the standard.

```cs
// ---------------------------
// Debug - dbg
// ---------------------------
```

`Debug` banner marks the beginning of all Debug variable and function definitions used for the purpose of debugging.

All of these variables have to have the `dbg` prefix as part of the module variable definition and have to be constants.

```cs
// ---------------------------
// Main - g
// ---------------------------
```

`Main` with the prefix `g` for global, marks the beginning of the Main module. This module contains all Amnesia callback functions (OnStart, OnEnter and OnLeave) even if they are not used.

### Module variables

Modules can declare their own variables. The variables defined in each module must be directly related to the module's function.

All variables have to contain the banner's prefix. The standard naming convention is camelCase with the first later lowercase. This applies to all variable names.

All of these variables must be delcared as constants. The use of `GetLocalVar___();` and `SetLocalVar___(__, __);` is recommended for non-constant variables.

```cs
// ---------------------------
// MyFirstModule - mfm
// ---------------------------
const string mfmSoundFile = "sound_file.snt";
const string mfmEntityToMove = "MyEntity";
```

### Functions

All function names have to adhere to PascalCase format.

Function names have to be descriptive.

```cs
// (!) WRONG (!)
void MyFunc7(string entity, bool active)
{
    // Code...
}

// (i) CORRECT (i)
void ActivateEntity(string entity, bool active)
{
    // Code...
}
```

Functions should do only one thing. It's better to create multiple small and reusable function than a single large function.

### Debug actions

When defining a behavior dependent on debug variables, the debug part of the behavior must be enclosed in an XML-like comment tag.

```cs
// <DEBUG>
if(dbgFullOilStart) SetPlayerLampOil(100.0f);
// </DEBUG>
```

This is done to easily automate the removal of these Debug behaviors before release.

### For loop

Due to the way HPL2 is built, when iterating through an array using a for loop, the datatype must be an unsigned integer. Other types of for loop may use a standard integer.

```cs
for(uint i = 0; i < usedSounds.length(); i++) PreloadSound(usedSounds[i]);
```

### Switch statement

Switch statements are not allowed by this standard. The prefered alternative is an if-statement.

Instead of

```cs
switch(name)
{
   case "Jon":
      // code
      break;
   default:
      // code
      break;
}
```

use

```cs
if(name == "Jon")
{
   // code
}
else
{
   // code
}
```

### Private functions

Private functions are in the following format `_FunctionName()`. These function should not be called by a programmer. They are called by an internal system.

Example:

```cs
// Can be called by a user outside of the current module.
void DoAction()
{
   // Code
}

// Can only be called by other functions in this module.
void _DoAction()
{
   // Code
}
```

### Calling functions with long signatures

Amnesia has a number of functions with a very long signature. Especially the parameters list. The prefered way of organizing long function signatures is with the following style:

Example (Playing voiceover files):

```cs
AddEffectVoice(
    myFile,       /* Voice File */
    "",           /* Effect File */
    "MyCategory", /* Text category */
    "MyEntry",    /* Text entry */
    true,         /* Use position */
    mySoundArea,  /* Position entity */
    0.0f,         /* Minimum hearing distance */
    10.0f         /* Maximum hearing distance */
);
```

For every parameter specified, there should be a comment with either the parameter's name or a description. This also avoids the so called "Boolean trap" where two booleans are provided and the user has no idea what they represent.

### Using enums

When dealing with Amnesia's script functions, some of them require a state represented as a number. This is usually misleading and can make the code harder to read. For that purpose, these integers should be substituted with a enum.

```cpp
// declaring an enum
enum CollisionState
{
    onlyEnter = 1,
    onlyLeave = -1,
    any = 0
}

// usage in an entity callback
AddEntityCollideCallback(
    "Player",                 /* Parent Name */
    collideArea,              /* Child Name */
    "OnWalkForwardCollide",   /* Callback Function */
    true,                     /* Delete on collide */
    CollisionState::onlyEnter /* State */
);
```

### Event Initialization

When a new event requiring collision callback definition or any other type of initialization is created, its initialize method (located under the right banner) must be called from the `Main - g` module or in a place derived from its functionality.

```cs
void OnStart()
{
    SetPlayerLampOil(0.0f);
    InitializeMainQuest(); // An event being intialized in OnStart.
    InitializeEvents(); // Group of minor events being initialized.
}
```
