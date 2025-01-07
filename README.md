pip install pygame
import pygame
import random
import sys

# Initialize pygame
pygame.init()

# Set up display
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Racing Game")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)

# Car and track settings
CAR_WIDTH = 50
CAR_HEIGHT = 100
CAR_SPEED = 5
CAR_IMAGE = pygame.Surface((CAR_WIDTH, CAR_HEIGHT))
CAR_IMAGE.fill(RED)

TRACK_WIDTH = 400
TRACK_BORDER = 50

# Game clock
FPS = 60
clock = pygame.time.Clock()

# Define font
font = pygame.font.SysFont("Arial", 30)

# Car class
class Car(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = CAR_IMAGE
        self.rect = self.image.get_rect()
        self.rect.center = (x, y)
        self.speed = CAR_SPEED

    def update(self, keys):
        if keys[pygame.K_LEFT] and self.rect.left > TRACK_BORDER:
            self.rect.x -= self.speed
        if keys[pygame.K_RIGHT] and self.rect.right < WIDTH - TRACK_BORDER:
            self.rect.x += self.speed
        if keys[pygame.K_UP] and self.rect.top > TRACK_BORDER:
            self.rect.y -= self.speed
        if keys[pygame.K_DOWN] and self.rect.bottom < HEIGHT - TRACK_BORDER:
            self.rect.y += self.speed

# Track class
class Track(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((WIDTH, HEIGHT))
        self.image.fill(BLACK)
        self.rect = self.image.get_rect()

        pygame.draw.rect(self.image, WHITE, [TRACK_BORDER, TRACK_BORDER, WIDTH - 2 * TRACK_BORDER, HEIGHT - 2 * TRACK_BORDER], 10)

# Obstacle class
class Obstacle(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill(GREEN)
        self.rect = self.image.get_rect()
        self.rect.center = (x, y)

    def update(self):
        self.rect.y += 5
        if self.rect.top > HEIGHT:
            self.rect.y = -50
            self.rect.x = random.randint(TRACK_BORDER, WIDTH - TRACK_BORDER)

# Game loop
def game_loop():
    running = True
    car = Car(WIDTH // 2, HEIGHT - 150)
    track = Track()

    all_sprites = pygame.sprite.Group()
    all_sprites.add(car)
    all_sprites.add(track)

    obstacles = pygame.sprite.Group()
    for i in range(5):
        obstacle = Obstacle(random.randint(TRACK_BORDER, WIDTH - TRACK_BORDER), random.randint(-100, -50))
        all_sprites.add(obstacle)
        obstacles.add(obstacle)

    score = 0
    start_ticks = pygame.time.get_ticks()

    while running:
        # Handle events
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        # Update
        keys = pygame.key.get_pressed()
        car.update(keys)
        obstacles.update()

        # Check collision
        if pygame.sprite.spritecollide(car, obstacles, False):
            running = False

        # Draw
        screen.fill(BLACK)
        all_sprites.draw(screen)

        # Display score
        seconds = (pygame.time.get_ticks() - start_ticks) // 1000
        score = seconds
        score_text = font.render(f"Time: {score}s", True, WHITE)
        screen.blit(score_text, (10, 10))

        # Refresh screen
        pygame.display.flip()

        # Cap the frame rate
        clock.tick(FPS)

    pygame.quit()
    sys.exit()

if __name__ == "__main__":
    game_loop()
