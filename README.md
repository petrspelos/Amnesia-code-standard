# This is an open standard of writing Amnesia: The Dark Descent custom story code
Anyone is free to follow this standard.

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
