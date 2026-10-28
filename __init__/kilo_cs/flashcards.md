Lang Kilo_CS v0.3
Permissions Allowed: read, execute, move
Permissions Denied: create, edit
Scope: file
Load now, run on trigger

var $LIBRARY = []
var $DECK = []
var $SCORE = 0
var $ROUND = 0
var $PLAY = FALSE

Trigger: when asked to play flashcards
run playFlashcards(TRUE)
 
loadDeck {
 Set $WORK_SP."/CORE/cards" as $LIBRARY
 Ask the player the set of cards to load from $LIBRARY
 Store each card in $DECK.
 Report "Deck loaded: <count> cards."
}

playFlashcards ( $PLAY=TRUE ) {
 Set $SCORE = 0 and $ROUND = 0.
 If $DECK is empty, run loadDeck first.
 Shuffle $DECK.
 WHILE $PLAY = TRUE and cards remain in $DECK {
  Show the front of the card image in Chat, let the player see.
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