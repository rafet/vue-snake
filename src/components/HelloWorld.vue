<template>
  <div>
    <center>
      <div class="mb-5 mt-5">
        <h4>Snake-vue</h4>
        <div style="opacity:.6">
          <span>Rafet Topçu</span>
          |
          <span>
            <a href="https://github.com/rafettopcu" target="_blank">github</a>
          </span>
          <p>Score : {{ road.length - 3 }}</p>
        </div>
      </div>
      <table style="">
        <div v-for="_ in size" :key="_">
          <div
            class="cell"
            v-for="__ in size"
            :key="__"
            :style="{
              background: road.includes((_ - 1) * size + __ - 1)
                ? 'black'
                : (food[0] - 1) * size + food[1] - 1 === (_ - 1) * size + __ - 1
                ? 'white'
                : '#a5d1af'
            }"
          ></div>
        </div>
        <div v-if="end" class="end-panel">
          <h4>GAME OVER</h4>
          <p @click="replay">Replay</p>
        </div>
      </table>
    </center>
  </div>
</template>

<script>
export default {
  data() {
    return {
      size: 25,
      road: [0, 1, 2],
      currentWay: 's',
      food: [-1, -1],
      snakeLength: 1,
      end: false,
      wayChanged: false,
      maxSpeed: 60,
      startSpeed: 180
    };
  },
  computed: {
    speed() {
      const x = this.startSpeed - this.road.length * 10;
      return x >= this.maxSpeed ? x : this.maxSpeed;
    }
  },
  created() {
    this.spawnFood();
    this.move();
  },
  mounted() {
    window.addEventListener(
      'keypress',
      function(e) {
        const key = String.fromCharCode(e.keyCode);
        if (key === 'a' || key === 'A') {
          this.changeWay('a');
        }
        if (key === 'd' || key === 'D') {
          this.changeWay('d');
        }
        if (key === 'w' || key === 'W') {
          this.changeWay('w');
        }
        if (key === 's' || key === 'S') {
          this.changeWay('s');
        }
      }.bind(this)
    );
  },
  methods: {
    async move() {
      let last = this.road[this.road.length - 1];
      const modula = last % this.size;
      let new_road = '';
      if (this.currentWay === 'd') {
        if ((modula + 1) % this.size === 0) {
          last -= this.size;
        }
        new_road = last + 1;
      } else if (this.currentWay === 's') {
        if (last > this.size * this.size) {
          last -= this.size * this.size + this.size;
        }
        new_road = last + this.size;
      } else if (this.currentWay === 'a') {
        if (modula === 0) {
          last += this.size;
        }
        new_road = last - 1;
      } else if (this.currentWay === 'w') {
        if (last < 0) {
          last += this.size * this.size + this.size;
        }
        new_road = last - this.size;
      }
      console.log('n', new_road);

      if (this.road.includes(new_road)) {
        this.end = true;
        return;
      } else this.road.push(new_road);
      if ((this.food[0] - 1) * this.size + this.food[1] - 1 === last) {
        this.spawnFood();
      } else this.road = this.road.slice(1, this.road.length);

      setTimeout(() => {
        this.move();
        this.wayChanged = false;
      }, this.speed);
    },
    spawnFood() {
      const r1 = Math.floor(Math.random() * (this.size - 1)) + 1;
      const r2 = Math.floor(Math.random() * (this.size - 1)) + 1;
      this.food[0] = r1;
      this.food[1] = r2;
      console.log((this.food[0] - 1) * this.size + this.food[1] - 1);
    },
    changeWay(way) {
      if (this.wayChanged) return;
      if (way === 'd' && this.currentWay === 'a') return;
      if (way === 'a' && this.currentWay === 'd') return;
      if (way === 'w' && this.currentWay === 's') return;
      if (way === 's' && this.currentWay === 'w') return;
      this.currentWay = way;
      this.wayChanged = true;
    },
    replay() {
      this.size = 25;
      this.road = [0, 1, 2];
      this.currentWay = 's';
      this.food = [-1, -1];
      this.snakeLength = 1;
      this.end = false;
      this.wayChanged = false;
      this.maxSpeed = 60;
      this.startSpeed = 250;
      this.spawnFood();
      this.move();
    }
  }
};
</script>

<style>
body {
  background: #89b894 !important;
}
.cell {
  margin: 0;
  padding: 8px;
  margin: 1px;
  float: left;
  border-radius: 2px;
}
.end-panel {
  text-align: center;
  padding: 180px;
  background: #000000aa;
  height: 100%;
  width: 100%;

  height: 449px;
  left: 0;
  position: absolute;
}
.end-panel h4,
p {
  color: white;
}
.end-panel p {
  cursor: pointer;
}
</style>
