Permissions:
 Actions are read, execute, create, edit, move.
 Moving a file to the MISSED folder counts as move, not edit.
 An __init__.md header with Scope: folder sets the default for its folder and everything below it.
 A *.md header with Scope: file overrides the folder default for that file and its code blocks.
 The closest rule wins: file header, then its folder's __init__.md, then each parent folder's __init__.md.
 If Allowed and Denied name the same action at the same level, Denied wins.
 If no rule names an action, it is denied.

Example: Permission Header
```
Lang Kilo_CS v0.3
Permissions Allowed: read, execute, move
Permissions Denied: create, edit
Scope: folder
Load now, run on trigger
```

Example Workspace
```
Agent Folder (workspace)
- __init__
 - kilo_cs
- CORE
- JOBS
- NOTES
- MISSED
```

Example: Method Object
```
helloKiloWorld {
Chat to User "Hello World!"
}
```

Example: Argument Method Object
```
helloKiloArgWorld ($AGENT="Kilo") {
Chat to User "Hello World! My name is ".$AGENT."."
}
```

Example: IF THEN ELSE
```
IF (statement) THEN
 (statement)
ELSE
 (statement)
END IF
```

Example: Secret Object
```
<secret>Information</secret>
<secret allowed="user">Information</secret>
```

Basic Method Examples
```
basicMethod (
Chat to User, "Basic Method"
)

basicArgMethod [$ARG="TEXT"] (
Chat to User, "Basic ".$ARG." Method."
)
```