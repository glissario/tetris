<template>
  <article>
    <h1 @click="moveDown">
      My Minimal Tetris - press 'Enter'
      {{ this.activeGame ? "to pause" : "to start" }}
    </h1>

    <div class="playground">
      <GroundItem
        v-for="(item, index) of 150"
        :key="index"
        :id="index + 1"
        :index="index + 1"
        :activeStatus="activeStatus[index + 1]"
      />
    </div>
    <p class="points">Points: {{ points }}</p>
    <p class="points">Level: {{ lvl }}</p>
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
      position: this.randomNumber(1, 10),
      activeStonePosition: "",
      activeStatus: [],
      activeGame: false,
      color: "",
      points: 0,
      lvl: 1,
      speed: 700,
    };
  },
  created() {
    for (let i = 1; i <= 150; i++) {
      this.activeStatus[i] = "wait";
    }
    this.color = this.randomColor();
    this.activeStatus[this.position] = this.color;
    window.addEventListener("keydown", (e) => {
      console.log(e.key);
      if (e.key === "ArrowLeft") {
        this.moveManual("left");
      } else if (e.key === "ArrowRight") {
        this.moveManual("right");
      } else if (e.key === "ArrowDown") {
        this.moveManual("down");
      } else if (e.key === "Enter") {
        if (!this.activeGame) {
          this.activeGame = setInterval(() => {
            this.moveDown();
          }, this.speed);
        } else {
          clearInterval(this.activeGame);
          this.activeGame = false;
        }
      }
    });
  } /*
  mounted() {
    if (this.activeGame) {
      this.activeGame = setInterval(() => {
        this.moveDown();
      }, this.speed);
    }
  },*/,
  methods: {
    moveDown() {
      if (
        this.position > 140 ||
        this.activeStatus[this.position + 10] !== "wait"
      ) {
        this.activeStatus[this.position] = this.color;
        this.checkPoints();
        this.color = this.randomColor();
        this.position = this.randomNumber(1, 10);
        this.activeStatus[this.position] !== "wait"
          ? clearInterval(this.activeGame)
          : (this.activeStatus[this.position] = this.color);
        return;
      }
      this.activeStatus[this.position] = "wait";
      this.position = this.position + 10;
      this.activeStatus[this.position] = this.color;
    },
    moveManual(direction) {
      if (direction === "left") {
        if (
          this.activeStatus[this.position - 1] === "wait" &&
          this.position % 10 !== 1
        ) {
          this.activeStatus[this.position] = "wait";
          this.position--;
          this.activeStatus[this.position] = this.color;
        }
      } else if (direction === "right") {
        if (
          this.activeStatus[this.position + 1] === "wait" &&
          this.position % 10 !== 0
        ) {
          this.activeStatus[this.position] = "wait";
          this.position++;
          this.activeStatus[this.position] = this.color;
        }
      } else if (direction === "down") {
        this.moveDown();
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
        this.activeStatus[this.position - 1] ===
          this.activeStatus[this.position] ||
        this.activeStatus[this.position + 1] ===
          this.activeStatus[this.position] ||
        this.activeStatus[this.position + 10] ===
          this.activeStatus[this.position]
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
