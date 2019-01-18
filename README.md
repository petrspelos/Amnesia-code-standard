# Amnesia Code Standard

This is an open standard for creating custom stories and other mods for Amnesia: The Dark Descent. The standard serves to provide a unified set of development guidelines and best practices.

## 🔧 Development Tools

_All development tools required are free._

The assumed tools for this standard are:

- Visual Studio Code ([Download](https://code.visualstudio.com/) | [Setup Guide](https://www.youtube.com/watch?v=LL5N7NEvPBo))
    - Amnesia (HPL2) extension ([View](https://marketplace.visualstudio.com/items?itemName=Spelos.HPL2Syntax))
    - C++ extension ([View](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools))
        - Associated extensions with C++ syntax ([Guide](#file-association-guide))
- Amnesia Preprocessor ([GitHub](https://github.com/petrspelos/amnesia-preprocessor))

## 📁 Custom Story structure
The directory of a custom story should contain all non-empty folders needed for the full and intended playthrough of the mod.

These folders must (according to this standard) mirror the original game's internal folder structure. That is **lowercase letters of the English alphabet** and an underscore `_` for word separation.

✅ **DO** name your subfolder
```
static_objects
```
❌ **DON NOT** name your subfolder
```
Static Objects
```

> This makes it easier for the game to properly load your resources. Mirroring the original game's structure also ensures any potential future updates will not break your mod, unless they also change the original game's directory structure.

### File naming
:warning: **Failure to understand this section might lead to game-breaking bugs!**

When loading custom stories, Amnesia follows the alphabetical order of the custom story's folder names.
Let's assume we have the following custom stories installed:

```
📁custom_stories
    ├ 📁A_CustomStory
    │   └📁maps
    │       └📄Cellar.map
    ├ 📁B_CustomStory
    │   └📁maps
    │       └📄Cellar.map
    └ 📁C_CustomStory
```

No matter what their name inside `custom_story_settings.cfg` is, they will be loaded in that order. That is Alphabetically according to their folder names.

If `A_CustomStory` has a map called `Cellar.map` and `B_CustomStory` has a map of the same name inside its folder, `A_CustomStory/maps/Cellar.map` might be loaded even for `B_CustomStory`.

To avoid this issue, a unique custom story prefix is recommended for all custom assets. This prefix can be derived from the project's name, or it can be completely random, it does, however, need to be unique enough to minimize a chance of a different custom story having files of the same name.

An example prefix for a custom story called `The Adventures of Phill` might be `TAoP` and the custom files might be something like `TAoP_Village.map`, `TAoP_Dialogue01_Kate_00.ogg`, etc.

This standard demands the use of a single prefix for **all custom files and maps.**

:warning: An exception is with standard imported assets such as AMFP models. If there are no changes made to the asset, it can retain its original name, since a collision would not matter with these assets.

## extra_english.lang
The `extra_english.lang` file should adhere to the common XML schema rules (with the exception of XML header). The main tag names must be typed in all capital letters.

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

With each new depth the indentation increases by **four spaces**.

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

Breaking inside an Entry tag is not allowed.
```xml
    <Entry Name="MyEntry">Example of what[br]
not to do[br]
inside an entry.</Entry>
```

## HPS Files

The `.hps` files are the script files executing when a map is launched. The engine dictates the name is exactly the same as the map it's for. The only difference being the `.hps` extension.

In order to not be limited by this single file limitation, the use of **Amnesia Preprocessor** is required. The preprocessor takes in Source HPS (`.shps`) and Include HPS (`.ihps`) files and generates the final `.hps` code capable of executing.

What follows is a couple of recommendations in terms of file separation and structure of your `.ihps` and `.shps` code.

### General code structure

In order to keep the source code readable and clean, no line should be longer than **80 columns**.

### Preloading sound and particle systems

The Amnesia engine has to read and parse `.snt` and `.ps` files. This procedure usually takes longer than a game's tick. This results in a small stutter whenever these files are used.

To get rid of this stutter, a `PreloadSound` and `PreloadParticleSystem` calls need to be executed in one of the entry methods.

Commonly a new function called `Preload` is created containing all of the preload calls.

```cs
void Preload()
{
    PreloadSound("__.snt");
    PreloadSound("__.snt");
    PreloadParticleSystem("__.ps");
    PreloadParticleSystem("__.ps");
}
```

Keeping track of this function and making sure it has all necessary files used while not containing those that are not used is a common source of human errors.

**Amnesia Preprocessor**, however, generates this method if none is declared. Therefore, you should never declare a `Preload` function inside a `.shps` or `.ihps` file.

### Banner comments

Banner comments are a way of conveying header-like information for all sorts of purposes.

You might want to use banner comments for declaring sections of your code, for example.

The decoration of a banner comment should be less than 80 columns (characters). That is two forward slashes (`/`) a single space and 76 dashes `=`.

This visually promotes the enforced line-length by creating a reference point. Since anything longer than the banner decoration line is failing the line length rule.

```cs
// ============================================================================
// Banner Comment
// ============================================================================

```

### Include directive

With **Amnesia Preprocessor**, an `#include` directive is allowed inside `.shps` and `.ihps` files.
What this directive does is it includes contents of a mentioned `.ihps` file.

This enables us to separate our code into reusable pieces.

For example:

```cs
// These are contents of: myScares.ihps
void ExecuteScare()
{
    // ...
}
```

```cpp
// These are contents of: map_01.shps
#include "myScares.ihps"

void OnEnter()
{
    ExecuteScare(); // is a valid call
}
```

To read more about the include directive see the guide [HERE](https://github.com/petrspelos/amnesia-preprocessor/blob/master/docs/examples/CustomStory.md).

### Functions

All function names have to adhere to [PascalCase format](http://wiki.c2.com/?PascalCase).

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

Functions should do only one thing. It's better to create multiple small and reusable functions rather than a single large one.

### For loop

Due to the way HPL2 is built, when iterating through an array using a for loop, the datatype must be an unsigned integer. Other types of for loop may use the standard signed integer.

```cs
for(uint i = 0; i < usedSounds.length(); i++) { PreloadSound(usedSounds[i]) };
```

#### For loop whitespace

Whitespaces in for loops are also strict.

```cs
// (!) INCORECT WHITESPACE USAGE (!)
for(int i=0;i<=2;i++)
{
}

// CORRECT
for(int i = 0; i <= 2; i++)
{
}
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

Additionally if statements and for loops must always have a body (`{}`) even if a single line syntax is used.

This is because when an extension needs to be made, not having the body might result in an accidental bug or misunderstanding.

```cs
// (!) Incorrect (!)
if(condition) FunctionCall();

// (!) Incorrect (!)
if(condition)
    FunctionCall();

// Correct
if(condition) { FunctionCall(); }

// Correct
if(condition)
{ 
    FunctionCall();
}
```

**:warning: Exception**

The only exception to the switch statement rule is the case where it greatly reduces the code's size and improves readability.

For example returning a random string literal by a random integer.

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

For every parameter specified, there should be a comment with either the parameter's name or a description. This also avoids the so called "[Boolean trap](https://ariya.io/2011/08/hall-of-api-shame-boolean-trap)" where two booleans are provided and the user has no idea what they represent.

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

This enum, should not be redefined, instead, it should be included using a Preprocessor directive from a common include file.

### Timer Switch

Timer switch is a higher level structure typically used for cutscenes and other timed events.

```cs
void MyTimerSwitch(string t)
{
    int stage = GetLocalVarInt("MyTimerSwitch");
    AddLocalVarInt("MyTimerSwitch", 1);
    float delay = 0.0f;

    if(stage == 0)
    {
        // code here

        delay = 1.0f;
    }
    else if(stage == 1)
    {
        // code here
        delay = 1.0f;
    }
    else
    {
        return;
    }

    AddTimer("", delay, "MyTimerSwitch");
}
```

Notice the name `MyTimerSwitch` is repeated in the control local variable and the function name.

Every time a Timer Switch is called, it gets and increments the control counter, called `stage`. A timer switch can have as many stages as are needed.

Every stage can overwrite the default `delay` variable, which is in seconds and represents the delay before looping back to the timer switch's next stage.

The loop continue until it's broken with a `return;` or until it falls into the `else` branch.

### Voice over in a timer switch

Sometimes, delay isn't enough and a variable delay based on a voice over effect might be needed. For this purpose the use of a helper method should be used:

```cs
else if(stage == 1)
{
    SetEffectVoiceOverCallback("MyTimerSwitchVoiceLoop");
    AddEffectVoice(/* ... */);
    return;
}
```
where the loop function is defined as
```cs
void MyTimerSwitchLoop()
{
    AddTimer("", 0.0f, "MyTimerSwitch");
}
```
this makes sure after a voice over is done, the timerSwitch will trigger again.
:warning: make sure the `return;` statement is always included at the end of such loop, otherwise, the timer switch will continue its execution despite the voice over.

### Module structure

A module in Amnesia is a source code file of either `.shps` or `.ihps` type that contains functions and variables that are in some way related.

For example, all functions extending the Sound API of Amnesia should be in the same module. Perhaps even called `AmnesiaSoundExtensions.ihps`.

A special kind of a module, is a **map controller**, this is a `.shps` named after an existing `.map` files that contains all the necessary directives and boilerplate code to compile into a cohesive map.

Map controllers should be kept simple. Every instance of a Timer Switch should be delegated into a separate `.ihps` file.

## Additional Resources

### File Association Guide

To associate source files with the C++ syntax, we need to setup file associations inside Visual Studio Code.

1. To open settings, first open the Command Palette by pressing <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd>
2. Type `Open Settings` and select the option with `(JSON)` at the end.
3. If you do not see `FileAssociations` on the right side of your screen, you can add it by searching it in the left view, or manually adding the following section to your right side view:

```json
"files.associations": {
    "*.hps": "cpp",
    "*.ihps": "cpp",
    "*.shps": "cpp",
    "*.lang": "xml",
    "*.cfg": "xml"
}
```
