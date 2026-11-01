Lang Kilo_CS v0.3
Permissions Allowed: read, execute, move
Permissions Denied: create, edit
Scope: file
Load now, run on trigger

var $LIBRARY = [], var $ROW = 0
var $OPTIONS = []
var $DECK = []
var $SCORE = 0
var $ROUND = 0
var $PLAY = FALSE
 
loadDeck () {
 Read $WORK_SP."/CORE/Flash Cards" into $LIBRARY.
 For Each $BOOK in $LIBRARY {
   IF $BOOK contains folders THEN
    $OPTIONS[$ROW] = {$BOOK: []}
     For Each $B in $BOOK {
      Add $B to $OPTIONS[$ROW][$BOOK].
     }
    $ROW += 1.
  }
 Using $OPTIONS, ask the user for a Language and Deck. You can mix decks of the same language.
 Store each card in $DECK.
 $ROW = 0
 Report "Deck loaded: <count> cards."
}

playFlashcards ( $PLAY=TRUE ) {
 Set $SCORE = 0 and $ROUND = 0.
 $DECK = [], run loadDeck() first.
 Shuffle $DECK.
 WHILE $PLAY = TRUE and cards remain in $DECK {
  Chat the card image to Text.
  <secret>
   Keep the card's answer secret during the game.
  </secret>
  Wait for the player's answer.
  If the player says "stop", set $PLAY = FALSE and end the loop.
  $ROUND = $ROUND + 1
  IF their answer matches the card (ignore case and small typos) THEN
   Say "Correct" and add 1 to $SCORE.
  ELSE 
   Acknowledge the mistake, "Not quite". 
   Allow two Re-tries before scoring 0 to $SCORE, "Incorrect. But that's why we are here."
  END IF
  Move to the next card.
 }
 Set $DECK = [].
 Report "Score: $SCORE of $ROUND."
}