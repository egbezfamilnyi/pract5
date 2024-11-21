# Практическое занятие №5.Вопросы виртуализации
## Задача 1. Исследование виртуальной стековой машины CPython. Изучите возможности просмотра байткода ВМ CPython.
```
11 0 LOAD_FAST 0 (x)
```
Эта команда загружает значение локальной переменной x в стек.
Эквивалентное выражение на Python: x
```
2 LOAD_CONST 1 (10)
```
Здесь загружается константа 10 в стек.
Эквивалентное выражение на Python: 10
```
4 BINARY_MULTIPLY
```
Умножает два значения на верхушке стека (значение из x и 10), результат будет помещен на верх стека.
Эквивалентное выражение на Python: x * 10
```
6 LOAD_CONST 2 (42)
```
Загружает константу 42 в стек.
Эквивалентное выражение на Python: 42
```
8 BINARY_ADD
```
Складывает два значения на верхушке стека (результат умножения x * 10 и 42) и помещает результат в стек.
Эквивалентное выражение на Python: x * 10 + 42
```
10 RETURN_VALUE
```
Эта команда возвращает значение, находящееся на верхушке стека, в качестве результата выполнения функции.
Эквивалентное выражение на Python: return (x * 10 + 42)

## Задача 2. Что делает следующий байткод (опишите шаги его работы)? Это известная функция, назовите ее.
Этот байткод реализует функцию для вычисления факториала числа n
```5 0 LOAD_CONST 1 (1)``` Загружает константу 1 в стек.

```2 STORE_FAST 1 (r)``` Сохраняет значение 1 в локальную переменную r. В данный момент r = 1.

```6 >> 4 LOAD_FAST 0 (n)``` Загружает значение переменной n в стек. Это текущее значение числа, для которого вычисляется факториал.

```6 LOAD_CONST 1 (1)``` Загружает константу 1 снова в стек.

```8 COMPARE_OP 4 (>)``` Сравнивает значение n с 1. Если n больше 1, выполняется переход к следующему шагу. Иначе, выполнение переходит к инструкции 30 для возврата r.

```10 POP_JUMP_IF_FALSE 30``` Если n не больше 1, то голова управления переходит к адресу 30 (к возврату результата).

```12 LOAD_FAST 1 (r) ```Загружает текущее значение r в стек.

```14 LOAD_FAST 0 (n)``` Загружает значение n в стек.

```16 INPLACE_MULTIPLY ```Умножает r на n (текущее значение) и сохраняет результат в r. Теперь r = r * n.

```20 LOAD_FAST 0 (n) ```Снова загружает значение n в стек.

```22 LOAD_CONST 1 (1)``` Загружает 1 в стек.

```24 INPLACE_SUBTRACT``` Вычитает 1 из n и обновляет n. Теперь n = n - 1.

```26 STORE_FAST 0 (n)``` Сохраняет обновленное значение n.

```28 JUMP_ABSOLUTE 4 ```Переходит к инструкции с адресом 4, что приводит к повторному проверке условия .

```30 >> LOAD_FAST 1 (r)``` После завершения цикла возвращает значение переменной r, которая содержит результат вычисления факториала.

```32 RETURN_VALUE``` Завершает выполнение и возвращает значение r.

## Задача 3. Приведите результаты из задач 1 и 2 для виртуальной машины JVM (Java) или .Net (C#).
```
public class Factorial {
    public static int factorial(int n) {
        int r = 1;
        while (n > 1) {
            r *= n;
            n--;
        }
        return r;
    }
}
```
## Задача 4. Работа с qemu. Скачать и установить ISO-образ Alpine Linux для виртуальных машин с официального сайта. Создать с помощью qemu образ жесткого диска (опция -f qcow2). Объем диска 500 Мб. Запустить Alpine Linux с CD-ROM. Установить систему на sda. Изменить motd. Загрузиться уже с sda. Прислать полный список команд для установки и загрузки, а также скриншот с motd, где фигурируют ваши имя и фамилия.
```
qemu-img create -f qcow2 alpine_disk.qcow2 500M
```
```
qemu-system-x86_64 -cdrom path/to/alpine.iso -hda alpine_disk.qcow2 -boot d -m 512
```
```
echo "Sklyar Matvey" > /etc/motd
```
```
reboot
```
```
qemu-system-x86_64 -hda alpine_disk.qcow2 -boot c -m 512
```
<img width="519" alt="Снимок экрана 2024-11-22 в 00 07 18" src="https://github.com/user-attachments/assets/3d22689b-6440-4185-9eab-4e4635799401">

## Задача 5. (после разбора на семинаре и написания у доски базовой части эмулятора древней игровой приставки CHIP-8)
Реализовать вывод на экран.
Добиться запуска Тетриса.
Реализовать ввод с клавиатуры.
Добиться успешной работы всех приложений.
```
class Chip8:
    def __init__(self):
        self.memory = [0] * 4096  
        self.V = [0] * 16         
        self.I = 0                
        self.pc = 0x200           
        self.stack = []           
        self.display = [0] * (64 * 32) 
        self.keys = [0] * 16      
```
```
def load_game(self, game):
    with open(game, 'rb') as file:
        game_data = file.read()
        for i in range(len(game_data)):
            self.memory[0x200 + i] = game_data[i]
```
```
import pygame

    def initialize_graphics(self):
        pygame.init()
        self.window = pygame.display.set_mode((640, 320))
        pygame.display.set_caption("CHIP-8 Emulator")

    def draw_display(self):
        for x in range(64):
            for y in range(32):
                color = (255, 255, 255) if self.display[x + (y * 64)] else (0, 0, 0)
                pygame.draw.rect(self.window, color, (x * 10, y * 10, 10, 10))
        pygame.display.flip()
```
```
def handle_keys(self):
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
        
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_1:
                self.keys[0] = 1
            

        if event.type == pygame.KEYUP:
            if event.key == pygame.K_1:
                self.keys[0] = 0
```
```
def emulate_cycle(self):
    self.handle_keys()
    
    opcode = self.memory[self.pc] << 8 | self.memory[self.pc + 1]

    if opcode & 0xF000 == 0xA000:  
        self.I = opcode & 0x0FFF
        self.pc += 2
    self.draw_display()   
```
```
def main():
    chip8 = Chip8()
    chip8.initialize_graphics()
    chip8.load_game("tetris.ch8") 

    while True:
        chip8.emulate_cycle()

if __name__ == "__main__":
    main()
```
