**Status:** Maintenance (expect bug fixes and minor updates)

# Procgen Benchmark

16 simple-to-use procedurally-generated environments which provide a direct measure of how quickly a reinforcement learning agent learns generalizable skills.  The environments run at high speed (thousands of steps per second) on a single core.

These environments are associated with the paper [Leveraging Procedural Generation to Benchmark Reinforcement Learning](https://openai.com/blog/procgen-benchmark/) [(citation)](#citation).

Supported platforms:

- Windows 10
- macOS 10.14 (Mojave), 10.15 (Catalina)
- Linux (manylinux2010)

Supported Pythons:

- 3.6 64-bit
- 3.7 64-bit
- 3.8 64-bit

Supported CPUs:

- Must have at least AVX

## Installation

First make sure you have a supported version of python:

```
# run these commands to check for the correct python version
python -c "import sys; assert (3,6,0) <= sys.version_info <= (3,9,0), 'python is incorrect version'; print('ok')"
python -c "import platform; assert platform.architecture()[0] == '64bit', 'python is not 64-bit'; print('ok')"
```

To install the wheel:

```
pip install procgen
```

To try an environment out interactively:

```
python -m procgen.interactive --env_name coinrun
```

The keys are: left/right/up/down + q, w, e, a, s, d for the different (environment-dependent) actions.  Your score is displayed as "episode_return" on the right.  At the end of an episode, you can see your final "episode_return" as well as "level_completed" which will be `1` if you successfully completed the level.

To create an instance of the [gym](https://github.com/openai/gym) environment:

```
import gym
env = gym.make("procgen:procgen-coinrun-v0")
```

To create an instance of the vectorized environment:

```
from procgen import ProcgenEnv
venv = ProcgenEnv(num_envs=1, env_name="coinrun")
```

The environment uses the [`VecEnv`](https://github.com/openai/baselines/blob/master/baselines/common/vec_env/vec_env.py#L29) interface from [`baselines`](https://github.com/openai/baselines), `baselines` is not a dependency of this library.

## Environments

The observation space is a box space with the RGB pixels the agent sees in a numpy array of shape (64, 64, 3).  The expected step rate for a human player is 15 Hz.

The action space is `Discrete(15)` for which button combo to press.  The button combos are defined in [`env.py`](procgen/env.py).

If you are using the vectorized environment, the observation space is a dictionary space where the pixels are under the key "rgb".

Here are the 16 environments:

| Image | Name | Description |
| --- | --- | --- |
| <img src="screenshots/bigfish.png" width="200px"> | `bigfish` | The player starts as a small fish and becomes bigger by eating other fish. The player may only eat fish smaller than itself, as determined solely by width. If the player comes in contact with a larger fish, the player is eaten and the episode ends. The player receives a small reward for eating a smaller fish and a large reward for becoming bigger than all other fish, at which point the episode ends. 
| <img src="screenshots/bossfight.png" width="200px"> | `bossfight` | The player controls a small starship and must destroy a much bigger boss starship. The boss randomly selects from a set of possible attacks when engaging the player. The player must dodge the incoming projectiles or be destroyed. The player can also use randomly scattered meteors for cover. After a set timeout, the boss becomes vulnerable and its shields go down. At this point, the players projectile attacks will damage the boss. Once the boss receives a certain amount of damage, the player receives a reward, and the boss re-raises its shields. If the player damages the boss several times in this way, the boss is destroyed, the player receives a large reward, and the episode ends.
| <img src="screenshots/caveflyer.png" width="200px"> | `caveflyer` | The player must navigate a network of caves to reach the exit. Player movement mimics the Atari game “Asteroids”: the ship can rotate and travel forward or backward along the current axis. The majority of the reward comes from successfully reaching the end of the level, though additional reward can be collected by destroying target objects along the way with the ship's lasers. There are stationary and moving lethal obstacles throughout the level.
| <img src="screenshots/chaser.png" width="200px"> | `chaser` | Inspired by the Atari game “MsPacman”. Maze layouts are generated using Kruskal’s algorithm, and then walls are removed until no dead-ends remain in the maze. The player must collect all the green orbs. 3 large stars spawn that will make enemies vulnerable for a short time when collected. A collision with an enemy that isn’t vulnerable results in the player’s death. When a vulnerable enemy is eaten, an egg spawns somewhere on the map that will hatch into a new enemy after a short time, keeping the total number of enemies constant. The player receives a small reward for collecting each orb and a large reward for completing the level.
| <img src="screenshots/climber.png" width="200px"> | `climber` | A simple platformer. The player must climb a sequence of platforms, collecting stars along the way. A small reward is given for collecting a star, and a larger reward is given for collecting all stars in a level. If all stars are collected, the episode ends. There are lethal flying monsters scattered throughout the level.
| <img src="screenshots/coinrun.png" width="200px"> | `coinrun` | A simple platformer. The goal is to collect the coin at the far right of the level, and the player spawns on the far left. The agent must dodge stationary saw obstacles, enemies that pace back and forth, and chasms that lead to death. Note that while the previously released version of CoinRun painted velocity information directly onto observations, the current version does not. This makes the environment significantly more difficult.
| <img src="screenshots/dodgeball.png" width="200px"> | `dodgeball` | Loosely inspired by the Atari game “Berzerk”. The player spawns in a room with a random configuration of walls and enemies. Touching a wall loses the game and ends the episode. The player moves relatively slowly and can navigate throughout the room. There are enemies which also move slowly and which will occasionally throw balls at the player. The player can also throw balls, but only in the direction they are facing. If all enemies are hit, the player can move to the unlocked platform and earn a significant level completion bonus.
| <img src="screenshots/fruitbot.png" width="200px"> | `fruitbot` | A scrolling game where the player controls a robot that must navigate between gaps in walls and collect fruit along the way. The player receives a positive reward for collecting a piece of fruit, and a larger negative reward for mistakenly collecting a non-fruit object. Half of the spawned objects are fruit (positive reward) and half are non-fruit (negative reward). The player receives a large reward if they reach the end of the level. Occasionally the player must use a key to unlock gates which block the way.
| <img src="screenshots/heist.png" width="200px"> | `heist` | The player must steal the gem hidden behind a network of locks. Each lock comes in one of three colors, and the necessary keys to open these locks are scattered throughout the level. The level layout takes the form of a maze, again generated by Kruskal's algorithm. Once the player collects a key of a certain color, the player may open the lock of that color. All keys in the player's possession are shown in the top right corner of the screen.
| <img src="screenshots/jumper.png" width="200px"> | `jumper` | A platformer with an open world layout. The player, a bunny, must navigate through the world to find the carrot. It might be necessary to ascend or descend the level to do so. The player is capable of “double jumping”, allowing it to navigate tricky layouts and reach high platforms. There are spike obstacles which will destroy the player on contact. The screen includes a compass which displays direction and distance to the carrot. The only reward in the game comes from collect the carrot, at which point the episode ends.
| <img src="screenshots/leaper.png" width="200px"> | `leaper` | Inspired by the classic game “Frogger”. The player must cross several lanes to reach the finish line and earn a reward. The first group of lanes contains cars which must be avoided. The second group of lanes contains logs on a river. The player must hop from log to log to cross the river. If the player falls in the river, the episode ends.
| <img src="screenshots/maze.png" width="200px"> | `maze` | The player, a mouse, must navigate a maze to find the sole piece of cheese and earn a reward. Mazes are generated by Kruskal's algorithm and range in size from 3x3 to 25x25. The maze dimensions are uniformly sampled over this range. The player may move up, down, left or right to navigate the maze.
| <img src="screenshots/miner.png" width="200px"> | `miner` | Inspired by the classic game “BoulderDash”. The player, a robot, can dig through dirt to move throughout the world. The world has gravity, and dirt supports boulders and diamonds. Boulders and diamonds will fall through free space and roll off each other. If a boulder or a diamond falls on the player, the game is over. The goal is to collect all the diamonds in the level and then proceed through the exit. The player receives a small reward for collecting a diamond and a larger reward for completing the level.
| <img src="screenshots/ninja.png" width="200px"> | `ninja` | A simple platformer. The player, a ninja, must jump across narrow ledges while avoiding bomb obstacles. The player can toss throwing stars at several angles in order to clear bombs, if necessary. The player's jump can be charged over several timesteps to increase its effect. The player receives a reward for collecting the mushroom at the end of the level, at which point the episode terminates.
| <img src="screenshots/plunder.png" width="200px"> | `plunder` | The player must destroy enemy pirate ships by firing cannonballs from its own ship at the bottom of the screen. An on-screen timer slowly counts down. If this timer runs out, the episode ends. Whenever the player fires, the timer skips forward a few steps, encouraging the player to conserve ammunition. The player must take care to avoid hitting friendly ships. The player receives a positive reward for hitting an enemy ship and a large timer penalty for hitting a friendly ship. A target in the bottom left corner identifies the color of the enemy ships to target.
| <img src="screenshots/starpilot.png" width="200px"> | `starpilot` | A simple side scrolling shooter game. Relatively challenging for humans to play since all enemies fire projectiles that directly target the player. An inability to dodge quickly leads to the player's demise. There are fast and slow enemies, stationary turrets with high health, clouds which obscure player vision, and impassable meteors.

## Environment Options

* `env_name` - Name of environment, or comma-separate list of environment names to instantiate as each env in the VecEnv.
* `num_levels` - The number of unique levels that can be generated. Set to 0 to use unlimited levels.
* `start_level` - The lowest seed that will be used to generated levels. 'start_level' and 'num_levels' fully specify the set of possible levels.
* `paint_vel_info` - Paint player velocity info in the top left corner. Only supported by certain games.
* `use_generated_assets` - Use randomly generated assets in place of human designed assets.
* `debug_mode` - A useful flag that's passed through to procgen envs. Use however you want during debugging.
* `center_agent` - Determines whether observations are centered on the agent or display the full level. Override at your own risk.
* `use_sequential_levels` - When you reach the end of a level, the episode is ended and a new level is selected.  If `use_sequential_levels` is set to `True`, reaching the end of a level does not end the episode, and the seed for the new level is derived from the current level seed.  If you combine this with `start_level=<some seed>` and `num_levels=1`, you can have a single linear series of levels similar to a gym-retro or ALE game.
* `distribution_mode` - What variant of the levels to use, the options are `"easy", "hard", "extreme", "memory", "exploration"`.  All games support `"easy"` and `"hard"`, while other options are game-specific.  The default is `"hard"`.  Switching to `"easy"` will reduce the number of timesteps required to solve each game and is useful for testing or when working with limited compute resources.

## Notes

* You should depend on a specific version of this library (using `==`) for your experiments to ensure they are reproducible.  You can get the current installed version with `pip show procgen`.
* This library does not require or make use of GPUs.
* While the library should be thread safe, each individual environment instance should only be used from a single thread.
* Calling `reset()` early will not do anything, please re-create the environment if you want to reset it early.

# Build from Source

If you want to change the environments or create new ones, you should build from source.  You can get miniconda from https://docs.conda.io/en/latest/miniconda.html if you don't have it, or install the dependencies from [`environment.yml`](environment.yml) manually.

```
git clone git@github.com:openai/procgen.git
cd procgen
conda env update --name procgen --file environment.yml
conda activate procgen
pip install -e .
# this should say "building procgen...done"
python -c 'from procgen import ProcgenEnv; ProcgenEnv(num_envs=1, env_name="coinrun")'
# this should create a window where you can play the coinrun environment
python -m procgen.interactive
```

The environment code is in C++ and is compiled into a shared library loaded by python using a C interface based on [`libenv`](https://github.com/cshesse/libenv).  The C++ code uses [Qt](https://www.qt.io/) for drawing.

# Create a new environment

* Copy [`src/games/bigfish.cpp`](procgen/src/games/bigfish.cpp) to `src/games/<name>.cpp`
* Replace `BigFish` with `<name>` and `"bigfish"` with `"<name>"` in your cpp file
* Add `src/games/<name>.cpp` to [`CMakeLists.txt`](procgen/CMakeLists.txt)
* Run `python -m procgen.interactive --env_name <name>` to test it out

# Changelog

See [CHANGES](CHANGES.md) for changes present in each release.

# Contributing

See [CONTRIBUTING](CONTRIBUTING.md) for information on contributing.

# Assets

See [ASSET_LICENSES](ASSET_LICENSES.md) for asset license information.

# Citation

Please cite using the following bibtex entry:

```
@article{cobbe2019procgen,
  title={Leveraging Procedural Generation to Benchmark Reinforcement Learning},
  author={Cobbe, Karl and Hesse, Christopher and Hilton, Jacob and Schulman, John},
  year={2019}
}
```