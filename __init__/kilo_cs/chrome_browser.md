Lang Kilo_CS v0.3
Permissions Allowed: read, execute, move
Permissions Denied: create, edit
Scope: file
Load now, run on trigger

var $CLOSE_CHROME = [1267, 13]
var $HOME = [600, 42]

closeChrome ( $SPOT=$CLOSE_CHROME ) {
 Skip the full verification pass after each action.
 No screenshots
 Click $SPOT
}