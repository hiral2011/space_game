# space_game
import pygame
import random
import math
from pygame import mixer 

#intialize the pygame
pygame.init()

# creat the screen
screen = pygame.display.set_mode((800,600))

#background
# background = pygame.image.load('bg.png')


# backgroundsound
mixer.music.load('background.wav')
mixer.music.play(-1)

#title and icon
pygame.display.set_caption("space invaders")
icon = pygame.image.load('ufo.png')
# icon = pygame.transform.scale(icon,(32,32))
pygame.display.set_icon(icon)


#player
playerimg = pygame.image.load('player.png')
playerX = 370
playerY = 480
playerX_change = 0


#ememy
enemyimg = []
enemyX = []
enemyY = []
enemyX_change = []
enemyY_change = []
num_of_enemies = 8

for i in range(num_of_enemies):
    enemyimg.append(pygame.image.load('alien.png'))
    enemyX.append(random.randint(0,735))
    enemyY.append(random.randint(50,150))
    enemyX_change.append(0.2)
    enemyY_change.append(40)


#bullet

#ready - you can not see bullet on the screeen
#fire - the bullet is curently moving

bulletimg = pygame.image.load('bullets.png')
bulletX = 0
bulletY = 480
bulletX_change = 0
bulletY_change = 10
bullet_state = "ready"


# score
score_value = 0
font = pygame.font.Font('freesansbold.ttf',32)

textX = 10
textY = 10

# game over text
over_font =  pygame.font.Font('freesansbold.ttf',65)

def show_score(x,y):
    score = font.render("score : " + str(score_value),True,(255,255,255))
    screen.blit(score, (x,y))


def game_over_text():
    over_text = over_font.render("GAME OVER",True,(255,255,255))
    screen.blit(over_text, (200,250))


def player(x,y):
    screen.blit(playerimg, (x,y))

def enemy(x,y,i):
    screen.blit(enemyimg[i], (x,y))

def fire_bullet(x,y):
    global bullet_state
    bullet_state = "fire"
    screen.blit(bulletimg,(x + 16, y + 10))

def isCollision(enemyX,enemyY,bulletX,bulletY):
    distance = math.sqrt((math.pow(enemyX-bulletX,2)) + (math.pow(enemyY-bulletY,2)))
    if distance < 27:
        return True
    else:
        return False


# game loop
running = True
while running:


    # rgb = red,green ,blue
    screen.fill((0,0,0))
    #backgrond image
    # screen.blit(background,(0,0))
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        #if key is press check whether its  right or left
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                playerX_change = -0.3
            if event.key == pygame.K_RIGHT:
                playerX_change = 0.3
            if event.key == pygame.K_SPACE:
                if bullet_state is "ready":
                    bullet_sound = mixer.Sound('laser.wav')
                    bullet_sound.play()
                    bulletX = playerX
                    fire_bullet(bulletX,bulletY)


        if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                playerX_change = 0

  
    # 5 = 5+ -0.1 -> 5=5-0.1
    #5 = 5 +0.1 -=> 5.1
    #player movement     
    playerX += playerX_change

    if playerX <= 0:
        playerX = 0
    elif playerX >= 736:
        playerX = 736


    #enemy movement
    for i in range(num_of_enemies):
        
        # game over
        if enemyY[i] > 300:
            for j in range(num_of_enemies):
                enemyY[j] = 2000
            game_over_text()
            break

        enemyX[i] += enemyX_change[i]
        if enemyX[i] <= 0:
            enemyX_change[i] = 0.2
            enemyY[i] += enemyY_change[i]
        elif enemyX[i] >= 736:
            enemyX_change[i] = -0.2
            enemyY[i] += enemyY_change[i]

        
        #collosion
        collision = isCollision(enemyX[i],enemyY[i],bulletX,bulletY)
        if collision:
            # explosion_sound = mixer.Sound('smb_mariodie.wav')
            # explosion_sound.play()
            bulletY = 480
            bullet_state = "ready"
            score_value += 1
            enemyX[i] = random.randint(0, 735)
            enemyY[i] = random.randint(50, 150)
            
        enemy(enemyX[i], enemyY[i], i)



    #bullet movement
    if bulletY <= 0 :
        bulletY = 480
        bullet_state = "ready"

    if bullet_state is "fire":
        fire_bullet(bulletX,bulletY)
        bulletY -= bulletY_change

    



    player(playerX,playerY)
    show_score(textX, textY)
    pygame.display.update()

