<template>
  <div class="tris-grid">
    <div 
      v-for="(item, index) in grid" 
      v-bind:key="index" 
      v-bind:class="getContainerClass(item, index)"
      v-on:click="putX(index)"
    >
      <!-- X element -->
      <div class="X" v-bind:class="getItemClass(item, index, 'X')">
        <div>&nbsp;</div>
        <div>&nbsp;</div>
      </div>

      <!-- O element -->
      <div class="O" v-bind:class="getItemClass(item, index, 'O')">&nbsp;</div>
    </div>
  </div>
</template>

<script>
  const defaultData = function () {
    return {
      myTurn: true,
      winLines: [],
      grid: {
        'A1': false,
        'A2': false,
        'A3': false,
        'B1': false,
        'B2': false,
        'B3': false,
        'C1': false,
        'C2': false,
        'C3': false
      },
      possibleCombinations: [
        ['A1','A2','A3'],
        ['A1','B2','C3'],
        ['A1','B1','C1'],
        ['A2','B2','C2'],
        ['A3','B2','C1'],
        ['A3','B3','C3'],
        ['B1','B2','B3'],
        ['C1','C2','C3']
      ]
    }
  };
  export default {
    name: 'TrisGrid',
    data: function() {
      return defaultData();    
    },
    methods: {
      //Action for putting my X in player's turn
      putX: function(index) {
        if(!this.myTurn) return; //If it's not my turn do nothing
        if(!this.grid[index]) {
          this.grid[index] = 'X'; //Set my X in this index
          this.myTurn = false;
          if(!this.checkWinner()) {
            setTimeout(this.computerTurn, 1000)
          } else {
            this.winX();
            setTimeout(this.resetGame, 2000);
          }
        }
      },

      //Computer AI decision
      computerTurn: function() {
        //Check avaiable choices
        let avaiablePositions = {};
        let xPositions = {};
        let oPositions = {};
        for(var index in this.grid) {
          avaiablePositions[index] = (!this.grid[index]) ? true : false;
          if(this.grid[index] === 'X') {
            xPositions[index] = true
          }
          if(this.grid[index] === 'O') {
            oPositions[index] = true
          }
        }

        //First turn
        if(Object.keys(xPositions).length === 1) {
          if(xPositions['A1'] || xPositions['A3'] || xPositions['C1'] || xPositions['C3']) {  
            if(avaiablePositions['B2']) {
              this.setAIDecision('B2');
              return;
            }
          } else {
            let choices = ['A1', 'A3', 'C1', 'C3'];
            this.setAIDecision(choices[this.getRand(0, choices.length)]);
          }
        } else {
          let decision = false;

          //Check enemy position and valutate the danger of lost this game and the possibility to win it
          let dangerValutation = [];
          let winValutation = [];
          this.possibleCombinations.map(combination => {
            let dangerCounter = 0;
            let winCounter = 0;
            combination.map(item => {
              if(xPositions[item]) dangerCounter++;
              if(oPositions[item]) winCounter++;
            })
            dangerValutation.push(dangerCounter);
            winValutation.push(winCounter);
          })
          
          //Manage win level 2, major decision for winning the game
          this.possibleCombinations.map((combination, index) => {
            if(winValutation[index] === 2) {
              combination.map(item => {
                if(avaiablePositions[item] && !decision) {
                  this.setAIDecision(item)
                  decision = true;
                  return;
                }
              })
            }
          })

          //Manage danger level 2, major decision for avoiding player's win
          this.possibleCombinations.map((combination, index) => {
            if(dangerValutation[index] === 2) {
              //Check if the danger was neutralized
              let danger = true;
              let avaiable = false;
              combination.map(item => {
                if(oPositions[item]) {
                  danger = false;
                }
                if(avaiablePositions[item]) {
                  avaiable = item;
                }
              })
              if(danger && !decision) {
                this.setAIDecision(avaiable)
                decision = true;
                return;
              }
            }
          })

          //Manage danger level 0, minor decisions
          this.possibleCombinations.map((combination, index) => {
            if(dangerValutation[index] === 0 && !decision) {
              let avaiable = [];
              combination.map(item => {
                if(avaiablePositions[item]) {
                  avaiable.push(item)
                }
              })
              if(!decision) {
                this.setAIDecision(avaiable[this.getRand(0, avaiable.length)])
                decision = true;
                return;
              }
            }
          })
        }
      },

      setAIDecision: function(index) {
        this.grid[index] = 'O';
        if(!this.checkWinner()) {
          this.myTurn = true;
        } else {
          this.winO();
          setTimeout(this.resetGame, 2000);
        }
      },

      getRand: function(min, max) {
        return Math.floor(Math.random() * max) + min;
      },

      //Check if exists a winner
      checkWinner: function() {
        let g = this.grid;
        let winner = false;

        //Horizontal match
        if(!winner) winner = this.compareValues(g['A1'], g['A2'], g['A3'], ['A1','A2','A3']);
        if(!winner) winner = this.compareValues(g['B1'], g['B2'], g['B3'], ['B1','B2','B3']);
        if(!winner) winner = this.compareValues(g['C1'], g['C2'], g['C3'], ['C1','C2','C3']);

        //Vertical match
        if(!winner) winner = this.compareValues(g['A1'], g['B1'], g['C1'], ['A1','B1','C1']);
        if(!winner) winner = this.compareValues(g['A2'], g['B2'], g['C2'], ['A2','B2','C2']);
        if(!winner) winner = this.compareValues(g['A3'], g['B3'], g['C3'], ['A3','B3','C3']);

        //Cross match
        if(!winner) winner = this.compareValues(g['A1'], g['B2'], g['C3'], ['A1','B2','C3']);
        if(!winner) winner = this.compareValues(g['A3'], g['B2'], g['C1'], ['A3','B2','C1']);

        this.checkIfDraw(); //Check if the game is finished

        return winner;
      },

      checkIfDraw: function() {
        let avaiablePositions = 0;
        for(var index in this.grid) {
          if(!this.grid[index]) avaiablePositions++
        }
        if(avaiablePositions === 0) {
          setTimeout(this.resetGame, 2000);
        }
      },

      //Compare lines
      compareValues: function(a, b, c, index) {
        if(a === b && a === c && a !== false) {
          this.winLines = index;
          return a;
        }
        return false;
      },

      //Manage multiple dynamic classes in container
      getContainerClass: function(item, index) {
        return [
          index,
          'cell',
          ((!item && this.myTurn) ? 'empty' : '')
        ].join(' ');
      },

      //Manage multiple dynamic classes in items
      getItemClass: function(item, index, target) {
        return [
          (item === target) ? 'show' : '',
          (this.winLines.includes(index) ? 'win' : '')
        ]
      },

      //Function for updating scores
      updateValue: function (key, value) {
        this.$emit(key, value);
      },

      //Reset the state of game for new play
      resetGame: function() {
        Object.assign(this.$data, defaultData());
      }
    },
    props: {
      winX: Function,
      winO: Function
    }
  }
</script>

<style lang="scss" scoped>
  $b: 5px solid white;
  $n: 5px solid transparent;

  @keyframes pulse {
    0% { opacity: 0.1; }
    50% { opacity: 0.8; }
    100% { opacity: 0.1; }
  }

  @keyframes x-animation {
    0% { transform: rotate(-45deg) }
    100% { transform: rotate(45deg); }
  }

  @keyframes o-animation {
    0% { transform: scale(0); }
    100% { transform: scale(1); }
  }

  @mixin generateBorder($left, $top, $right, $bottom) {
    border: {
      left: $left;
      top: $top;
      right: $right;
      bottom: $bottom;
    }
  }

  .tris-grid {
    @media screen and (orientation:portrait) {
      width: 80vw;
      height: 80vw;
    }
    @media screen and (orientation:landscape) {
      width: 80vh;
      height: 80vh;
    }
    
    padding: 1%;
    display: grid;
    grid-template-columns: 33.33% 33.33% 33.33%;
    grid-template-rows: 33.33% 33.33% 33.33%;

    .cell {
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      
      &.A1 {
        @include generateBorder($n, $n, $b, $b);
      }

      &.A2 {
        @include generateBorder($b, $n, $b, $b);
      }

      &.A3 {
        @include generateBorder($b, $n, $n, $b);
      }

      &.B1 {
        @include generateBorder($n, $b, $b, $b);
      }

      &.B2 {
        @include generateBorder($b, $b, $b, $b);
      }

      &.B3 {
        @include generateBorder($b, $b, $n, $b);
      }

      &.C1 {
        @include generateBorder($n, $b, $b, $n);
      }

      &.C2 {
        @include generateBorder($b, $b, $b, $n);
      }

      &.C3 {
        @include generateBorder($b, $b, $n, $n);
      }

      &.empty {
        &:hover {
          .X {
            display: block;
            animation: pulse 1.3s infinite;
            div {
              background: grey;
            }
          }
        }
      }

      .X {
        display: none;
        position: relative;
        width: 100%;
        height: 100%;

        &.show {
          display: block;
          div:first-child {
            animation: x-animation 1s;
          }
        }

        &.win {
          div {
            background: green;
          }
        }

        div {
          position: absolute;
          top: 50%;
          left: 0;
          width: 100%;
          height: 20px;
          background: white;

          &:first-child {
            transform: rotate(45deg);
          }
          &:last-child {
            transform: rotate(-45deg);
          }
        }
      }

      .O {
        display: none;
        position: relative;
        width: 90%;
        height: 90%;
        border: 20px solid white;
        border-radius: 100%;

        &.show {
          display: block;
          animation: o-animation 1s;
        }

        &.win {
          border-color: green;
        }
      }
      
    }
  }

  @media screen and (max-width: 700px) {
    .X {
      div {
        height: 7px !important;
      }
    }

    .O {
      border: 7px solid white !important;
    }
  }
</style>
