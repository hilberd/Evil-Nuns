# Evil-Nuns
Evil Nuns game
import pygame
import random
import sys

# Ініціалізація Pygame
pygame.init()

# Параметри вікна
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Evil Nuns")

# Кольори
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# FPS і таймер
FPS = 60
clock = pygame.time.Clock()

# Гравець
player_size = 50
player_x = WIDTH // 2
player_y = HEIGHT - player_size - 10
player_speed = 7
bullets = []

# Монахині
nun_size = 50
nuns = []
nun_speed = 3
nun_spawn_delay = 1000  # 1 секунда між появами
last_spawn_time = pygame.time.get_ticks()

# Рахунок
score = 0
font = pygame.font.SysFont(None, 35)

def display_score():
    score_text = font.render(f"Score: {score}", True, BLACK)
    screen.blit(score_text, (10, 10))

# Функція для малювання куль
def draw_bullets():
    for bullet in bullets:
        pygame.draw.rect(screen, BLUE, bullet)

# Функція для малювання монахинь
def draw_nuns():
    for nun in nuns:
        pygame.draw.rect(screen, RED, nun)

# Основний ігровий цикл
def game_loop():
    global player_x, player_y, bullets, nuns, score, last_spawn_time

    running = True
    while running:
        screen.fill(WHITE)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    # Стрільба
                    bullet = pygame.Rect(player_x + player_size // 2 - 5, player_y, 10, 20)
                    bullets.append(bullet)

        # Керування гравцем
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            player_x -= player_speed
        if keys[pygame.K_RIGHT]:
            player_x += player_speed

        # Обмеження руху гравця
        if player_x < 0:
            player_x = 0
        if player_x > WIDTH - player_size:
            player_x = WIDTH - player_size

        # Оновлення куль
        for bullet in bullets[:]:
            bullet.y -= 10
            if bullet.y < 0:
                bullets.remove(bullet)

        # Спавн монахинь
        current_time = pygame.time.get_ticks()
        if current_time - last_spawn_time > nun_spawn_delay:
            nun_x = random.randint(0, WIDTH - nun_size)
            nun = pygame.Rect(nun_x, 0, nun_size, nun_size)
            nuns.append(nun)
            last_spawn_time = current_time

        # Оновлення монахинь
        for nun in nuns[:]:
            nun.y += nun_speed
            if nun.y > HEIGHT:
                nuns.remove(nun)

        # Перевірка зіткнень куль із монахинями
        for nun in nuns[:]:
            for bullet in bullets[:]:
                if nun.colliderect(bullet):
                    bullets.remove(bullet)
                    nuns.remove(nun)
                    score += 1

        # Перевірка зіткнення гравця з монахинями
        for nun in nuns:
            if nun.colliderect(pygame.Rect(player_x, player_y, player_size, player_size)):
                running = False

        # Малювання гравця, куль і монахинь
        pygame.draw.rect(screen, BLACK, (player_x, player_y, player_size, player_size))
        draw_bullets()
        draw_nuns()

        # Відображення рахунку
        display_score()

        # Оновлення екрану
        pygame.display.flip()
        clock.tick(FPS)

    # Кінець гри
    screen.fill(WHITE)
    game_over_text = font.render("Game Over! Press R to Restart or Q to Quit", True, BLACK)
    screen.blit(game_over_text, (WIDTH // 2 - 200, HEIGHT // 2))
    pygame.display.flip()

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_r:
                    main()
                if event.key == pygame.K_q:
                    pygame.quit()
                    sys.exit()

def main():
    game_loop()

if __name__ == "__main__":
    main()
