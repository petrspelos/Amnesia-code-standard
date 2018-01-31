# Amnesia: The Dark Descent custom stories
## Open content standard
Anyone is free to follow this standard.

**Quick selection:**

* [extra_english.lang](#extra_englishlang)
    * [Template](#template)
    * [Comments](#comments)
    * [Category and Entry](#category-and-entry)
    * [Indentation](#indentation)
* [HPS Files](#hps-files)
    * [Template](#template-1)
    * [Banners](#banners)
        * [Special banners](#special-banners)
    * [Module variables](#module-variables)
    * [Functions](#functions)
    * [Debug actions](#debug-actions)
    * [For loop](#for-loop)
    * [Event initialization](#event-initialization)

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

If the variable does not change or references a static type (such as a sound file name, music file or map entity) it must be declared as a constant.

```cs
// ---------------------------
// MyFirstModule - mfm
// ---------------------------
string const mfmSoundFile = "sound_file.snt";
string const mfmEntityToMove = "MyEntity";
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
