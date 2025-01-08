### WAR

We develop a WAR metric for cricketers. 

### Concepts and Definitions

* Replacement-Level Player: A replacement-level player represents the skill and performance level of a readily available, below-average player who can fill in at short notice. Our instantiations could be:

	* Batting: The bottom 20-25% of batters for a particular position in a particular format in terms of runs scored per match and strike rate. (For tests, we ignore the strike rate.)

	Use to derive Batting Runs Above Replacement (BRAR). BRAR = Player’s Total Runs − Replacement-Level Runs

	* Bowling: The bottom 20-25% of bowlers (of a specific kind perhaps) in a particular format in terms of bowling economy, and average.

	Use to derive Bowling Runs Above Replacement (BoRAR). 

	* No metrics for fielding as none are widely available (except for crude metrics for wicketkeepers, which are not super diagnostic).

* Runs Per Win (RPW)

* WAR = (BRAR + BoRAR)/RPW
