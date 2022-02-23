<template>
  <article>
    <h1 @click="moveDown">
      My Minimal Tetris - press 'Enter'
      {{ this.activeGame ? "to pause" : "to start" }}
    </h1>

    <div class="playground">
      <GroundItem
        v-for="(item, index) of 150"
        :active="false"
        :key="index"
        :index="index + 1"
        :itemColor="itemColor[index + 1]"
      />
    </div>
    <p class="points">Points: {{ points }}</p>
    <p class="points">Level: {{ lvl }}</p>
    <button @click="delRow(14)">delete 14th row</button>
  </article>
</template>

<script>
import GroundItem from "@/components/GroundItem.vue";
export default {
  components: {
    GroundItem,
  },
  data() {
    return {
      position: this.randomNumber(11, 19),
      activeStonePosition: [this.position],
      itemColor: [],
      actualFigure: [],
      activeGame: false,
      color: "",
      points: 0,
      lvl: 1,
      speed: 700,
    };
  },
  created() {
    for (let i = 1; i <= 150; i++) {
      this.itemColor[i] = "wait";
    }
    this.color = this.randomColor();
    this.itemColor[this.position] = this.color;
    this.actualFigure = this.createRect(this.position);
    window.addEventListener("keydown", (e) => {
      if (e.key === "ArrowLeft") {
        this.moveRectManual("left");
      } else if (e.key === "ArrowRight") {
        this.moveRectManual("right");
      } else if (e.key === "ArrowDown") {
        this.moveRectManual("down");
      } else if (e.key === "Enter") {
        if (!this.activeGame) {
          this.activeGame = setInterval(() => {
            this.moveRectDown();
          }, this.speed);
        } else {
          clearInterval(this.activeGame);
          this.activeGame = false;
        }
      }
    });
  },
  methods: {
    createRect() {
      this.position === 20 ? (this.position = 19) : this.position;
      this.itemColor[this.position] = this.color;
      this.itemColor[this.position + 1] = this.color;
      this.itemColor[this.position - 10] = this.color;
      this.itemColor[this.position - 9] = this.color;
      return [0, -10, 1, -9];
    },
    moveRectDown() {
      if (
        this.position > 140 ||
        this.itemColor[this.position + 10] !== "wait" ||
        this.itemColor[this.position + 1 + 10] !== "wait"
      ) {
        this.checkPoints();
        this.color = this.randomColor();
        this.position = this.randomNumber(10, 19);

        this.activeStonePosition[0] = this.position;
        this.itemColor[this.position] !== "wait"
          ? clearInterval(this.activeGame)
          : (this.actualFigure = this.createRect(this.randomNumber(10, 19)));
        return "bottom";
      }

      for (let i = 0; i < this.actualFigure.length; i++) {
        this.itemColor[this.position + this.actualFigure[i]] = "wait";
        this.itemColor[this.position + 10 + this.actualFigure[i]] = this.color;
      }
      this.activeStonePosition[0] = this.position;
      this.position = this.position + 10;
    },
    moveDown() {
      if (
        this.position > 140 ||
        this.itemColor[this.position + 10] !== "wait"
      ) {
        this.checkPoints();
        this.color = this.randomColor();
        this.position = this.randomNumber(1, 10);
        this.activeStonePosition[0] = this.position;
        this.itemColor[this.position] !== "wait"
          ? clearInterval(this.activeGame)
          : (this.itemColor[this.position] = this.color);
        return "bottom";
      }
      this.itemColor[this.position] = "wait";
      this.position = this.position + 10;
      this.activeStonePosition[0] = this.position;
      this.itemColor[this.position] = this.color;
    },
    moveRectManual(direction) {
      if (!this.activeGame) return;
      if (direction === "left") {
        if (
          this.itemColor[this.position - 1] === "wait" &&
          this.itemColor[this.position - 11] === "wait" &&
          this.position % 10 !== 1
        ) {
          for (let i = 0; i < this.actualFigure.length; i++) {
            this.itemColor[this.position + this.actualFigure[i]] = "wait";
            this.itemColor[this.position - 1 + this.actualFigure[i]] =
              this.color;
          }
          this.position--;
        }
      } else if (direction === "right") {
        if (
          this.itemColor[this.position + 2] === "wait" &&
          this.itemColor[this.position - 8] === "wait" &&
          (this.position + 1) % 10 !== 0
        ) {
          console.log(this.position);
          for (let i = this.actualFigure.length; i >= 0; i--) {
            console.log(i);
            this.itemColor[this.position + this.actualFigure[i]] = "wait";
            this.itemColor[this.position + 1 + this.actualFigure[i]] =
              this.color;
          }
          this.position++;
        }
      } else if (direction === "down") {
        this.moveRectDown();
      }
    },
    delRow(n) {
      for (let j = n; j > 1; j--) {
        for (let i = 1; i <= 10; i++) {
          if (this.activeStonePosition[0] !== (j - 1) * 10 + i) {
            this.itemColor[j * 10 + i] = this.itemColor[(j - 1) * 10 + i];
          }
        }
      }
      for (let i = 1; i <= 10; i++) {
        this.itemColor[i] = "wait";
      }
    },

    randomNumber(min, max) {
      const num = Math.random() * (max - min + 1) + min;
      return Math.floor(num);
    },
    randomColor() {
      const n = this.randomNumber(1, 4);
      switch (n) {
        case 1:
          return "red";
        case 2:
          return "green";
        case 3:
          return "yellow";
        case 4:
          return "hotpink";
      }
    },
    changeSpeed(newSpeed) {
      clearInterval(this.activeGame);
      this.speed = newSpeed;
      if (this.activeGame) {
        this.activeGame = setInterval(() => {
          this.moveDown();
        }, this.speed);
      }
      return;
    },
    checkPoints() {
      if (
        this.itemColor[this.position - 1] === this.itemColor[this.position] ||
        this.itemColor[this.position + 1] === this.itemColor[this.position] ||
        this.itemColor[this.position + 10] === this.itemColor[this.position]
      ) {
        this.points = this.points + 1;
      }
      switch (this.points) {
        case 10: {
          this.lvl++;
          return this.changeSpeed(500);
        }
        case 20: {
          this.lvl++;
          return this.changeSpeed(400);
        }
        case 30: {
          this.lvl++;
          return this.changeSpeed(250);
        }
      }
    },
  },
};
</script>
<style lang="scss">
.playground {
  background-color: grey;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
  height: 600px;
  width: 450px;
  margin: 0 auto;
  .ground-item {
    border: 0.025px solid rgb(139, 136, 136);
  }
}
.points {
  font-size: 2rem;
}
</style>
