### 1. Введение

- **Цель документа:** Описать архитектуру и проектные решения для разработки игры "Тетрис" на языке Go с использованием библиотеки Ebitengine.
- **Область применения:** Для разработчиков, участвующих в создании игры "Тетрис". Документ описывает архитектурные решения, компоненты игры, их взаимодействие и дизайн.
- **Ссылки:** 
  - SRS-документация для игры "Тетрис"
  - Документация на библиотеку Ebitengine

---

### 2. Общее описание системы

- **Обзор системы:** Классическая головоломка, где игрок управляет падающими тетромино, заполняя горизонтальные линии, которые исчезают. Цель — не допустить переполнения экрана.
- **Архитектурные ограничения:** 
  - Язык: Go
  - Библиотека: Ebitengine
  - Разрешение: 320x640 пикселей
  - Платформы: Windows, Linux

---

### 3. Архитектурный дизайн

- **Архитектурный подход:** 
  - Объектно-ориентированная архитектура.
  - Основные классы: `Game`, `Tetromino`, `GameBoard`, `Score`.
- **Диаграмма архитектуры:**
  - `Game`: основной игровой класс.
  - `Tetromino`: управление движением и поворотом блоков.
  - `GameBoard`: логика поля, заполнение и очистка линий.
  - `Score`: подсчёт очков и уровней.

---

### 4. Детальное описание компонентов

## Класс `Game`

- **Описание:** Представляет основное состояние игры. Управляет игровым циклом, состоянием доски, текущим тетромино, счётом, уровнями, звуковыми эффектами и параметрами игры.

- **Атрибуты:**

    - `board [][]int` — игровое поле, представленное как двумерный массив целых чисел.
    - `currentTetromino [][]int` — текущее тетромино в виде двумерного массива.
    - `currentColor color.RGBA` — цвет текущего тетромино.
    - `tetrominoX, tetrominoY int` — текущие координаты тетромино на доске.
    - `frameCount int` — количество кадров, используемое для обновления игры.
    - `moveTimer, rotateTimer int` — таймеры для управления движением и поворотом.
    - `movePressed, rotatePressed bool` — флаги для отслеживания действий пользователя.
    - `score, linesCleared int` — текущий счёт и количество очищенных линий.
    - `isGameOver bool` — флаг состояния окончания игры.
    - `level int` — текущий уровень.
    - `moveDelay int` — задержка между движениями, влияющая на скорость игры.
    - `isChoosingDifficulty bool` — флаг выбора сложности.
    - `boardWidthExtra, boardHeightExtra int` — ширина и высота доски с учётом отступов.
    - `screenWidth, screenHeight int` — размеры экрана.
    - `audioContext *AudioContextWrapper` — контекст аудио для воспроизведения звуков.
    - `soundDead, soundPop, soundTurn *SoundPlayer` — звуковые эффекты для завершения игры, удаления линий и вращения.

- **Методы:**

    - `New() *Game` — инициализация нового экземпляра игры. Настраивает параметры экрана и звуковую систему.
    - `gameOver()` — вызывается при завершении игры. Устанавливает флаг окончания, выводит результат и воспроизводит звуковой эффект.
    - `initAudio()` — инициализирует аудиоконтекст и загружает звуковые файлы (`dead.mp3`, `pop.mp3`, `turn.mp3`). В случае ошибки выводит сообщение в лог.
    - `Init(level int)` — инициализирует игру с заданным уровнем сложности, устанавливает размеры доски, сбрасывает состояние и вызывает `spawnTetromino()`.
    - `updateScreenSize()` — обновляет размеры экрана на основе текущих размеров доски.
    - `boardHeight() int` — возвращает высоту доски в зависимости от уровня игры:
      - Уровень 1: 20 строк.
      - Уровень 2: 22 строки.
      - Уровень 3: 24 строки.
    - `Draw(screen *ebiten.Image)` — отрисовывает состояние игры: игровую сетку, доску, активное тетромино, счёт, сообщения о завершении игры или выборе сложности.
    - `Layout(outsideWidth, outsideHeight int) (int, int)` — определяет размеры экрана игры, возвращая ширину и высоту.
    - `updateSpeed()` — регулирует скорость игры в зависимости от уровня:
      - Уровень 1: каждые 1200 кадров уменьшает задержку до минимального значения 15.
      - Уровень 2: каждые 600 кадров уменьшает задержку до минимального значения 10.
      - Уровень 3: каждые 300 кадров уменьшает задержку до минимального значения 5.
    - `moveTetromino(dx, dy int) bool` — перемещает активное тетромино на `dx` по горизонтали и `dy` по вертикали. Возвращает `true`, если движение успешно.
    - `canMove(x, y int, tetromino [][]int) bool` — проверяет возможность перемещения тетромино на заданную позицию `x, y`, исключая столкновения с границами и блоками.
    - `mergeTetromino()` — добавляет текущее тетромино на игровую доску, фиксируя его положение.
    - `clearLines() int` — проверяет и очищает заполненные линии на доске, возвращает количество очищенных линий.
    - `updateScore(linesCleared int)` — обновляет счёт в зависимости от количества очищенных линий (с увеличением бонуса за большее число линий).
    - `spawnTetromino()` — создаёт новое тетромино в верхней части доски:
      - Уровень 1: только простые формы.
      - Уровень 2: простые и средние.
      - Уровень 3: простые, средние и сложные.
    - `rotateTetromino()` — поворачивает активное тетромино на 90° по часовой стрелке с проверкой на возможность перемещения.
    - `rotateMatrix(matrix [][]int) [][]int` — вспомогательная функция для поворота матрицы (тетромино) на 90°.
    - `Update() error` — главный игровой цикл:
      - Обрабатывает ввод пользователя.
      - Регулирует скорость игры.
      - Управляет движением и поворотом тетромино.
      - Проверяет завершение линий, обновляет счёт, создаёт новые тетромино.




---

### 5. Дизайн данных

- **Структуры данных:**
  - Массивы для позиций тетромино и клеток поля.
  - Двумерный массив для состояния игрового поля.
- **Формат данных:**
  - Координаты объектов в виде пар `(x, y)`.
  - Тетромино как объект с атрибутами и вектором поворота.

---

### 6. Интерфейсы системы

- **Внутренние интерфейсы:**
  - `Game` взаимодействует с остальными методами.
- **Внешние интерфейсы:**
  - Управление через клавиатуру:
    - Стрелки — движение.
    - Пробел — поворот.

---

### 7. Пользовательский интерфейс

- **Описание:** 
  - Отображение игрового поля, счёта и уровня.
  - Кнопки 1, 2, 3 для выбора сложности.
  - Кнопка R для перезапуска.
- **Макеты экранов:**
  - Главный экран: 1, 2, 3, R/
  - Игровой экран: поле, тетромино, текущий счёт.

---

### 8. Требования к производительности

- **Стабильность:**
  - Частота кадров: ≥60 FPS.
  - Время отклика: ≤100 мс.
- **Ограничения:**
  - Потребление памяти: ≤100 МБ.

---

### 9. Безопасность

- **Валидация данных:**
  - Проверка ввода при изменении настроек.

---

### 10. Обеспечение качества

- **Стратегия тестирования:**
  - Юнит-тесты для классов `Tetromino`, `GameBoard`, `Score`.
- **Критерии приемки:**
  - Стабильность и производительность (≥60 FPS).

---

### 11. Требования к среде разработки

- **Инструменты:**
  - Язык: Go
  - Библиотека: Ebitengine
  - IDE: VS Code
- **Ограничения:**
  - Go: ≥1.18
  - Ebitengine: ≥1.0

---

### 12. Приложения

- **Диаграммы UML:**
  - Диаграмма классов (`Game`).
  - Диаграмма последовательностей игрового цикла.
- **Доп. материалы:**
  - Схемы взаимодействий между объектами и методами.