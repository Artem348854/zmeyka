# zmeyka
игра в змейку, автор "булыч" управление: W + enter (верх) A + Enter (влево) S + enter (вниз) D + Enter (право)
import random
import time
import os

WIDTH = 20
HEIGHT = 15

SNAKE_HEAD = "🟢"
SNAKE_BODY = "🟩"
FOOD = "🍎"
WALL = "⬛"
EMPTY = "  "

snake = [(WIDTH // 2, HEIGHT // 2)]
direction = (0, -1)
food = None
score = 0
game_over = False

def clear():
    os.system('clear' if os.name == 'posix' else 'cls')

def generate_food():
    global food
    while True:
        pos = (random.randint(0, WIDTH-1), random.randint(0, HEIGHT-1))
        if pos not in snake:
            food = pos
            break

def draw():
    clear()
    print(WALL * (WIDTH + 2))
    
    for y in range(HEIGHT):
        line = WALL
        for x in range(WIDTH):
            if (x, y) == snake[0]:
                line += SNAKE_HEAD
            elif (x, y) in snake[1:]:
                line += SNAKE_BODY
            elif (x, y) == food:
                line += FOOD
            else:
                line += EMPTY
        line += WALL
        print(line)
    
    print(WALL * (WIDTH + 2))
    print(f"\n🍎 Счёт: {score}")
    print("-" * 30)
    print("Управление:")
    print("  W - вверх    S - вниз")
    print("  A - влево    D - вправо")
    print("  R - рестарт  Q - выход")
    print("-" * 30)

def move():
    global score, game_over
    head = snake[0]
    new_head = (head[0] + direction[0], head[1] + direction[1])
    
    # Проверка на стены
    if new_head[0] < 0 or new_head[0] >= WIDTH or new_head[1] < 0 or new_head[1] >= HEIGHT:
        game_over = True
        return
    
    # Проверка на еду
    if new_head == food:
        snake.insert(0, new_head)
        score += 1
        generate_food()
    else:
        snake.insert(0, new_head)
        snake.pop()
    
    # Проверка на самосъедание
    if snake[0] in snake[1:]:
        game_over = True

def restart():
    global snake, direction, score, game_over, food
    snake = [(WIDTH // 2, HEIGHT // 2)]
    direction = (0, -1)
    score = 0
    game_over = False
    generate_food()

# Запуск игры
generate_food()
draw()

# ОСНОВНОЙ ЦИКЛ ИГРЫ
while True:
    if not game_over:
        # Показываем ход игры
        print(f"\n⏳ Ход змейки... (нажми W/A/S/D для поворота или Enter для продолжения)")
        
        # Ждём ввод с таймаутом 0.4 секунды
        import sys
        import select
        
        # Проверяем, есть ли ввод
        if select.select([sys.stdin], [], [], 0.4)[0]:
            key = sys.stdin.readline().strip().lower()
            
            if key == 'w' and direction != (0, 1):
                direction = (0, -1)
            elif key == 's' and direction != (0, -1):
                direction = (0, 1)
            elif key == 'a' and direction != (1, 0):
                direction = (-1, 0)
            elif key == 'd' and direction != (-1, 0):
                direction = (1, 0)
            elif key == 'r':
                restart()
                draw()
                continue
            elif key == 'q':
                print("\n👋 До свидания!")
                sys.exit(0)
        
        # Двигаем змейку
        move()
        draw()
        
        # Проверка на конец игры
        if game_over:
            print("\n" + "=" * 30)
            print("       💀 ИГРА ОКОНЧЕНА 💀")
            print(f"       🍎 Счёт: {score}")
            print("=" * 30)
            print("\nНажми R для новой игры или Q для выхода")
    
    else:
        # Если игра окончена, ждём R или Q
        key = input().strip().lower()
        if key == 'r':
            restart()
            draw()
        elif key == 'q':
            print("\n👋 До свидания!")
            break
