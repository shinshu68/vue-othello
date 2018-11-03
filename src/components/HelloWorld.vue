<template>
  <div class="othello">
    <ul v-for="i in 8" :key="i">
      <li v-for="j in 8" :key="j">
        <b-img v-if="field[i-1][j-1]==1" rounded="circle" blank width="35" blank-color="white" alt="named color" class="m-1"/>
        <b-img v-else-if="field[i-1][j-1]==2" rounded="circle" blank width="35" blank-color="black" alt="named color" class="m-1"/>

        <b-img v-else-if="turn%2 == 0 && whitePutAble[i-1][j-1]==1" blank width="35" @click="putWhite(i-1, j-1)" blank-color="Beige" alt="named color" class="m-1"/>
        <b-img v-else-if="turn%2 == 1 && blackPutAble[i-1][j-1]==1" blank width="35" @click="putBlack(i-1, j-1)" blank-color="Gray" alt="named color" class="m-1"/>

        <b-img v-else-if="field[i-1][j-1]==0" blank width="35" blank-color="green" alt="named color" class="m-1"/>
      </li>
    </ul>
    <div>
      <h3>white: {{countPieces(1)}} black: {{countPieces(2)}}</h3>
    </div>
  </div>


</template>

<script>

export default {
  name: 'HelloWorld',
  data () {
    return {
      turn: 0,

      field: [[0,0,0,0,0,0,0,0],
              [0,0,0,0,0,0,0,0],
              [0,0,0,0,0,0,0,0],
              [0,0,0,1,2,0,0,0],
              [0,0,0,2,1,0,0,0],
              [0,0,0,0,0,0,0,0],
              [0,0,0,0,0,0,0,0],
              [0,0,0,0,0,0,0,0]],

      whitePutAble: [[0,0,0,0,0,0,0,0],
                     [0,0,0,0,0,0,0,0],
                     [0,0,0,0,1,0,0,0],
                     [0,0,0,0,0,1,0,0],
                     [0,0,1,0,0,0,0,0],
                     [0,0,0,1,0,0,0,0],
                     [0,0,0,0,0,0,0,0],
                     [0,0,0,0,0,0,0,0]],

      blackPutAble: [[0,0,0,0,0,0,0,0],
                     [0,0,0,0,0,0,0,0],
                     [0,0,0,1,0,0,0,0],
                     [0,0,1,0,0,0,0,0],
                     [0,0,0,0,0,1,0,0],
                     [0,0,0,0,1,0,0,0],
                     [0,0,0,0,0,0,0,0],
                     [0,0,0,0,0,0,0,0]]
    }
  },

  methods: {
    setBlackEightLine(i, j, dx, dy) {
      while(true) {
        i += dy;
        j += dx;
        if (i < 0 || i >= 8) break;
        if (j < 0 || j >= 8) break;
        if (this.field[i][j] == 0) this.$set(this.blackPutAble[i], j, 1);
      }
    },

    setWhiteEightLine(i, j, dx, dy) {
      while(true) {
        i += dy;
        j += dx;
        if (i < 0 || i >= 8) break;
        if (j < 0 || j >= 8) break;
        if (this.field[i][j] == 0) this.$set(this.whitePutAble[i], j, 1);
      }
    },

    trunAbleWhitePiecesCount(i, j) {
      var n = i, m = j;
      var dx = [-1,0,1,-1,1,-1,0,1];
      var dy = [-1,-1,-1,0,0,1,1,1];
      var c = 0;
      var t = 0;
      for (var k = 0; k < 8; k++) {
        t = 0;
        n = i; m = j;
        while (true) {
          n += dy[k];
          m += dx[k];
          if (n < 0 || n >= 8) break;
          if (m < 0 || m >= 8) break;
          if (this.field[n][m] == 0) break;
          if (this.field[n][m] == 1) t++;
          if (this.field[n][m] == 2) {
            if (t != 0) c += t;
            break;
          }
        }
      }

      return c;
    },

    trunAbleBlackPiecesCount(i, j) {
      var n = i, m = j;
      var dx = [-1,0,1,-1,1,-1,0,1];
      var dy = [-1,-1,-1,0,0,1,1,1];
      var c = 0;
      var t = 0;
      for (var k = 0; k < 8; k++) {
        t = 0;
        n = i; m = j;
        while (true) {
          n += dy[k];
          m += dx[k];
          if (n < 0 || n >= 8) break;
          if (m < 0 || m >= 8) break;
          if (this.field[n][m] == 0) break;
          if (this.field[n][m] == 2) t++;
          if (this.field[n][m] == 1) {
            if (t != 0) c += t;
            break;
          }
        }
      }
      return c;
    },

    putWhite(i, j) {
      this.$set(this.field[i], j, 1);
      this.turnBlackPieces(i, j);
      this.remakeWhitePutAble();
      this.remakeBlackPutAble();
      this.turn++;
    },

    putBlack(i, j) {
      this.$set(this.field[i], j, 2);
      this.turnWhitePieces(i, j)
      this.remakeWhitePutAble();;
      this.remakeBlackPutAble();
      this.turn++;
    },

    remakeWhitePutAble() {
      var i,j,k;
      var f;
      var dx = [-1,0,1,-1,1,-1,0,1];
      var dy = [-1,-1,-1,0,0,1,1,1];

      for (i = 0; i < 8; i++) {
        for (j = 0; j < 8; j++) {
          this.$set(this.whitePutAble[i], j, 0);
        }
      }

      for (i = 0; i < 8; i++) {
        for (j = 0; j < 8; j++) {
          if (this.field[i][j] == 1) {
            for(k = 0; k < 8; k++) {
              this.setWhiteEightLine(i,j,dx[k],dy[k]);
            }
          }
        }
      }

      for (i = 0; i < 8; i++) {
        for (j = 0; j < 8; j++) {
          if (this.whitePutAble[i][j] == 1) {
            var c = this.trunAbleBlackPiecesCount(i,j);
            if (c == 0) this.$set(this.whitePutAble[i], j, 0);
          }
        }
      }
    },

    remakeBlackPutAble() {
      var i,j,k;
      var f;
      var dx = [-1,0,1,-1,1,-1,0,1];
      var dy = [-1,-1,-1,0,0,1,1,1];

      for (i = 0; i < 8; i++) {
        for (j = 0; j < 8; j++) {
          this.$set(this.blackPutAble[i], j, 0);
        }
      }

      for (i = 0; i < 8; i++) {
        for (j = 0; j < 8; j++) {
          if (this.field[i][j] == 2) {
            for(k = 0; k < 8; k++) {
              this.setBlackEightLine(i,j,dx[k],dy[k]);
            }
          }
        }
      }

      for (i = 0; i < 8; i++) {
        for (j = 0; j < 8; j++) {
          if (this.blackPutAble[i][j] == 1) {
            var c = this.trunAbleWhitePiecesCount(i,j);
            //if (i == 5 && j == 4) console.log();
            if (c == 0) this.$set(this.blackPutAble[i], j, 0);
          }
        }
      }
    },

    turnBlackPieces(i, j) {
      var n = i, m = j;
      var dx = [-1,0,1,-1,1,-1,0,1];
      var dy = [-1,-1,-1,0,0,1,1,1];
      var t = 0;
      for (var k = 0; k < 8; k++) {
        t = 0;
        n = i; m = j;
        while (true) {
          n += dy[k];
          m += dx[k];
          if (n < 0 || n >= 8) break;
          if (m < 0 || m >= 8) break;
          if (this.field[n][m] == 0) break;
          if (this.field[n][m] == 2) t++;
          if (this.field[n][m] == 1) {
            if (t != 0) this.turnPieces(1,i,j,n,m,dx[k],dy[k]);
            break;
          }
        }
      }
    },

    turnWhitePieces(i, j) {
      var n = i, m = j;
      var dx = [-1,0,1,-1,1,-1,0,1];
      var dy = [-1,-1,-1,0,0,1,1,1];
      var t = 0;
      for (var k = 0; k < 8; k++) {
        t = 0;
        n = i; m = j;
        while (true) {
          n += dy[k];
          m += dx[k];
          if (n < 0 || n >= 8) break;
          if (m < 0 || m >= 8) break;
          if (this.field[n][m] == 0) break;
          if (this.field[n][m] == 1) t++;
          if (this.field[n][m] == 2) {
            if (t != 0) this.turnPieces(2,i,j,n,m,dx[k],dy[k]);
            break;
          }
        }
      }
    },

    turnPieces(color, i, j, n, m, dx, dy) {
      while(i != n || j != m) {
        this.$set(this.field[i], j, color);
        i += dy;
        j += dx;
      }
    },

    countPieces(color) {
      var c = 0;
      this.field.forEach(row => {
        row.forEach(element => {
          if(element == color) c++;
        })
      });
      return c;
    },
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
div.othello li {
  background: green;
  display: inline-block;
  margin: 1px;
}

div.othello ul {
  padding: 0;
  margin: 0;
  background: black;
}
</style>
