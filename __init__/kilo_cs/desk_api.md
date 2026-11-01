Lang Kilo_CS v0.3
Permissions Allowed: read, execute
Permissions Denied: create, edit
Scope: file
Load now, run on trigger

#One entry per app $AGENT starts, keyed by a short name (e.g. "chrome").
#Fields: pid, win_id, opened
var $INTERFACE = {}
var $FOCUS = ""
var $DESK_SIZE = []    #real screen size, set by getDeskSize

<secret>
Run every $PROMPT on the box shell. Prompt Tools never run anything. They only return text.
</secret>

#Start Process
Trigger: when asked to start terminal
startApp("terminal", getTerminal())

Trigger: when asked to start chrome
startApp("chrome", getStart('box-chrome --new-window '.$URL))

Trigger: when asked to start files
startApp("files", getStart('thunar '.$PATH))

startApp ($NAME="", $PROMPT="") {
 Send $PROMPT to the terminal, read back pid and win
 $INTERFACE[$NAME] = {"pid": pid, "win_id": win, "opened": now}
 $FOCUS = $NAME
}

closeProcess ($NAME=$FOCUS) {
 Send getClose($INTERFACE[$NAME]["pid"])
 Remove $NAME from $INTERFACE
 IF $FOCUS = $NAME THEN
  $FOCUS = ""
 END IF
}

#Process Actions
focusWindow ($NAME="") {
 Send getFocus($INTERFACE[$NAME]["win_id"])
 $FOCUS = $NAME
}

sendKeys ($KEYS="", $NAME=$FOCUS) {
 IF $FOCUS != $NAME THEN
  focusWindow($NAME)
 END IF
 Send getKeys($KEYS)
}

typeText ($TEXT="", $NAME=$FOCUS) {
 IF $FOCUS != $NAME THEN
  focusWindow($NAME)
 END IF
 Send getType($TEXT)
}

#Desk Size
getDeskSize () {
 IF $DESK_SIZE is empty THEN
  Send getSize(), read back width and height
  $DESK_SIZE = [width, height]
 END IF
 Report "Desk: ".$DESK_SIZE[0]."x".$DESK_SIZE[1].". Done."
}

#Prompt Tools
getTerminal () {
 $PROMPT = 'xfce4-terminal --disable-server >/dev/null 2>&1 & PID=$!; for i in 1 2 3 4 5; do WIN=$(xdotool search --pid $PID 2>/dev/null | tail -1); [ -n "$WIN" ] && break; sleep 1; done; echo "pid=$PID win=$WIN"'
 return $PROMPT
}

#If the launcher exits without a window (box-chrome), take the active window and its real pid.
getStart ($CMD="") {
 $PROMPT = $CMD.' >/dev/null 2>&1 & PID=$!; for i in 1 2 3 4 5; do WIN=$(xdotool search --pid $PID 2>/dev/null | tail -1); [ -n "$WIN" ] && break; sleep 1; done; if [ -z "$WIN" ]; then WIN=$(xdotool getactivewindow); PID=$(xdotool getwindowpid $WIN); fi; echo "pid=$PID win=$WIN"'
 return $PROMPT
}

getFocus ($WIN="") {
 $PROMPT = 'xdotool windowactivate --sync '.$WIN
 return $PROMPT
}

getKeys ($KEYS="Return") {
 $PROMPT = 'xdotool key --clearmodifiers '.$KEYS
 return $PROMPT
}

getType ($TEXT="") {
 $PROMPT = 'xdotool type --delay 20 "'.$TEXT.'"'
 return $PROMPT
}

getClose ($PID="") {
 $PROMPT = 'kill '.$PID
 return $PROMPT
}

getSize () {
 $PROMPT = 'xdotool getdisplaygeometry'
 return $PROMPT
}
