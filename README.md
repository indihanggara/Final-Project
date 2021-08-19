# Final-Project
Final Project Indonesian Women in Tech 2021
Programming with Python
# Intialization
 ```
 pygame.init() 
width = 700 
height = 500 

display = pygame.display.set_mode((width, height))
clock = pygame.time.Clock() 
pygame.display.set_caption("Don't Let Them In") 

ship_width = 40 
ship_height = 30 
```
# Sound
sound from https://opengameart.org/content/laser-fire
```
click_sound = pygame.mixer.Sound("laser5.wav")
back_sound=pygame.mixer.Sound("alex_mcculloch_synth_wave.mp3")
win_sound=pygame.mixer.Sound("Won!.wav")
```
# Colours
```
background = (74, 35, 90)
white = (244, 246, 247)
yellow = (241, 196, 15)
orange = (186, 74, 0)
green = (35, 155, 86)
white1 = (253, 254, 254)
dark_gray = (23, 32, 42)
```
# Space-Ship Class 
```
class SpaceShip:
    def __init__(self, x, y, w, h, colour):
        self.x = x 
        self.y = y 
        self.w = w 
        self.h = h 
        self.colour = colour 

    def draw(self):
        pygame.draw.polygon(display, yellow, [[self.x + self.w/2-20, self.y],[self.x + self.w/2,self.y-25],[self.x + self.w/2 + 20,self.y]]) 
        pygame.draw.rect(display, self.colour, (self.x, self.y, self.w, self.h)) 
        pygame.draw.rect(display, yellow, (self.x+self.w/2-13,self.y+30,25,9)) 
        pygame.draw.rect(display, dark_gray, (self.x + 5, self.y + 6, 5, self.h - 10)) 
        pygame.draw.rect(display, dark_gray, (self.x + self.w - 10, self.y + 6, 5, self.h - 10)) 
```
# Bullet Class
```
class Bullet:
    def __init__(self, x, y):
        self.x = x 
        self.y = y 
        self.d = 8 
        self.speed = -5 

    def draw(self):
        pygame.draw.ellipse(display, orange, (self.x, self.y, self.d, self.d)) 
        
    def move(self):
        self.y += self.speed  

    def hit(self, x, y, d): 
        if x < self.x < x+d:
            if y + d > self.y > y:
                return True
```
# Alien Class
```
class Alien:
    def __init__(self, x, y, d):
        self.x = x 
        self.y = y 
        self.d = d
        self.x_dir = 1
        self.speed = 3

    def draw(self):
        pygame.draw.ellipse(display, green, (self.x, self.y, self.d, self.d)) 
        pygame.draw.ellipse(display, dark_gray, (self.x + 10, self.y + self.d/3, 8, 8), 2) 
        pygame.draw.ellipse(display, dark_gray, (self.x + self.d - 20, self.y + self.d/3, 8, 8), 2) 
        pygame.draw.ellipse(display, dark_gray, (self.x + self.d - 30, self.y + self.d/2, 8, 8), 2)

    def move(self):
        self.x += self.x_dir*self.speed 
        
    def shift_down(self):
        self.y += self.d 
```
# Saved
Display when win
```
def saved():
    font = pygame.font.SysFont("Wide Latin", 22)
    font_large = pygame.font.SysFont("Wide Latin", 43)
    text = font.render("You Win!", True, white1)
    display.blit(text, (250, height/2))
    pygame.display.update()
```
# Death
Display when lose
```
def GameOver():
    font = pygame.font.SysFont("Chiller", 50)
    font_large = pygame.font.SysFont("Chiller", 100)
    text2 = font_large.render("Game Over!", True, white1)
    display.blit(text2, (180, height/2-50))
```
# The Game 
```
def game():
    attack = False
    ship = SpaceShip(width/5-ship_width/2, height-ship_height - 10, ship_width, ship_height, white)
   
    

    bullets = []
    num_bullet = 0
    for i in range(num_bullet):
        i = Bullet(width/2 - 5, height - ship_height - 20)
        bullets.append(i)

    x_move = 0
    
    aliens = []
    num_aliens = 9
    d = 50
    for i in range(num_aliens):
        i = Alien((i+1)*d + i*20, d+20, d)
        aliens.append(i)
        back_sound.play()
```
# Keyboard
```
while not attack:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
                

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_q:
                    pygame.quit()
                    sys.exit()

                if event.key == pygame.K_RIGHT:
                    x_move = 5
                    

                if event.key == pygame.K_LEFT:
                    x_move = -5
                 

                if event.key == pygame.K_SPACE:
                    num_bullet += 1
                    i = Bullet(ship.x + ship_width/2 - 5, ship.y)
                    bullets.append(i)
                    click_sound.play()
                    

            if event.type == pygame.KEYUP:
                x_move = 0

        display.fill(background)
```
# When bullet vs alien
```
for i in range(num_bullet):
            bullets[i].draw()
            bullets[i].move()

        for alien in list(aliens):
            alien.draw()
            alien.move()
            for item in list(bullets):
                if item.hit(alien.x, alien.y, alien.d):
                    bullets.remove(item)
                    num_bullet -= 1
                    aliens.remove(alien)
                    num_aliens -= 1
```
# When the number of alien zero
```
if num_aliens == 0:
            saved()
            back_sound.stop()
            win_sound.play()
            attack = True
```
# Alien
```
 for i in range(num_aliens):
            if aliens[i].x + d >= width+200:
                for j in range(num_aliens):
                    aliens[j].x_dir = -1
                    aliens[j].shift_down()

            if aliens[i].x <= 0:
                for j in range(num_aliens):
                    aliens[j].x_dir = 1
                    aliens[j].shift_down()
      
        try:
            if aliens[0].y + d > height:
                GameOver()
                pygame.display.update()
                time.sleep(3)
                attack = True
        except Exception as e:
            pass
```
# Movement Ship and Alien
```
ship.x += x_move

        if ship.x < 0:
            ship.x -= x_move
        if ship.x + ship_width > width:
            ship.x -= x_move

        ship.draw()

        pygame.display.update()
        clock.tick(60)
```
# Calling the Game Function
```
game()
```
