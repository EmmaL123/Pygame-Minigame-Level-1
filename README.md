# Pygame-Minigame-Level-1
# Game name: Moving Geometry

import pygame

pygame.init()

WIDTH = 530
HEIGHT = 280
SIZE = (WIDTH, HEIGHT)

screen = pygame.display.set_mode(SIZE)
clock = pygame.time.Clock()

# ---------------------------
# GLOBAL VARIABLES

square_side = 50
obstacle1_x = 0
obstacle2_x = WIDTH-square_side
obstacle_speed = 5

player_x = 0
player_y = HEIGHT - square_side
player_speed = 5

game_font = pygame.font.SysFont('Calibri', 32)  # "Try Again" and "Success!" Text

# ---------------------------

# INTRO PAGE
def show_intro():
    game_name = "Welcome to MOVING GEOMETRY!"
    intro_text = "Move (arrow keys) to the light green area on the other side. Avoid anything red!"
    start_text = "Press ENTER to start the game."
    intro_font = pygame.font.SysFont('Calibri', 15)
    game_name_surf = intro_font.render(game_name, True, (0, 0, 0))
    intro_surf = intro_font.render(intro_text, True, (0, 0, 0))
    start_surf = intro_font.render(start_text, True, (0, 0, 0))
    game_name_rect = game_name_surf.get_rect(center=(WIDTH/2, HEIGHT/2 - 50))
    intro_rect = intro_surf.get_rect(center=(WIDTH/2, HEIGHT/2))
    start_rect = start_surf.get_rect(center=(WIDTH/2, HEIGHT/2 + 50))
    screen.fill((207,226,243))
    screen.blit(game_name_surf, game_name_rect)
    screen.blit(intro_surf, intro_rect)
    screen.blit(start_surf, start_rect)
    pygame.display.flip()
    waiting = True
    while waiting:
        for event in pygame.event.get():
            if event.type == pygame.KEYDOWN and event.key == pygame.K_RETURN:
                waiting = False

# RESTART IF PLAYER LOSES
def restart_round():
    global obstacle1_x, obstacle2_x, player_x, player_y
    obstacle1_x = 0
    obstacle2_x = WIDTH-square_side
    player_x = 0
    player_y = HEIGHT - square_side

# ---------------------------

show_intro()

running = True
while running:
    # EVENT HANDLING
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # KEYBOARD MOVEMENT
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        if player_x > 0:
            player_x -= player_speed
    elif keys[pygame.K_RIGHT]:
        if player_x < WIDTH - square_side:
            player_x += player_speed
    elif keys[pygame.K_UP]:
        if player_y > 0:
            player_y -= player_speed
    elif keys[pygame.K_DOWN]:
        if player_y < HEIGHT - square_side:
            player_y += player_speed

    # GAME STATE UPDATES    

    obstacle1_x += obstacle_speed # OBSTACLE1 MOVEMENT
    obstacle2_x -= obstacle_speed # OBSTACLE2 MOVEMENT

    if (obstacle1_x + square_side > WIDTH) or (obstacle1_x < 0): # OBSTACLE1 MOVEMENT
        obstacle_speed = - obstacle_speed
    elif (obstacle2_x + square_side > WIDTH) or (obstacle2_x < 0): # OBSTACLE2 MOVEMENT
        obstacle_speed = - obstacle_speed

    # COLLISION DETECTION
    player_rect = pygame.Rect(player_x, player_y, square_side, square_side)
    obstacle1_rect = pygame.Rect(obstacle1_x, 77, square_side, square_side)
    obstacle2_rect = pygame.Rect(obstacle2_x, 155, square_side, square_side)

    if player_rect.colliderect(obstacle1_rect) or player_rect.colliderect(obstacle2_rect):
        restart_round()
        # "TRY AGAIN" MESSAGE
        message = game_font.render("TRY AGAIN", True, (255, 0, 0))
        message_rect = message.get_rect(center=(WIDTH/2, HEIGHT/2))
        start_time = pygame.time.get_ticks()
        while pygame.time.get_ticks() - start_time < 2000:
            screen.blit(message, message_rect)
            pygame.display.flip()
    else:
        # "SUCCESS" MESSAGE
        goal_rect = pygame.Rect(WIDTH-square_side, 0, square_side, square_side)
        if player_rect.colliderect(goal_rect):
            message = game_font.render("SUCCESS", True, (0, 255, 0))
            message_rect = message.get_rect(center=(WIDTH/2, HEIGHT/2))
            start_time = pygame.time.get_ticks()
            while pygame.time.get_ticks() - start_time < 2000:
                screen.blit(message, message_rect)
                pygame.display.flip()
            restart_round()

    # DRAWING
    screen.fill((207,226,243))

    level_font = pygame.font.SysFont ('Calibri', 20, True, False) # "LEVEL 1" text
    level_text = level_font.render("LEVEL 1", True, (0,0,0))
    screen.blit (level_text, [400,250])

    pygame.draw.rect(screen, ((182,215,168)), (0, HEIGHT - square_side, square_side, square_side), 0) # STARTING POINT
    pygame.draw.rect(screen, ((0,0,0)), (0, HEIGHT - square_side, square_side, square_side), 1)
    area_font = pygame.font.SysFont ('Calibri', 10, True, False) # "START"/"GOAL" text
    start_text = area_font.render("START", True, (0,0,0)) # "START" text
    screen.blit (start_text, [9,250])
    
    pygame.draw.rect(screen, ((182,215,168)), (WIDTH - square_side, 0, square_side, square_side), 0) # GOAL
    pygame.draw.rect(screen, ((0,0,0)), (WIDTH - square_side, 0, square_side, square_side), 1)
    goal_text = area_font.render("GOAL", True, (0,0,0)) # "GOAL" text
    screen.blit (goal_text, [492,20])

    pygame.draw.rect(screen, ((234,153,153)), (obstacle1_x, 77, square_side, square_side), 0) # OBSTACLE 1
    pygame.draw.rect(screen, ((0,0,0)), (obstacle1_x, 77, square_side, square_side), 1)

    pygame.draw.rect(screen, ((234,153,153)), (obstacle2_x, 155, square_side, square_side), 0) # OBSTACLE 2
    pygame.draw.rect(screen, ((0,0,0)), (obstacle2_x, 155, square_side, square_side), 1)

    pygame.draw.rect(screen, ((50,205,50)), (player_x, player_y, square_side, square_side), 0) # PLAYER
    pygame.draw.rect(screen, ((0,0,0)), (player_x, player_y, square_side, square_side), 1)

    pygame.display.flip()
    clock.tick(30)

pygame.quit()
