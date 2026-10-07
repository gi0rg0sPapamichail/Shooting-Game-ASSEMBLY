# Shooting Game - Assembly

A simple 2D shooting game developed entirely in 8086 Assembly as part of an Assembly programming project.

The game runs in a DOS environment and uses VGA Mode 13h to create a 320×200 pixel, 256-color graphical game. The player controls a spaceship at the bottom of the screen and must shoot the descending enemy before it reaches the player.

## Game Overview

The objective of the game is to survive while shooting the enemy as it moves horizontally across the screen and gradually descends toward the player.

The enemy starts at the top-center of the screen and continuously moves from side to side. Whenever it reaches one of the horizontal boundaries, it changes direction and moves one pixel downward.

The player can move horizontally and fire missiles toward the enemy.

The game ends when the enemy reaches the player's level.

The score represents the amount of movement made by the enemy during the game.

## Features

- 320×200 VGA graphics using 256 colors
- Player-controlled spaceship
- Horizontal player movement
- Enemy with automatic horizontal movement
- Enemy changes direction when reaching the screen boundaries
- Enemy gradually moves downward
- Missile shooting system
- Up to 6 active missiles at the same time
- Missile/enemy collision detection
- Timer-based movement using the BIOS system timer
- Score tracking
- Game-over screen
- Keyboard controls
- Direct access to VGA video memory
- Written in 8086 Assembly

## Controls

| Key | Action |
|---|---|
| Left Arrow | Move player left |
| Right Arrow | Move player right |
| Space | Shoot a missile |
| Q | Quit the game |

At the beginning of the game, press any key to start.

## How the Game Works

### Player

The player is positioned near the bottom of the screen and can move horizontally.

The player is represented by a small triangular shape created using multiple horizontal lines. Its position is stored using `player_x` and `player_y`.

The player cannot move outside the horizontal boundaries of the screen.

### Enemy

The enemy starts near the top-center of the screen.

It moves horizontally across the screen. When the enemy reaches the left or right boundary, it changes direction and moves one pixel downward.

This means that while the enemy is moving horizontally, it is also progressively getting closer to the player.

The enemy movement is controlled by the `move_enemy` procedure.

### Missiles

The player fires missiles using the `SPACE` key.

Each missile stores information about its position and timing inside a fixed-size missile array.

The game supports a maximum of 6 active missiles at any given time.

The missile system is separated into procedures for creating and moving individual or multiple missiles.

### Collision Detection

The game checks whether an active missile has reached the enemy.

The collision detection is handled by:
```

search_win_condition

```

The procedure examines the missile and enemy coordinates and determines whether their positions overlap within the defined collision range.

When a collision is detected, the game reaches the game-over state.

### Timing

Game movement is controlled using the BIOS timer through:
```

INT 1Ah

```

The timer is used to control:

- Enemy movement
- Missile movement
- Missile firing intervals

This prevents movement from being directly dependent on the speed of the main game loop.

### Score

The score is stored in the `score` variable.

The enemy's movement contributes to the score, with additional points being added as the enemy reaches the horizontal boundaries and moves downward.

When the game ends, the score is converted from its numerical representation into ASCII characters and displayed to the user.

The game-over message displays:
```

GAME OVER. Enemy moved: \<score\> pixels.

```

## Graphics

The game uses VGA Mode 13h:
```

320 × 200 pixels 256 colors

```

Graphics mode is enabled using BIOS interrupt `INT 10h`.

The VGA video memory starts at:
```

A000:0000

```

The program directly writes pixel data to this memory to draw the game objects.

The project implements its own basic drawing routines, including:

- Horizontal lines
- Vertical lines
- Player
- Enemy
- Missiles

### Graphics Initialization

The procedure:
```

setGraphicsMode

```

switches the system to VGA Mode 13h.

At the end of the game:
```

endGraphicsMode

```

returns the system to standard text mode.

## Technical Implementation

The project demonstrates several fundamental Assembly programming concepts.

### BIOS and DOS Interrupts

The program makes use of BIOS and DOS interrupts for system functionality:
```

| Interrupt | Purpose |
| --- | --- |
| `INT 10h` | Graphics mode and display |
| `INT 16h` | Keyboard input |
| `INT 1Ah` | System timer |
| `INT 21h` | DOS services |

### Direct Video Memory Access

Instead of relying on a high-level graphics library, the program writes directly to VGA memory.

The video segment is:

```
A000h
```

This allows individual pixels to be manipulated directly.

### Procedures

The game is divided into multiple Assembly procedures, each responsible for a specific part of the game.

| Procedure | Description |
| --- | --- |
| `setGraphicsMode` | Enables VGA Mode 13h |
| `endGraphicsMode` | Returns to text mode |
| `convertScoreToASCII` | Converts the score into printable ASCII |
| `add32_8` | Adds an 8-bit value to a 32-bit timer value |
| `drawhorizontalline` | Draws a horizontal line |
| `drawverticalline` | Draws a vertical line |
| `create_player` | Draws the player |
| `create_enemy` | Draws the enemy |
| `move_enemy` | Moves the enemy and updates its direction |
| `move_player` | Moves the player |
| `create_missle_in_array` | Creates and stores a missile |
| `create_missle` | Draws a missile |
| `move_single_missle` | Moves an individual missile |
| `move_multi_missles` | Updates all active missiles |
| `search_win_condition` | Checks for missile/enemy collision |

## Missile Data Structure

The game uses a fixed-size array to store missile information.

The maximum number of simultaneous missiles is defined as:

```
max_missles equ 6
```

Each missile occupies four words in the array.

The array therefore reserves enough space for six missiles and their associated data.

This allows the game to manage multiple missiles simultaneously without dynamically allocating memory.

## Project Structure

The project contains the Assembly source code and the compiled executable.

A typical structure is:

```
Shooting-Game-ASSEMBLY/
│
├── <Assembly source file>
├── program.exe
└── README.md
```

The Assembly source contains the complete game implementation, including graphics, input handling, game logic, timing, collision detection, and score handling.

## Development Environment

The game was written and assembled using **EMU8086**.

EMU8086 was used as the development environment for writing and assembling the 8086 Assembly code.

The resulting executable is then run in a DOS-compatible environment.

## Running the Game

The compiled game is intended to run in **DOSBox**, which provides a DOS environment suitable for running the generated executable.

### Requirements

- EMU8086
- DOSBox
- A compiled `program.exe`

DOSBox can be downloaded from the official website:

https://www.dosbox.com/

### 1\. Compile the Program

Open the Assembly project in EMU8086 and assemble/build the program.

Make sure that the resulting executable is available in the folder you want to run from DOSBox.

The executable used by the instructions below is:

```
program.exe
```

### 2\. Open DOSBox

Start DOSBox.

### 3\. Mount the Drive

Mount the drive containing your project:

```
mount c c:/
```

### 4\. Switch to the Mounted Drive

```
c
```

### 5\. Navigate to the Project Folder

Navigate to the folder containing the executable:

```
cd <folder containing the file>
```

For example:

```
cd Shooting-Game-ASSEMBLY
```

### 6\. Run the Game

Execute the program:

```
program.exe
```

### Complete Example

```
mount c c:/
c
cd Shooting-Game-ASSEMBLY
program.exe
```

## Game Flow

The general flow of the game is:

```
Start Program
     |
     v
Initialize Graphics
     |
     v
Create Player
     |
     v
Create Enemy
     |
     v
Press Any Key
     |
     v
Game Loop
     |
     +-------------------+
     |                   |
     v                   v
Player Input       Move Missiles
     |                   |
     +---------+---------+
               |
               v
       Check Collision
               |
          +----+----+
          |         |
         Hit      No Hit
          |         |
          v         v
      Game Over  Move Enemy
                    |
                    v
           Enemy Reaches Player?
                |        |
               Yes       No
                |        |
                v        v
           Game Over  Continue
```

## Main Game Loop

The game continuously performs the following operations:

1. Move active missiles.
2. Check for keyboard input.
3. Move the player if an arrow key is pressed.
4. Create a missile when `SPACE` is pressed.
5. Check for a missile/enemy collision.
6. Update the enemy according to the timer.
7. Check whether the enemy has reached the player's level.
8. Continue until the game ends or the player presses `Q`.

## Screenshots

Screenshots or a gameplay GIF can be added here.

For example:

```
![Gameplay](screenshots/gameplay.png)
```

## Project Information

This project was developed as an 8086 Assembly programming project and demonstrates the implementation of a simple interactive game without the use of high-level graphics or game-development libraries.

The project focuses on low-level concepts including:

- Registers and memory
- Stack-based procedure parameters
- BIOS interrupts
- DOS interrupts
- VGA graphics
- Direct video-memory manipulation
- Keyboard input
- Timer-based movement
- Arrays and structured data
- Collision detection
- Coordinate-based game logic

## Author

**Giorgos Papamichail**
