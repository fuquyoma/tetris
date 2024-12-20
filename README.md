[![Go Reference](https://pkg.go.dev/badge/github.com/natemcintosh/gocombinatorics.svg)](https://pkg.go.dev/github.com/webbsalad/tetris) [Пользовательская документация Latex](https://www.overleaf.com/project/676591221c704173a187d1de)


# Tetris Game

Это классическая игра Tetris, написанная на Go с использованием библиотеки [Ebiten](https://github.com/hajimehoshi/ebiten). Игрок управляет падающими тетромино, очищает линии на поле и набирает очки. Игра поддерживает выбор уровня сложности, управление тетромино и отслеживание счета.


  

## Особенности

- **Несколько уровней сложности**: Выберите один из трех уровней сложности, каждый с собственными настройками скорости и поля.

- **Движение тетромино**: Перемещайте тетромино влево, вправо и вниз.

- **Поворот тетромино**: Поворачивайте тетромино с помощью пробела.

- **Очистка линий**: Очищайте полные линии и увеличивайте свой счет.

- **Конец игры**: Игра заканчивается, когда новое тетромино не может быть размещено на поле.

- **Звуковые эффекты**: Звуки для вращения, очистки тетромино и проигрыша.

  

## Установка

Для запуска игры необходимо установить Go. Следуйте этим шагам:

  

1. Клонируйте репозиторий:
```
git clone https://github.com/webbsalad/tetris.git
cd tetris

```


2. Установите необходимые зависимости:
```
go mod tidy

```


3. Постройте игру:
```
go build -o tetris main.go

```

или просто запустите:
```
go run main.go

```
  

4. Запустите игру:
```
./tetris

```

  

## Управление

- **Левая стрелка**: Переместить тетромино влево.

- **Правая стрелка**: Переместить тетромино вправо.

- **Нижняя стрелка**: Переместить тетромино вниз быстрее.

- **Пробел**: Повернуть тетромино.

- **1, 2, 3**: Выбрать уровень сложности (1 = Легкий, 2 = Средний, 3 = Сложный).

- **R**: Перезапустить игру после окончания.

  

## Игровой процесс

- Игра начинается с экрана выбора сложности. Выберите сложность, нажав `1`, `2` или `3`.

- После начала игры тетромино начнут падать. Вам нужно перемещать и поворачивать их, чтобы они подходили в сетку.

- Полные линии блоков очищаются автоматически, и вы получаете очки за каждую очищенную линию.

- Игра заканчивается, когда новое тетромино не может быть размещено на поле.

- После окончания игры нажмите `R`, чтобы перезапустить игру.

  

## Лицензия

Этот проект лицензирован по лицензии MIT — подробности см. в файле [LICENSE](LICENSE).