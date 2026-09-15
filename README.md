
# Daisy's Garden

A 2D game built in Python using Pygame, featuring core gameplay systems including player movement and a health bar system.

## 🚧 Status: Actively Being Redeveloped

This project was originally built with a team of 3 in Fall 2024. I'm currently redeveloping it solo to expand and improve on the original build. **The code in this repo is a work in progress and may not run cleanly right now** — here's what I'm actively working on:

- [ ] Rebuilding the movement system for smoother, more responsive controls
- [ ] Adding a start screen
- [ ] Developing new levels
- [ ] Fixing runtime issues so the game runs end-to-end

## About the Original Build (Fall 2024)

Built collaboratively with a team of 3. Core systems implemented:
- Player movement
- Health bar system

## Tech Stack

- **Language:** Python
- **Library:** Pygame

## Why I'm Revisiting This

I wanted to apply skills I've picked up since the original build — in gameplay systems design, debugging, and project scoping — to take this project further than the original scope and get it to a fully playable state.

---

*Last updated: September 2026*

# PyCharm files 
place the following  image file into directory in PyCharm (preferrably) for code to run:
![vines](https://github.com/Aang33/Daisy-s-Garden/assets/166956027/7e582a49-f527-4bed-b976-493bd85c18d8)
![background](https://github.com/Aang33/Daisy-s-Garden/assets/166956027/5a1b737d-bed6-4fd5-a931-fafc91ce8dc8)
![pixel_daisy](https://github.com/Aang33/Daisy-s-Garden/assets/166956027/589106b4-ca7b-4d60-b3b6-a40087178a1a)
![smaller_heart](https://github.com/Aang33/Daisy-s-Garden/assets/166956027/9ba57fac-a7b4-45ff-acec-0912269a7f07)



# start
import random 
import time
import turtle 

turtle.register_shape("smaller_heart.gif") # this adds images into the file of the game so that it can be used 

# settings for cannon, laser and aliens
# this sets the frame rate for the game
FRAME_RATE = 30  # Frames per second  
TIME_FOR_1_FRAME = 1 / FRAME_RATE  # Seconds

# this sets up the features of the game, there timing, and there size 
CANNON_STEP = 10
LASER_LENGTH = 20
LASER_SPEED = 60
ALIEN_SPAWN_INTERVAL = 1  # Seconds
ALIEN_SPEED = 2.5
MAX_HEALTH = 3  # Maximum health value for the player

# set up windows for initial game
window = turtle.Screen()
window.tracer(0)
window.setup(0.5, 0.75) #makes the window


window.bgpic("background.png") # registers the the background images 
window.title("Daisy's Garden") # gives the pop up window a title 

# this gives phrases such as left, right, and top a purpose for later in the code. It sets up what those terms mean and where they are in the window pop up 
LEFT = -window.window_width() / 2
RIGHT = window.window_width() / 2
TOP = window.window_height() / 2
BOTTOM = -window.window_height() / 2
FLOOR_LEVEL = 0.9 * BOTTOM
GUTTER = 0.025 * window.window_width()


turtle.register_shape("pixel_daisy.gif") # inserting image into file 
turtle.register_shape("vines.gif")# inserting image into file 
# Create laser cannon
cannon = turtle.Turtle()
cannon.penup()
cannon.color(1, 1, 1)
cannon.shape("daisy.png") # takes that image out of the file to be used as the cannon
cannon.setposition(0, FLOOR_LEVEL + 115) # sets cannon on the floor
cannon.cannon_movement = 0  # -1, 0 or 1 for left, stationary, right 

# Create lives indicator (hearts)
lives = []
for i in range(MAX_HEALTH):
    life = turtle.Turtle()
    life.penup()
    life.color("red")
    life.shape("smaller_heart.gif") # takes image from file
    life.setposition(RIGHT - 30 - i * 30, TOP - 30) # puts its positon in the top right hand corner 
    life.showturtle()
    lives.append(life)

# Create turtle for writing text. This will be used for when the gaem is over but for now this piece of code just establishes a turtle whoes purpose is to write text
text = turtle.Turtle()
text.penup()
text.hideturtle()
text.setposition(LEFT * 0.8, TOP * 0.8)
text.color(1, 1, 1)

lasers = []
aliens = []

player_lives = MAX_HEALTH


def move_left():
    cannon.cannon_movement = -1 


def move_right():
    cannon.cannon_movement = 1


def stop_cannon_movement():
    cannon.cannon_movement = 0

# this creates the laser 
def create_laser():
    laser = turtle.Turtle()
    laser.penup()
    laser.color(0, 0, 1)
    laser.hideturtle()
    laser.setposition(cannon.xcor() + 90, cannon.ycor() + 15) # sets the laser right about Dasiys watergun 
    laser.setheading(90)
    laser.forward(10)
    # Prepare to draw the laser
    laser.pendown()
    laser.pensize(7)

    lasers.append(laser)

# moves the laser up towads the vines 
def move_laser(laser):
    laser.clear()
    laser.forward(LASER_SPEED)
    # Draw the laser
    laser.forward(LASER_LENGTH)
    laser.forward(-LASER_LENGTH)

# creates alien 
def create_alien():
    alien = turtle.Turtle()
    alien.penup()
    alien.turtlesize(0.05)
    alien.setposition(
        random.randint(
            int(LEFT + 100),
            int(RIGHT - 100),
        ),
        TOP,
    )

    alien.shape("vines.gif") # gives the turtles that are aliens the image of vines 
    alien.setheading(-90)
    aliens.append(alien)


def remove_sprite(sprite, sprite_list):
    sprite.clear()
    sprite.hideturtle()
    window.update()
    sprite_list.remove(sprite)
    turtle.turtles().remove(sprite)


# Key bindings that establish what keys do what in the game 
window.onkeypress(move_left, "Left")
window.onkeypress(move_right, "Right")
window.onkeyrelease(stop_cannon_movement, "Left")
window.onkeyrelease(stop_cannon_movement, "Right")
window.onkeypress(create_laser, "space")
window.onkeypress(turtle.bye, "q")
window.listen()

# Game loop
alien_timer = 0
game_timer = time.time()
score = 0
game_running = True # this means the game is running and the pop up window stays open continuing the game
while game_running and player_lives > 0:
    timer_this_frame = time.time()

# esatblishes the game timer
    time_elapsed = time.time() - game_timer
    text.clear()
    text.write(
        f"Time: {time_elapsed:5.1f}s\nScore: {score:5}",
        font=("Courier", 20, "bold"),
    )
    # Move cannon
    new_x = cannon.xcor() + CANNON_STEP * cannon.cannon_movement
    if LEFT + GUTTER <= new_x <= RIGHT - GUTTER:
        cannon.setx(new_x)

    # Move all lasers
    for laser in lasers.copy():
        move_laser(laser)
        # Remove laser if it goes off screen
        if laser.ycor() > TOP:
            remove_sprite(laser, lasers)
            break
        # Check for collision with aliens
        for alien in aliens.copy():
            if laser.distance(alien) < 20:
                remove_sprite(laser, lasers)
                remove_sprite(alien, aliens)
                score += 1 # adds to score when vines die 
                break
    # Spawn new aliens when time interval elapsed
    if time.time() - alien_timer > ALIEN_SPAWN_INTERVAL:
        create_alien()
        alien_timer = time.time()

    # Move all aliens LIFE HEART!!!
    for alien in aliens:
        alien.forward(ALIEN_SPEED)
        # Check for game over
        if alien.ycor() < FLOOR_LEVEL:
            player_lives -= 1
            if player_lives == -1:
                game_running = False
            else:
                # Remove a heart from the display
                life = lives.pop()
                life.clear()
                life.hideturtle()
            remove_sprite(alien, aliens)

    # Check for collision with player
    for alien in aliens.copy():
        if alien.distance(cannon) < 20:  # Adjust collision distance as needed
            player_lives -= 1  # Decrease player's lives upon collision
            if player_lives == -1:
                game_running = False
            else:
                # Remove a heart from the display
                life = lives.pop()
                life.clear()
                life.hideturtle()

            remove_sprite(alien, aliens)
# sets timer 
    time_for_this_frame = time.time() - timer_this_frame
    if time_for_this_frame < TIME_FOR_1_FRAME:
        time.sleep(TIME_FOR_1_FRAME - time_for_this_frame)
    window.update()

# establishes a game over screen when all 3 lives are taken
splash_text = turtle.Turtle()
splash_text.hideturtle()
splash_text.color(1, 1, 1)
splash_text.write("GAME OVER", font=("Courier", 40, "bold"), align="center")

turtle.done() # code finshes 
window.update()
