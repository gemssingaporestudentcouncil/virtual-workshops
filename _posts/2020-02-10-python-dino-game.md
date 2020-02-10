---
title: Build the Offline Dinosaur Game
tags: Python CLI
---

Let's build a "no internet" TRex Game with Python! We have a base project this workshop will run you through the code as well as give you the task of extending it!

The source code is here: https://github.com/terryyin/trex-game

Open it with https://repl.it/github/terryyin/trex-game

![](https://gemssingaporestudentcouncil.github.io/virtual-workshops/assets/python-dino-img/dino-game.png)

# Define the game

A 2D, Side-scrolling, infinite, 1 key control video game. It has two scenes, game and game end.

# Game Loop

The game may have multiple rounds. At the end of the game, player can press 'ESC' to quit the game or 'Enter' to do another round.

```python
if __name__ == '__main__':
    # setup the screen ....
    
    while(True):
        main_game = TRexGame(window)
        main_game.start()
        if not main_game.should_continue():
            break

def should_continue(self):
        self.window.clear()
        self.window.border(NO_BORDER)
        self.draw_at(10, 27, [
            "  ___   _   __  __ ___    _____   _____ ___ ",
            " / __| /_\ |  \/  | __|  / _ \ \ / / __| _ \\",
            "| (_ |/ _ \| |\/| | _|  | (_) \ V /| _||   /",
            " \___/_/ \_\_|  |_|___|  \___/ \_/ |___|_|_\\",
            "",
            "             FINAL_SCORE : "+str(self.score),
            "",
            "Press 'Enter' Key to Restart or 'ESC' to Quit"
        ])
        while(True):
            key_event = self.window.getch()
            if key_event is KEY_ESC:
                return False
            elif key_event is KEY_ENTER:
                return True
```

# Graphics

**FPS** - Frame Per Second

FPS means how many images (frames) the computer can render. Higher FPS makes the game feel more real, but it needs more CPU power.

From the code in the `TRexGame::start method`, can you tell what is our game's FPS?

```python
class TRexGame:
    def start(self, window):
        while(not self.is_end_of_game()):
            self.handle_controls()
            self.update_all()
            self.draw_all()
            sleep(0.06)
        sleep(2)
        return self.score
```
Each object is rendered separately. The order of the rendering (drawing) might be important.

In some more complicated games, re-rendering everything everytime could be very costly. You will need to find more smart way to re-draw only the part that is changed.

```python
def draw_all(self):
        window.clear()
        self.cloud.draw()
        self.ground.draw()
        self.cacti.draw()
        self.trex.draw()
        self.draw_score()
```

# Sprites / Actors / Game Objects

In computer graphics, a sprite is a two-dimensional bitmap that is integrated into a larger scene, most often in a 2D video game.

![](https://gemssingaporestudentcouncil.github.io/virtual-workshops/assets/python-dino-img/sprites.png)

Our Sprites (`sprites.py`):

``` python
TREX_SPRITES = [
    [ # stride
        "   =o===",
        "   ==www",
        ", |||-",
        "\||||",
        "  / \\",
    ],
    [ # legs closed
        "   =o===",
        "   ==www",
        ", |||-",
        "\||||",
        "  \ /"
    ],
    [ # jumping
        "   =-===",
        "   ==www",
        ", |||-",
        "\||||",
        "  L L"
    ],
    [ # dead
        "   =X===",
        "   ==www",
        ", |||-",
        "\||||",
        "  L L"
    ]
]
```

# Animation

Before each frame, or base on time, the state of all the game objects are updated. After the states are updated, the objects are rendered again according to the new state.

**Advice: one thing at a time.**

- Don't render when update states (data)
- Don't change states when rendering
- Keep the code separate

``` python
class TRexGame:
    def update_all(self):
        self.score += 1
        speed = self.score//200+3
        self.ground.update(speed)
        self.cacti.update(speed)
        self.cloud.update()
        self.trex.update()
        if self.check_collision():
            self.trex.die()
```

# Background && Parallax scrolling

Our game is not 2D. It's 2.5D!

![](https://gemssingaporestudentcouncil.github.io/virtual-workshops/assets/python-dino-img/parallax_scroll.gif)

# Control

``` python
def handle_controls(self):
        if window.getch() is KEY_SPACEBAR:
            self.trex.jump()
```

We tell the TRex to jump. The TRex will change its state and remember that it started to jump.

``` python
class Trex:
    #...
    def jump(self):
        self.jumping = True

    def update(self):
        self.frame = (self.frame + 1) % 2
        if self.jumping:
            jmp = [-3,-2,-1,0, 0,1,2,3]
            self.y = self.y + jmp[self.jump_state]
            self.jump_state += 1
            if self.y >= GND_Y:
                self.y = GND_Y
                self.jumping = False
                self.jump_state = 0
```

# Scoring

Scoring is one of the most important things with video games. In our game the scoring is easy, for every time frame the TRex survives the player will get 1 score. For every 100 score, the player gets 1 level up. Can you find this in the code?

# Collision detection

Collision detection is quite often needed in all sorts of video games. Unless the game objects are on different layers, there's a chance two game objects bump into each other. The result might be one of the game objects dies, loses health, etc. Sometimes it also change the moving of the game objects, for example in a Pool game.

``` python
class TRexGame:
    def check_collision(self):
        return self.trex.check_collision(self.cacti.get_first_pos())

class TRex:
    def check_collision(self, object_pos):
        if (abs(object_pos[1]-self.x-4)<3) and (abs(object_pos[0]-self.y)<2):
            return True
        return False
```

# Your Task

Add pterodactyls (birds) to the game.

The pterodactyls:
- Like the cacti, appear randomly from the right side of the screen.
- Some pterodactyls fly high and some fly low. They don't change their altitude.
- Collision with the pterodactyl will kill TRex
- TRex should be able to duck (with the down key) to avoid the high flying pterodactyl

-----

This workshop was created by [Terry Yin](https://github.com/terryyin) for GEMS Singapore's Innovation Week 2020. Thank you Terry for your support!