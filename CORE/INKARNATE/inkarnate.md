Lang Kilo_CS v0.3
Permissions Allowed: read, execute, move
Permissions Denied: create, edit
Scope: file
Load now, run on trigger

required: $WORK_SP."/__init__/kilo_cs/desk_api.md"
getDeskSize()

var $URL = "https://www.inkarnate.com/dashboard"
var $TOOL = ""

#Tools, from Cart's shortcut sheet
var $TOOLS = {
 "select": "v", "mask": "m", "brush": "b", "stamp": "o",
 "shape": "r", "line": "l", "text": "t", "note": "n"
}

#Actions, grouped by tool name. "global" works with any tool.
var $ACTIONS = {
 "global": {
  "undo": "ctrl+z", "redo": "ctrl+shift+z",
  "grid": "g", "shortcuts": "k", "right panel": "slash",
  "previous tool": "Tab",
  "layer up": "ctrl+shift+Up", "layer down": "ctrl+shift+Down",
  "value up": "shift+Up", "value down": "shift+Down"
 },
 "select": {
  "select all": "ctrl+a", "restore selection": "grave",
  "deselect": "Escape", "delete": "Delete", "copy": "ctrl+c",
  "group": "ctrl+g", "ungroup": "shift+ctrl+g", "lock": "ctrl+l",
  "bigger": "w", "smaller": "s",
  "rotate cw": "greater", "rotate ccw": "less",
  "sublayer up": "ctrl+Up", "sublayer down": "ctrl+Down",
  "layer up": "ctrl+shift+Up", "layer down": "ctrl+shift+Down"
 },
 "mask": {
  "switch mode": "x", "bigger": "w", "smaller": "s"
 },
 "brush": {
  "catalog": "f", "bigger": "w", "smaller": "s",
  "more opacity": "e", "less opacity": "d",
  "more softness": "q", "less softness": "a"
 },
 "stamp": {
  "catalog": "f", "bigger": "w", "smaller": "s",
  "more opacity": "e", "less opacity": "d",
  "bigger area": "q", "smaller area": "a",
  "more density": "alt+q", "less density": "alt+a",
  "next in set": "alt+Right", "previous in set": "alt+Left"
 },
 "text": {
  "bigger font": "w", "smaller font": "s",
  "next font": "bracketright", "previous font": "bracketleft",
  "more curve": "q", "less curve": "a"
 },
 "note": {
  "edit": "e"
 }
}

#Start Process
openInkarnate () {
 startApp("inkarnate", getStart('box-chrome --new-window '.$URL))
 Report "Inkarnate open."
}

Trigger: when asked to close inkarnate
closeInkarnate () {
 closeProcess("inkarnate")
 $TOOL = ""
}

#Tool and Actions
Trigger: when asked to pick an Inkarnate tool
setTool ( $NAME="" ) {
 IF $NAME is not in $TOOLS THEN
  Ask the user which tool, using $TOOLS.
 END IF
 sendKeys($TOOLS[$NAME], "inkarnate")
 $TOOL = $NAME
 Report "Tool: ".$NAME.". Done."
}

Trigger: when asked to do an Inkarnate action
doAction ( $ACTION="", $TIMES=1 ) {
 IF $ACTION is in $ACTIONS[$TOOL] THEN
  $KEY = $ACTIONS[$TOOL][$ACTION]
 ELSE IF $ACTION is in $ACTIONS["global"] THEN
  $KEY = $ACTIONS["global"][$ACTION]
 ELSE
  Ask the user which action, using $ACTIONS[$TOOL] and $ACTIONS["global"].
 END IF
 Repeat $KEY $TIMES times, separated by spaces, as $KEYS.
 sendKeys($KEYS, "inkarnate")
 Report "Action: ".$ACTION." x".$TIMES.". Done."
}

#Examples, need review
#Larger steps: the sheet says hold Shift for bigger steps on * entries.
exBigStep ( $ACTION="" ) {
 sendKeys("shift+".$ACTIONS[$TOOL][$ACTION], "inkarnate")
}

#Move selected: arrow keys, needs a direction and count.
exMove ( $DIR="Up", $TIMES=1 ) {
 Repeat $DIR $TIMES times as $KEYS.
 sendKeys($KEYS, "inkarnate")
}

#Zoom: mouse wheel over an approved spot, rate set with the user
var $ZOOM_SPOT = []    #approved mouse spot, empty until approved
var $ZOOM_RATE = ""    #zoom per wheel click, empty until measured

exZoom ( $DIR="in", $TIMES=1 ) {
 IF $TOOL != "select" THEN
  setTool("select")
 END IF
 IF $ZOOM_SPOT is empty THEN
  Ask the user to put the mouse where zoom is approved, or choose center.
  IF the user chose center THEN
   $ZOOM_SPOT = [$DESK_SIZE[0]/2, $DESK_SIZE[1]/2]   #center of the real screen
  ELSE
   $ZOOM_SPOT = the spot from xdotool getmouselocation
  END IF
 END IF
 $BTN = 4    #4 zooms in
 IF $DIR == "out" THEN
  $BTN = 5   #5 zooms out
 END IF
 focusWindow("inkarnate")
 Run xdotool mousemove $ZOOM_SPOT, then click --repeat $TIMES --delay 80 $BTN.
 IF $ZOOM_RATE is empty THEN
  Ask the user how much that zoomed, nudge with them, and save it to $ZOOM_RATE.
 END IF
 Report "Zoom ".$DIR." x".$TIMES.". Done."
}

#Pan: hold Spacebar and drag, needs start and end points.
exPan ( $FROM=[], $TO=[] ) {
 #xdotool keydown space, mousedown, mousemove, mouseup, keyup space.
}

#Add to selection: Ctrl+Click, needs a point on the map.
exAddSelect ( $SPOT=[] ) {
 #xdotool keydown ctrl, click at $SPOT, keyup ctrl.
}

#Rotate finer: Ctrl+Alt+Wheel. Snap rotation: Shift + drag orange circle.
exRotate ( $DIR="cw", $FINE=FALSE ) {
}

#Mask tool: a pop-up covered part of the sheet, so it may have more shortcuts.
