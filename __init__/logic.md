#Permissions

##Permissions as a Concept
 Read every .md file in Kilo CS as a module. When an agent reads one, it loads that file's methods and follows its header.
 Permissions Allowed lists what a loaded module and its methods may do.
 Permissions Denied lists what they must never do, without User granted permission.
 Scope sets how far those rules reach: "file" means only this module and the methods inside it.
 A module can't widen its own permissions. The User can temporarily grant permission.
 A temporary grant of Permission lasts for one action.

##Permissions as Logic:
 Actions are read, execute, create, edit, move.
 Moving a file to the MISSED folder counts as move, not edit.
 An __init__.md header with Scope: folder sets the default for its folder and everything below it.
 A *.md header with Scope: file overrides the folder default for that file and its code blocks.
 The closest rule wins: file header, then its folder's __init__.md, then each parent folder's __init__.md.
 If Allowed and Denied name the same action at the same level, Denied wins.
 If no rule names an action, it is denied.

##Example Permission Header
Follow these rules when reading.
```
Lang Kilo_CS v0.3
Permissions Allowed: read, execute, move
Permissions Denied: create, edit
Scope: folder
Load now, run on trigger
```

##Example Workspace
Maintain workspace structure. 
Use MISSED folder rather than trash.
```
Agent Folder (from /root)
/$AGENT (workspace)
- /__init__
 - /logic.md
 - /__init__.md
 - /kilo_cs (kilo cs system)
  - /core.md
  - /mood.md
- /CORE (user add on scripts)
- /JOBS (jobs and projects)
- /NOTES (shared for logging)
- /MISSED (personal trash)
```
#Kilo CS as a Language
##Example: Comment
Comment text is not read as code. Typically, comments are treated as notes.
```
# This block is a comment. 
```

##Example: Method Object
```
helloKiloWorld {
Chat to User "Hello World!"
}
```

##Example: Argument Method Object
```
helloKiloArgWorld ($AGENT="Kilo") {
Chat to User "Hello World! My name is ".$AGENT."."
}
```

##Example: IF THEN ELSE
```
IF (statement) THEN
 (statement)
ELSE
 (statement)
END IF
```

##Example: Secret Object
Currently for game tokens. A privacy feature using elements.
Allowed defaults to "user"
```
<secret>Information</secret>
<secret allowed="user">Information</secret>
```

##Basic Method Examples
An old formatting style. Keep for examples and readability. 
Easily converts to Kilo CS.
```
basicMethod (
Chat to User, "Basic Method"
)

basicArgMethod [$ARG="TEXT"] (
Chat to User, "Basic ".$ARG." Method."
)
```
