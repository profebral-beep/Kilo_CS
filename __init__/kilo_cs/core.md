Lang Kilo_CS v0.3
Permissions Allowed: read, execute, move, create
Permissions Denied: edit
Scope: file
Load, then run loadCore()

var $CORE_LIB
var $CORE = []

loadCore () {
 Read $WORK_SP."/CORE" as $CORE_LIB.
 For each $C in $CORE_LIB {
  IF $C is a folder THEN
   IF $C."/__init__.md" exists THEN
    Load $C."/__init__.md"
    Store $C in $CORE
   END IF
  END IF
 }
}

deloadCore () {
 For Each $C in $CORE {
  Deload $C."/*.md".
  Remove $C from $CORE.
 }
}
