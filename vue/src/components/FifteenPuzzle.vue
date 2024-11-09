<template>
  <div class="puzzle">
    <!-- Сообщение о победе -->
    <div v-if="isWinner" class="puzzle__win-message">
      Поздравляем! Вы победили!
    </div>

    <!-- Игровое поле -->
    <div class="puzzle__grid">
      <div
        v-for="(tile, index) in tiles"
        :key="index"
        :class="['puzzle__tile', { 'puzzle__tile--empty': tile === 0 }]"
        @click="moveTile(index)"
      >
        {{ tile !== 0 ? tile : "" }}
      </div>
    </div>

    <!-- Кнопка перемешивания -->
    <button class="puzzle__button" @click="shuffleTiles">Перемешать</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tiles: [
        1,
        2,
        3,
        4,
        5,
        6,
        7,
        8,
        9,
        10,
        11,
        12,
        13,
        14,
        15,
        0, // 0 represents the empty space
      ],
      emptyIndex: 15, // The index of the empty space (starts at the last position)
      isWinner: false, // Флаг для отслеживания победы
    };
  },
  methods: {
    // Проверка, можно ли переместить фишку
    moveTile(index) {
      const emptyRow = Math.floor(this.emptyIndex / 4);
      const emptyCol = this.emptyIndex % 4;
      const tileRow = Math.floor(index / 4);
      const tileCol = index % 4;

      console.log(
        `\nПробую переместить плитку: ${this.tiles[index]} с индекса: ${index} на индекс пустого пространства: ${this.emptyIndex}`,
      );
      console.log(`emptyRow: ${emptyRow}, emptyCol: ${emptyCol}`);
      console.log(`tileRow: ${tileRow}, tileCol: ${tileCol}`);

      // Проверяем, что фишка рядом с пустым пространством
      if (
        (Math.abs(emptyRow - tileRow) === 1 && emptyCol === tileCol) || // по вертикали
        (Math.abs(emptyCol - tileCol) === 1 && emptyRow === tileRow) // по горизонтали
      ) {
        console.log("Фишка может быть перемещена! Обмениваем их местами.");

        // Меняем местами tile и пустое пространство
        this.tiles[this.emptyIndex] = this.tiles[index];
        this.tiles[index] = 0;

        // Обновляем индекс пустой плитки
        this.emptyIndex = index;

        // После изменения данных массива мы гарантируем перерисовку, обновляя его через Vue
        this.$set(this, "tiles", [...this.tiles]);

        // Проверяем, не победил ли игрок
        this.checkVictory();

        // Логирование после перемещения
        console.log("Местами обменены. Новая конфигурация:");
        console.log(this.tiles);
      } else {
        console.log(
          "Фишка не может быть перемещена, так как она не рядом с пустым пространством.",
        );
      }
    },

    // Функция перемешивания фишек
    shuffleTiles() {
      console.log("\nПеремешиваем плитки...");
      let shuffled = [...this.tiles];
      shuffled = this.shuffleArray(shuffled);
      this.tiles = shuffled;
      this.emptyIndex = shuffled.indexOf(0);

      // Логирование после перемешивания
      console.log("Плитки перемешаны. Новая конфигурация:");
      console.log(this.tiles);

      // Проверка победы после перемешивания
      this.checkVictory();
    },

    // Проверка на победу
    checkVictory() {
      const winningConfiguration = [
        1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 0,
      ];

      // Если конфигурация плиток совпадает с выигрышной, показываем сообщение о победе
      if (
        this.tiles.every((tile, index) => tile === winningConfiguration[index])
      ) {
        this.isWinner = true;
        console.log("Вы победили!");
      } else {
        this.isWinner = false;
      }
    },

    // Перемешивание массива случайным образом
    shuffleArray(arr) {
      for (let i = arr.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]]; // Swap elements
      }
      return arr;
    },
  },
};
</script>

<style scoped>
/* Блок */
.puzzle {
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* Элемент: сетка плиток */
.puzzle__grid {
  display: grid;
  grid-template-columns: repeat(4, 80px);
  grid-template-rows: repeat(4, 80px);
  gap: 5px;
  margin-bottom: 20px;
}

/* Элемент: плитка */
.puzzle__tile {
  width: 80px;
  height: 80px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #ddd;
  font-size: 24px;
  border: 1px solid #aaa;
  cursor: pointer;
}

/* Модификатор для пустой плитки */
.puzzle__tile--empty {
  background-color: white;
  border: none;
}

/* Элемент: кнопка */
.puzzle__button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 5px;
  transition: background-color 0.3s ease;
}

/* Модификатор для кнопки при наведении */
.puzzle__button:hover {
  background-color: #45a049;
}

/* Элемент: сообщение о победе */
.puzzle__win-message {
  font-size: 24px;
  font-weight: bold;
  color: green;
  margin-bottom: 20px;
}
</style>
