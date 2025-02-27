# link-game-pt2
import pygame
import math
pygame.init()
pygame.display.set_caption("sprite sheet")  # sets the window title
screen = pygame.display.set_mode((800,800))  # creates game screen
screen.fill((0,0,0))
clock = pygame.time.Clock() #set up clock
gameover = False #variable to run our game loop

# CONSTANTS
LEFT = 0
RIGHT = 1
UP = 2
DOWN = 3
SPACE = 4
keys = [False, False, False, False, False] #this list holds whether each key has been pressed

# MAP: 2 is brick
map = [
    [2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2],
    [2, 5, 3, 2, 4, 3, 3, 3, 2, 4, 3, 3, 3, 2, 4, 4, 4, 4, 4, 2],
    [2, 3, 3, 2, 4, 3, 3, 3, 2, 4, 3, 3, 3, 2, 3, 3, 3, 3, 4, 2],
    [2, 3, 3, 2, 2, 2, 3, 3, 2, 2, 2, 3, 3, 2, 3, 3, 3, 3, 4, 2],
    [2, 3, 3, 3, 3, 3, 3, 3, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 4, 2],
    [2, 3, 3, 3, 3, 3, 3, 3, 2, 3, 3, 3, 3, 3, 3, 3, 3, 4, 4, 2],
    [2, 3, 3, 2, 2, 2, 3, 3, 3, 3, 3, 3, 3, 2, 3, 3, 4, 2, 2, 2],
    [2, 3, 3, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 2, 3, 3, 2, 3, 3, 2],
    [2, 4, 4, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 2, 2, 2, 2, 3, 3, 2],
    [2, 2, 2, 2, 3, 2, 2, 2, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 2],
    [2, 3, 3, 3, 3, 2, 3, 3, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 2],
    [2, 3, 3, 3, 3, 2, 3, 3, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 2],
    [2, 3, 3, 3, 3, 2, 3, 3, 2, 2, 2, 2, 2, 2, 2, 2, 2, 3, 3, 2],
    [2, 3, 3, 2, 3, 3, 3, 3, 2, 3, 3, 3, 3, 3, 3, 4, 2, 3, 3, 2],
    [2, 3, 3, 2, 3, 3, 3, 3, 2, 3, 2, 3, 2, 3, 3, 4, 2, 3, 3, 2],
    [2, 3, 3, 2, 3, 3, 3, 2, 2, 3, 2, 3, 3, 3, 3, 4, 2, 3, 3, 2],
    [2, 3, 3, 2, 3, 3, 3, 3, 2, 3, 2, 3, 3, 3, 3, 4, 2, 3, 3, 2],
    [2, 3, 3, 2, 3, 3, 3, 3, 2, 2, 2, 3, 3, 3, 3, 4, 2, 3, 3, 2],
    [2, 3, 3, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 4, 2, 3, 6, 2],
    [2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2] ]


brick = pygame.image.load('brick.png')
dirt = pygame.image.load('dirt.png') 
stone = pygame.image.load('stone.png')
Link = pygame.image.load('link.png')  # load your spritesheet
Link.set_colorkey((255, 0, 255))  # this makes bright pink (255, 0, 255) transparent (sort of)

# player variables
xpos = 400  # xpos of player
ypos = 400 # ypos of player

vx = 0
vy = 0

# animation variables variables
frameWidth = 13
frameHeight = 20
RowNum = 0  # for left animation, this will need to change for other animations
frameNum = 0
ticker = 0
direction = DOWN

while not gameover:
    clock.tick(60)  # FPS

    for event in pygame.event.get():  # quit game if x is pressed in top corner
        if event.type == pygame.QUIT:
            gameover = True

        #keyboard input---------------------------
        if event.type == pygame.KEYDOWN: 
            if event.key == pygame.K_LEFT:
                keys[LEFT] = True
            elif event.key == pygame.K_RIGHT:
                keys[RIGHT] = True
            elif  event.key == pygame.K_UP:
                keys[UP] = True
            elif event.key == pygame.K_DOWN:
                keys[DOWN] = True
        elif event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT:
                keys[LEFT] = False
            elif event.key == pygame.K_RIGHT:
                keys[RIGHT] = False

            elif  event.key == pygame.K_UP:
                keys[UP] = False
            elif event.key == pygame.K_DOWN:
                keys[DOWN] = False



        




    #Left/right MOVEMENT-------------------------------------
    if keys[LEFT] == True:
        vx = -3
        RowNum = 0
        direction = LEFT

    elif keys[RIGHT] == True:
        vx = 3
        RowNum = 1
        direction = RIGHT

    else:
        vx = 0
    
    if  keys[UP] == True:
        vy =-3
        RowNum = 2
        direction = UP

    elif keys[DOWN] == True:
        vy =+3
        RowNum = 3
        direction = DOWN
        
    else:
        vy = 0
     

    #map collision---------------------
    #left collision
    if map[int((ypos) / 40)][int((xpos - 5) / 40)] == 2 or map[int((ypos) / 40)][int((xpos - 5) / 40)] == 4:
        xpos+=3
        print("left collision!")

    if map[int((ypos - 5) / 40)][int((xpos ) / 40)] == 4  or map[int((ypos - 5) / 40)][int((xpos ) / 40)]  == 2:
        ypos+=3
        print("up collision!")
       
    #right collision
    if map[int((ypos) / 40)][int((xpos + 15) / 40)] == 2 or map[int((ypos) / 40)][int((xpos + 15) / 40)] == 4:
        xpos-=3   
        print("right collision!")

    if map[int((ypos  + 20) / 40)][int((xpos ) / 40)] == 4 or map[int((ypos + 20) / 40)][int((xpos ) / 40)]  == 2:
        ypos-=3   
        print("down collision!") 

    if map[int((ypos  + 10) / 40)][int((xpos + 10 ) / 40)] == 5:
        for i in range(20):
            for j in range(20):
                if map[i][j] == 6:
                    xpos = i*40
                    ypos = j*40
    xpos+=vx #update player xpos
    ypos+=vy

    # Animation update
    ticker+=1
    if vx != 0: #only animate when moving
        if ticker % 10 == 0:  # only change frames every 10 ticks (make number smaller for faster running animation)
            frameNum += 1
    if frameNum > 7:
        frameNum = 0



    # Render section--------------------------------------------------------
    screen.fill((0, 0, 0))  # wipe screen so it doesn't smear
    # draw map
    for i in range(20):
        for j in range(20):
            if map[i][j] == 2:
                screen.blit(brick, (j * 40, i * 40), (0, 0, 40, 40))
            if map[i][j] == 3:
                screen.blit(dirt, (j * 40, i * 40), (0, 0, 40, 40))
            if map[i][j] == 4:
                screen.blit(stone, (j * 40, i * 40), (0, 0, 40, 40))

    # draw player
    screen.blit(Link, (xpos, ypos), (frameWidth * frameNum, RowNum * frameHeight, frameWidth, frameHeight))
    pygame.display.flip()  # this actually puts the pixel on the screen

# end game loop
pygame.quit()
