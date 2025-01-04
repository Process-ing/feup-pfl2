# Functional and Logical Programming (PFL) Project 2

## Game_Group: Replica_7

| Name                                           | E-mail            | Contribution | Tasks Developed                                                     |
| ---------------------------------------------- | ----------------- | -----------: | ------------------------------------------------------------------- |
| Bruno Ricardo Soares Pereira de Sousa Oliveira | up202208700@up.pt |          50% | Development and documentation of **board** and **game** logic       |
| Rodrigo Albergaria Coelho e Silva              | up202205188@up.pt |          50% | Development and documentation of **input** and **interface** system |

## Installation and Execution

The project was made to run using SICStus Prolog 4.9. The process is identical for both Linux and Windows operating systems:

1. **Make sure you are using a proper terminal**: the project uses ANSI escape sequences for a better interface. In order for them to work, please use a terminal that supports these sequences:
   - **Windows**: **PowerShell** or Windows Terminal.
   - **Linux**: Any **modern terminal emulator** should work.
   > **Note**: for the interface, we also assumed that the terminal size is of about 120x40 characters, some terminal resize may be needed to correctly load the interface.
2. **Load the Project**: navigate to the project directory and load the main file by executing the following command:
   ```bash
   sicstus -l src/game.pl
   ```
3. **Start the Game**: once SICStus Prolog has loaded, start the game by calling:
   ```prolog
   play.
   ```

## Description of the Game

Replica is a two player game played using a chessboard and 12 black and 12 white flippable checkers. Players setup the game by placing the checkers on the board in opposite corners (with a 2x2 square in the corner, flanked by a 2x2 square on each side). The pieces in the very corner start the game flipped over (indicating a king). Players make one move per turn, starting with White.

On each turn, players either step, jump, or transform. All moves (even captures) must go "forward" (1 of the 3 directions towards the opponent corner). For steps and for jumps, if there is already a piece on the destination square, it is captured by replacement. For a step, the piece moves forward one square. For a jump, the piece moves in a straight line forward over friendly pieces until it reaches a square not occupied by a friendly piece. For a transform, a friendly non-king piece in line-of-sight of a friendly king gets flipped (this creates another friendly king). Only enemy pieces block line of sight.

The game is over if a player wins by getting any friendly king into the opposite corner, or wins by capturing any enemy king.

\- _description from designer (abridged)_

## Considerations for Game Extensions

For this game, we agreed that part of what makes is special is the simplicity of the rules. Some examples of additional rules were provided in the original game description, but we didn't find any of them to be particularly fitting as they tended to involve completely game-changing mechanics. We also considered prompting user input for the board size, but it couldn't be much less than 8x8 (because of the layout) and larger board sizes would only make the games less interesting as most of the moves would just be passive, so we decided against it.  

Nonetheless, we kept the game implementation flexible to allow for variable board sizes and additional rules to be added in the future with easily (although the interface might need some small adjustments). The interface is also extendable to allow the user to choose the theme and other parameters. Finally, we also implemented a third level of AI using Minimax to make the game more challenging (more details below).

## Game Logic

### Game Configuration Representation

Before the actual game starts, some configurations are needed to defined what type of game will be played. These configurations are stored stored in a `game_config` predicate that filled in the initial menus of the game. It's structure is the following:
   
```prolog
GameConfig = game_config(GameMode, Player1Info, Player2Info).
PlayerInfo = player_info(PlayerName, PlayerDifficulty).
```
where `GameMode` is an integer between 1 and 4 (1 for Human vs Human, 2 for Human vs Computer, 3 for Computer vs Human, 4 for Computer vs Computer), `PlayerName` is a string with the name of the player, and `PlayerDifficulty` is an integer between 0 and 3 (0 for Human, 1 for Easy, 2 for Medium, 3 for Hard).

The `GameConfig` is saved onto the initial `GameState` and is used throughout the game to access the name of the players and how the next move should be obtained.

### Internal Game State Representation

### Move Representation

### User Interaction

The files associated with the user interaction are the `display.pl` and `input.pl`:

- The `display.pl` file contains all predicates that print something to the screen (game title, option menus, **input prompts**, game board and other indications such as evaluation or move history). This file makes extensive use of the `ansi.pl` module developed and uses the configurations specified in the `theme.pl`, more details about this can be found below.
- The `input.pl` file contains the predicates responsible for reading user input (numbers/options, strings/names and positions/coordinates), without producing any output, in the following ways:
  - **Numbers**: read as the resulting integer from concatenating all the digits in the input string (i.e. skipping all the other characters until it reaches the end of the line)
  - **Strings**: read as the concatenation of all the characters with ASCII codes between 32-127, which includes spaces, numbers, letters and other symbols and punctuation (also skips all other characters until it reaches the end of the line)
  - **Positions**: referenced as a spreadsheet where the position is a combination of letters and numbers. Letters refer to the column, numbers to the row, and their order doesn't matter. Characters that are larger than a given coordinate range (or are irrelevant) are skipped automatically. When the coordinate only needs one character, the first valid character is considered. Otherwise, all valid symbols for a given coordinate are read and concatenated for spreadsheet-like indexing (i.e. rows: ... 9, 10, 11 ... and columns: ... Z, AA, AB ...). This was done to make the functions more flexible for larger board sizes.
    > As an example, all of the following "inputs" represent the same position (Column A, Row 8) in an 8x8 board: `a8`, `az8`, `ai8`, `8a`, `89za`, `8 _ a`, `l8-askd-jsa_das123888812312`.

When it comes to input validation, some small validations are performed by the input functions (mostly skipping unwanted characters), but on top that, the predicates from the `display.pl` that use them also make some verifications:

- **Options**: for option menus, it is checked that the number is contained between 1 and the total number of options
- **Positions**: for positions, it is checked that the position corresponds to a piece with valid moves for the current state of the board

Any errors that occur from the previous validations will simply display an error message to the user and prompt for new input from the user.

### Game Interface

### Third Level of AI (Minimax)

## Conclusions

## References
