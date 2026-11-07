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
startApp("chrome", getChrome($URL))

Trigger: when asked to start files
startApp("files", getStart('thunar '.q($PATH)))

startApp ($NAME="", $PROMPT="") {
 Send $PROMPT to the terminal, read back pid and win
 IF win is empty THEN Report $NAME." did not open a window. Nothing stored." and Stop.
 $INTERFACE[$NAME] = {"pid": pid, "win_id": win, "opened": now}
 $FOCUS = $NAME
}

closeProcess ($NAME=$FOCUS) {
 Send getClose($INTERFACE[$NAME]["win_id"])
 Remove $NAME from $INTERFACE
 IF $FOCUS = $NAME THEN $FOCUS = ""
}

#Process Actions
focusWindow ($NAME="") {
 Send getFocus($INTERFACE[$NAME]["win_id"])
 $FOCUS = $NAME
}

sendKeys ($KEYS="", $NAME=$FOCUS) {
 IF $FOCUS != $NAME THEN focusWindow($NAME)
 Send getKeys($KEYS)
}

typeText ($TEXT="", $NAME=$FOCUS) {
 IF $FOCUS != $NAME THEN focusWindow($NAME)
 Send getType($TEXT)
}

#Desk Size
getDeskSize () {
 IF $DESK_SIZE is empty THEN Send getSize(), read back width and height, $DESK_SIZE = [width, height]
 Report "Desk: ".$DESK_SIZE[0]."x".$DESK_SIZE[1].". Done."
}

#Prompt Tools
q ($S="") { RETURN "'".replace($S, "'", "'\''")."'" }

getTerminal () {
 $PROMPT = 'xfce4-terminal --disable-server >/dev/null 2>&1 & PID=$!; for i in 1 2 3 4 5; do WIN=$(xdotool search --pid $PID 2>/dev/null | tail -1); [ -n "$WIN" ] && break; sleep 1; done; echo "pid=$PID win=$WIN"'
 return $PROMPT
}

getStart ($CMD="") {
 $PROMPT = $CMD.' >/dev/null 2>&1 & PID=$!; for i in 1 2 3 4 5; do WIN=$(xdotool search --pid $PID 2>/dev/null | tail -1); [ -n "$WIN" ] && break; sleep 1; done; echo "pid=$PID win=$WIN"'
 return $PROMPT
}

#box-chrome hands off to a running Chrome, so take the window that is new since launch.
getChrome ($URL="") {
 $PROMPT = 'B=$(xdotool search --onlyvisible --class chrome); box-chrome --new-window '.q($URL).' >/dev/null 2>&1 & for i in 1 2 3 4 5; do sleep 1; WIN=$(xdotool search --onlyvisible --class chrome | grep -vxF "${B:-none}" | tail -1); [ -n "$WIN" ] && break; done; [ -n "$WIN" ] && PID=$(xdotool getwindowpid $WIN); echo "pid=$PID win=$WIN"'
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
 $PROMPT = 'xdotool type --delay 20 -- '.q($TEXT)
 return $PROMPT
}

getClose ($WIN="") {
 $PROMPT = 'xdotool windowclose '.$WIN
 return $PROMPT
}

getSize () {
 $PROMPT = 'xdotool getdisplaygeometry'
 return $PROMPT
}
