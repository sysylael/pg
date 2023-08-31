
import pygame, time, sys, os, asyncio
from pygame.constants import K_RIGHT


# Initialize essential setup
pygame.init()

# Screen size setting
screen_width = 640
screen_height = 480
screen = pygame.display.set_mode((screen_width, screen_height))



# Screen title setting
pygame.display.set_caption("Cat Pang")

# Frames Per Second (FPS) refers to the number of frames (images) displayed per second.
clock = pygame.time.Clock()

# User game initialization: Background, game images, coordinates, speed, font, etc.
# file_path = "Downloads/"
file_path = "/Users/dragon/Downloads/"

background = pygame.image.load(os.path.join(file_path, "background.png"))

# Define font
game_font = pygame.font.Font(None, 40)

start_ticks = pygame.time.get_ticks()

# Game end messages: TimeOut, Mission Complete, Game Over
game_result = ""



def death_screen():  
    # pygame.mixer.music.load('sound/game_over.wav')  
    # pygame.mixer.music.play(-1) 
    # pygame.mixer.music.set_volume(.9)

    death_background = pygame.image.load(os.path.join(file_path, "death_screen.png"))
    screen.blit(death_background, (0, 0))
    pygame.display.update()
    pygame.time.delay(1000)
    

    
def explode_1():
    global health, total_time, elapsed_time
    health -= 1
    pygame.display.update()
    time.sleep(0.1)

    

async def main(): 
    global health, total_time, elapsed_time
    # Create background
    background = pygame.image.load(os.path.join(file_path, "background.jpg"))
    
    health = 7
    total_time = 15
    
    # Create stage
    stage = pygame.image.load(os.path.join(file_path, "stage.jpg"))
    stage_size = stage.get_rect().size
    stage_height = stage_size[1]

    # Create character
    character = pygame.image.load(os.path.join(file_path, "character.png"))
    char_life = pygame.image.load(os.path.join(file_path, "heart.png"))
    character_size = character.get_rect().size
    character_width = character_size[0]
    character_height = character_size[1]
    character_x_pos = (screen_width / 2) - (character_width / 2)
    character_y_pos = screen_height - character_height - stage_height

    # Character's movement direction
    character_to_x = 0

    # Character's movement speed
    character_speed = 5

    # Create weapon
    weapon = pygame.image.load(os.path.join(file_path, "weapon.png"))
    weapon_size = weapon.get_rect().size
    weapon_width = weapon_size[0]
    weapons = []
    weapon_speed = 15

    # Create balls for 4 different sizes
    ball_images = [
        pygame.image.load(os.path.join(file_path, "balloon1.png")),
        pygame.image.load(os.path.join(file_path, "balloon2.png")),
        pygame.image.load(os.path.join(file_path, "balloon3.png")),
        pygame.image.load(os.path.join(file_path, "balloon4.png"))
    ]

    ball_speed_y = [-18, -15, -12, -9]
    balls = []

    balls.append({
        "pos_x": 50,
        "pos_y": 50,
        "img_idx": 0,
        "to_x": 3,
        "to_y": -6,
        "init_spd_y": ball_speed_y[0]
    })

    # Variables to store disappearing weapon and ball information
    weapon_to_remove = -1
    ball_to_remove = -1

    
    # Define font
    game_font = pygame.font.Font(None, 40)
    # total_time = 60
    start_ticks = pygame.time.get_ticks()

    # Game end messages: TimeOut, Mission Complete, Game Over
    game_result = ""
    
  
    running = True
    while running:
        dt = clock.tick(30)

        # Event handling (keyboard, mouse)
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:  # Move the character to the left.
                    character_to_x -= character_speed
                elif event.key == pygame.K_RIGHT:  # Move the character to the right.
                    character_to_x += character_speed
                elif event.key == pygame.K_SPACE:  # Fire the weapon.
                    weapon_x_pos = character_x_pos + \
                        (character_width / 2) - (weapon_width / 2)
                    weapon_y_pos = character_y_pos
                    weapons.append([weapon_x_pos, weapon_y_pos])

            if event.type == pygame.KEYUP:
                if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                    character_to_x = 0

        # 3. Define game character positions
        character_x_pos += character_to_x

        if character_x_pos < 0:
            character_x_pos = 0
        elif character_x_pos > screen_width - character_width:
            character_x_pos = screen_width - character_width

        # Adjust weapon positions
        # 100, 200 -> 180, 160, 140, ...
        # 500, 200 -> 180, 160, 140. ...
        weapons = [[w[0], w[1] - weapon_speed] for w in weapons]  # Raise the weapon's position.

        # Remove the weapons that have hit the ceiling
        weapons = [[w[0], w[1]] for w in weapons if w[1] > 0]

        # Define the positions of the balls.
        for ball_idx, ball_val in enumerate(balls):
            ball_pos_x = ball_val["pos_x"]
            ball_pos_y = ball_val["pos_y"]
            ball_img_idx = ball_val["img_idx"]

            ball_size = ball_images[ball_img_idx].get_rect().size
            ball_width = ball_size[0]
            ball_height = ball_size[1]

            # Change the movement position of the ball when it hits the horizontal wall (bouncing effect).
            if ball_pos_x < 0 or ball_pos_x > screen_width - ball_width:
                ball_val["to_x"] = ball_val["to_x"] * -1

            # Vertical position, handling the bounce off the stage to go upwards.
            if ball_pos_y >= screen_height - stage_height - ball_height:
                ball_val["to_y"] = ball_val["init_spd_y"]
            else:  # In all other cases, increase the speed.
                ball_val["to_y"] += 0.5

            ball_val["pos_x"] += ball_val["to_x"]
            ball_val["pos_y"] += ball_val["to_y"]

        # 4. Collision handling.

        # Update character's rect information.
        character_rect = character.get_rect()
        character_rect.left = character_x_pos
        character_rect.top = character_y_pos

        for ball_idx, ball_val in enumerate(balls):
            ball_pos_x = ball_val["pos_x"]
            ball_pos_y = ball_val["pos_y"]
            ball_img_idx = ball_val["img_idx"]

            # Update ball's rect information.
            ball_rect = ball_images[ball_img_idx].get_rect()
            ball_rect.left = ball_pos_x
            ball_rect.top = ball_pos_y
            
            
            # Check collision between ball and character.
            if character_rect.colliderect(ball_rect):
               # bulletSound.play()
                broken_heart = pygame.image.load(os.path.join(file_path, "broken_heart.png"))
                screen.blit(broken_heart, (300 - (broken_heart.get_width()/2), 60))
                pygame.display.update()
                await asyncio.sleep(0)

                if health <= 0:
                    running = False
                    time.sleep(2)
                    death_screen()
                    health = 20
                    total_time = 30

                else:
                    explode_1()

            # Handle collisions between balls and weapons.
            for weapon_idx, weapon_val in enumerate(weapons):
                weapon_pos_x = weapon_val[0]
                weapon_pos_y = weapon_val[1]

                # Update weapon's rect information.
                weapon_rect = weapon.get_rect()
                weapon_rect.left = weapon_pos_x
                weapon_rect.top = weapon_pos_y

                # Check for collisions.
                if weapon_rect.colliderect(ball_rect):
                    weapon_to_remove = weapon_idx  # Set values to remove the corresponding weapon.
                    ball_to_remove = ball_idx  # Set values to remove the corresponding ball.

                    # If it's not the smallest-sized ball, divide it into the next stage of balls.
                    if ball_img_idx < 3:
                        # Retrieve the current ball size information.
                        ball_width = ball_rect.size[0]
                        ball_heigth = ball_rect.size[1]

                        # Information about the divided balls.
                        small_ball_rect = ball_images[ball_img_idx + 1].get_rect()
                        small_ball_width = small_ball_rect.size[0]
                        small_ball_height = small_ball_rect.size[1]

                        # Ball bouncing to the left.
                        balls.append({
                            # Ball's x-coordinate.
                            "pos_x": ball_pos_x + (ball_width / 2) - (small_ball_width / 2),
                            # Ball's y-coordinate.
                            "pos_y": ball_pos_y + (ball_height / 2) - (small_ball_height / 2),
                            "img_idx": ball_img_idx + 1,  # Index of Images of Balls
                            "to_x": -3,  # X-axis Movement Direction, -3 for left, 3 for right
                            "to_y": -6,  # Y-axis Movement Direction,
                            # Initial Y Velocity
                            "init_spd_y": ball_speed_y[ball_img_idx + 1]
                        })

                        # Ball bouncing to the right.
                        balls.append({
                            "pos_x": ball_pos_x + (ball_width / 2) - (small_ball_width / 2),
                            "pos_y": ball_pos_y + (ball_height / 2) - (small_ball_height / 2),
                            "img_idx": ball_img_idx + 1,  
                            "to_x": 3, 
                            "to_y": -6, 
                            "init_spd_y": ball_speed_y[ball_img_idx + 1]
                        })
                    break
            else:  # Continue the game.
                continue  # If the condition for the inner loop is not met, continue. Proceed with the outer loop.
            break  # If a break is encountered in the inner for loop, entry is possible here. Escape from both nested loops simultaneously.

        # Remove collided balls or weapons.
        if ball_to_remove > -1:
            del balls[ball_to_remove]
            ball_to_remove = -1
        if weapon_to_remove > -1:
            del weapons[weapon_to_remove]
            weapon_to_remove = -1

        # Game over (success) when all balls are eliminated.
        if len(balls) == 0:
            game_result = "Mission Complete"
            running = False

        # 5. Draw on the screen.
        screen.blit(background, (0, 0))

        for weapon_x_pos, weapon_y_pos in weapons:
            screen.blit(weapon, (weapon_x_pos, weapon_y_pos))

        for idx, val in enumerate(balls):
            ball_pos_x = val["pos_x"]
            ball_pos_y = val["pos_y"]
            ball_img_idx = val["img_idx"]
            screen.blit(ball_images[ball_img_idx], (ball_pos_x, ball_pos_y))

        screen.blit(stage, (0, screen_height - stage_height))
        screen.blit(character, (character_x_pos, character_y_pos))

        screen.blit(char_life, (400, 15)) 
        life = game_font.render('x' + str(health), 1, (0,0,0))
        screen.blit(life, (435, 10))
        
        # Calculate elapsed time.
        elapsed_time = (pygame.time.get_ticks() - start_ticks) / 1000
        timer = game_font.render("Time: {}".format(
            int(total_time - elapsed_time)), True, (255, 255, 0))
        screen.blit(timer, (10, 10))

        # If time has exceeded.
        if total_time - elapsed_time <= 0:
            game_result = "Mission Complete"
            running = False
        
        pygame.display.update()
        await asyncio.sleep(0)
        
        msg = game_font.render(game_result, True, (0, 255, 0))
        msg_rect = msg.get_rect(center=(int(screen_width/2), int(screen_height/2)))
        screen.blit(msg, msg_rect)
        pygame.display.update()
              
        await asyncio.sleep(0)


# Wait for 2 seconds.
pygame.time.delay(2000)

text_background_color = (255, 255, 255)
title_font = pygame.font.SysFont('bahnschrift', 44)
menu_font = pygame.font.SysFont('bahnschrift', 28)

while True:
    screen.blit(background, (0, 0))  
    title_label = title_font.render('Welcome to', True, (0, 0, 0))
    title2_label = title_font.render('\'Pang Pang\'', True, (0, 0, 0))
    start_label = menu_font.render('Enter to \'Start\'', True, (0, 0, 0), text_background_color)  

    screen.blit(title_label, (220, 260))
    screen.blit(title2_label, (220, 310))
    screen.blit(start_label, (10, 390))
    pygame.display.update()

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            False
            pygame.quit()
            sys.exit()
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_RETURN:
                await main()
           
    
       
# asyncio.run(main())

