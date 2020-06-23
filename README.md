# chessmate
[![Build Status](https://travis-ci.org/sansona/chessmate.svg?branch=master)](https://travis-ci.org/sansona/chessmate)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
> chessmate is a framework built around ```python-chess``` that enables a programmatic approach to analyses and defining of chess engines.

---
## Installation
Requires python 3.5 and higher

```pip install chessmate```

---
## Usage
A general introduction can be seen below. A more practical demo can be found in ```Usage.ipynb```.

### Engines
The basic building block of chessmate is the engine. All engines inherit
from ```chessmate.engines.BaseEngine``` and obey an evaluate-move progression wherein the engine evaluates the current board
state by some metric and/or algorithm and returns a single move it deems best. Mathematically, an engine can be defined as ```f(boardstate) = move```

Examples of some simple engines included in ```chessmate``` are:
  1. ```Random``` - returns a random move
  2. ```PrioritizePawnMoves``` - prioritizes all moves pawn related
  3. ```CaptureHighestValue``` - prioritize capturing the highest value piece available
  4. ```ScholarsMate``` - obeys standard Scholar's Mate sequence and resigns if unsuccessful
  
Also included in the ```engines``` is the ```MiniMax``` engine, which utilizes the MiniMax algorithm to evaluate board states. The MiniMax algorithm provided comes with most bells and whistles: alpha-beta pruning, move ordering via. MVV-LVA, and a transposition table.

Since almost all chess engines can be boiled down to this basic progression, the chessmate engine schema provides a simple but powerful framework for developing and analyzing engines. More powerful chess engines (Stockfish, DeepBlue, etc) can be written to fit this schema and thus be analyzed within ```chessmate```.

### Evaluation functions
Each engine consists of an evaluation function which inherits from ```chessmate.analysis.EvaluationFunction```. An evaluation function can be defined mathematically as ```f(boardstate) = evaluation``` where evaluation is a numeric representation of the state of the board. If we expand the definition of an engine to ```f(eval_function(boardstate)) = move```, we can start optimizing engines via. the evaluation function. 

Examples of some evaluation functions included with ```chessmate``` are:
  1. ```StandardEvaluation``` - returns an evaluation based off the material difference on the board
  2. ```PiecePositionEvaluation``` - returns an evaluation based off the relative position of pieces on the board
 
Each engine is by default configured with the ```StandardEvaluation``` function but can be mapped to any evaluation function via. the ```self.evaluation_function``` attribute

### Piece values and piece value tables
Each evaluation function utilizes defined piece values and piece value tables from ```chessmate.constants.piece_values```. Piece values provide the fundamental value of a piece on a board. By defining the value of each piece under a given condition, the evaluation function can be made to prioritize certain pieces, boardstates, or strategies.

### Move ordering
```chessmate``` engines come predefined with move-ordering capabilities defined in ```heuristics.py```. Move ordering is defined as a heuristic function which is then incorporated into the engine. For example, adding SEE move ordering to the minimax algorithm is as simple as:
```
def SEE(board: chess.Board) - > List[chess.Move]:
  """ Move ordering via SEE algorithm """
  pass
  
minimax = MiniMax(color=chess.WHITE)
minimax.ordering_heuristic = SEE
```
  
---
### Game simulations
Once engines are defined, one can perform analysis via the classes available in ```chessmate.simulations```. Some example functionality includes:

Simulating a game between two engines:

```
from chessmate.simulations import ChessPlayground
from chessmate.engines import CaptureHighestValue, Random

# Setup simulated game between ScholarsMate engine on white and CaptureHighestValue engine on black.
simulation = ChessPlayground(ScholarsMate(), CaptureHighestValue())
simulation.play_game()
```

Simulating multiple games
```
# Setups multiple independently simulated games
simulation.play_multiple_games(1000)
```

One can also play directly against an engine in the IPython console:
```
playvs = PlayVsEngine(CaptureHighestValue())
playvs.play_game()
```

---
### Basic analysis

To evaluate the results of a simulation:

Since the ```ScholarsMate``` engine either successfully mates or resigns, we'd expect a small percentage of games to be won by white mating and the rest black by resignation.
```
from chessmate.utils import display_all_results
display_all_results(simulation.all_results)
```
![results](https://user-images.githubusercontent.com/17757035/82768134-f3b2b880-9de1-11ea-9b96-8a3be118fb80.png)

To view the difference in material across a game or games
```
from chessmate.utils import display_material_difference

# Use CaptureHighestvalue on white and Random engine on black
simulation = ChessPlayground(CaptureHighestValue(), Random())
simulation.play_multiple_games(10)
display_material_difference(simulation.all_material_differences, game_index=0)
```
![game_0](https://user-images.githubusercontent.com/17757035/82845850-0210db00-9e9b-11ea-8183-48958edbc418.png)

To visualize the events of a game, chessmate comes with IPython functionality to display games in the console move by move
```
from chessmate.utils import walkthrough_pgn
walkthrough_pgn(simulation.game_pgns[0])
```
![display](https://user-images.githubusercontent.com/17757035/82768462-07f7b500-9de4-11ea-83ec-97975e9e9017.png)

---
## Contributing
Contributions at all levels are welcome! I'm happy to discuss with anyone
the potential for contributions. Please see ```CONTRIBUTING.md``` for some
general guidelines and message [me](mailto:jiaming.justin.chen@gmail.com) with any questions!

---
## Meta
Jiaming Chen –  jiaming.justin.chen@gmail.com

Distributed under the GPL 3 (or any later version) license. See ``LICENSE`` for more information.

[https://github.com/sansona/chessmate](https://github.com/sansona/)
