<template>
  <main class="w-2/3 mx-auto py-20">
    <span class="absolute right-5 bottom-5 text-diman-dark-gray">
      Developed by <a href="https://www.joeypereira.dev" target="_blank">Joey Pereira</a>
    </span>
    <div class="h-80 bg-diman-green-bg px-20 py-10 text-white font-impact relative">
      <div class="h-full bg-diman-gray-border p-2 grid grid-rows-2 grid-flow-col auto-cols-fr gap-2">
        <div ref="away-bg" class="h-full col-span-2 row-span-1 bg-blue-500 overflow-hidden flex items-center px-10 py-5"
             :style="{ background: `linear-gradient(0deg, white -40%, ${awayColor})`}">
          <div class="h-full flex items-center">
            <img ref="away-logo" class="h-full" :src="`logo/${awayLogo}.png`" alt="Away team logo">
            <h2 ref="away-name" class="ml-10 text-5xl">{{ awayName }}</h2>
          </div>
          <span ref="away-score" class="flex-1 text-right text-6xl">{{ awayRuns }}</span>
        </div>
        <div ref="home-bg" class="h-full col-span-2 row-span-1 bg-blue-500 overflow-hidden flex items-center px-10 py-5"
             :style="{ background: `linear-gradient(180deg, white -40%, ${homeColor})`}">
          <div class="h-full flex items-center">
            <img ref="home-logo" class="h-full" :src="`logo/${homeLogo}.png`" alt="Home team logo">
            <h2 ref="home-name" class="ml-10 text-5xl">{{ homeName }}</h2>
          </div>
          <span ref="home-score" class="flex-1 text-right text-6xl">{{ homeRuns }}</span>
        </div>
        <div class="h-full col-span-1 row-span-2 bg-blue-500 relative pl-10 bg-gradient-to-b from-diman-light-gray to-diman-dark-gray">
          <div ref="side" :style="{ background: `linear-gradient(${computedDegrees}, #C40606 50%, white 50%)`}" class="diamond absolute my-auto top-0 bottom-0 h-8 w-8 transform rotate-45"></div>
          <div ref="innings" class="text-6xl absolute my-auto top-0 bottom-0 h-full left-8 flex items-center">{{ innings }}</div>
          <div class="absolute transform rotate-45 left-32">
            <div :class="{active: baseTwo}" class="inline-block w-20 h-20 bg-white border-4 border-white"></div>
            <div :class="{active: baseOne}" class="right-block inline-block w-20 h-20 bg-white border-4 border-white"></div>
            <div :class="{active: baseThree}" class="left-block block w-20 h-20 bg-white border-4 border-white"></div>
          </div>
          <span ref="outs" class="absolute bottom-4 left-32 pl-2 text-5xl">Outs - {{ outs }}</span>
        </div>
      </div>
      <div ref="modal-hr" class="modal absolute top-0 left-0 h-full w-full bg-diman-orange flex items-center justify-center">
        <h2 ref="modal-hr-text" class="text-center modal-text text-7xl">Home</h2>
      </div>
      <div ref="modal-so" class="modal absolute top-0 left-0 h-full w-full bg-diman-orange flex items-center justify-center">
        <h2 ref="modal-so-text" class="text-center modal-text text-7xl">Strikeout</h2>
      </div>
    </div>
    <div class="mt-10 text-white">
      <h1 class="text-center text-4xl">Controls</h1>
      <div class="min-h-80 grid gap-4 auto-cols-fr grid-flow-col mt-5">
        <div>
          <div class="flex items-center">
            <span class="mr-2">Game</span>
            <div class="flex-1 h-0.5 bg-diman-dark-gray"></div>
          </div>
          <button class="mt-2 py-4 bg-diman-dark-gray block w-full text-center
                         border-b-2 border-diman-light-gray hover:border-white"
                         @click="executeHomeRun()">Home Run</button>
          <button class="mt-2 py-4 bg-diman-dark-gray block w-full text-center
                         border-b-2 border-diman-light-gray hover:border-white"
                         @click="executeStrikeout()">Strikeout</button>
        </div>
        <div>
          <div class="flex items-center">
            <span class="mr-2">Inning</span>
            <div class="flex-1 h-0.5 bg-diman-dark-gray"></div>
          </div>
          <div class="mt-2 flex">
            <button class="inline-block py-4 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                           @click="decrementInnings()">-1</button>
            <div class="w-2"></div>
            <button class="inline-block py-4 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                           @click="incrementInnings()">+1</button>
          </div>
          <button class="mt-2 py-4 bg-diman-dark-gray block w-full text-center
                         border-b-2 border-diman-light-gray hover:border-white"
                         @click="changeInningSide()">Change Side</button>
        </div>
        <div>
          <div class="flex items-center">
            <span class="mr-2">Outs</span>
            <div class="flex-1 h-0.5 bg-diman-dark-gray"></div>
          </div>
          <div class="mt-2 flex">
            <button class="inline-block py-4 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                           @click="decrementOuts()">-1</button>
            <div class="w-2"></div>
            <button class="inline-block py-4 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                           @click="incrementOuts()">+1</button>
          </div>
        </div>
        <div>
          <div class="flex items-center">
            <span class="mr-2">Away</span>
            <div class="flex-1 h-0.5 bg-diman-dark-gray"></div>
          </div>
          <label for="away-name" class="block text-diman-light-gray">Team Name</label>
          <input v-model="awayName" class="block w-full px-2 text-white bg-diman-dark-gray" id="away-name" type="text" placeholder="Away Team Name">
          <label for="away-color" class="block mt-5 text-diman-light-gray">Team Color</label>
          <input v-model="awayColor" class="block w-full px-2 bg-diman-dark-gray" id="away-color" type="color">
          <label for="away-logo" class="block mt-5 text-diman-light-gray">Team Logo</label>
          <select v-model="awayLogo" class="block w-full px-2 bg-diman-dark-gray" id="away-logo" type="color">
            <option v-for="logo in logos" :value="logo.value">
              {{ logo.text }}
            </option>
          </select>
          <div class="mt-7 flex">
            <button class="inline-block py-2 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                           @click="decrementAwayRuns()">-1<br>Runs</button>
            <div class="w-2"></div>
            <button class="inline-block py-2 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                           @click="incrementAwayRuns()">+1<br>Runs</button>
          </div>
        </div>
        <div>
          <div class="flex items-center">
            <span class="mr-2">Home</span>
            <div class="flex-1 h-0.5 bg-diman-dark-gray"></div>
          </div>
          <label for="home-name" class="block text-diman-light-gray">Team Name</label>
          <input v-model="homeName" class="block w-full px-2 text-white bg-diman-dark-gray" id="home-name" type="text" placeholder="Home Team Name">
          <label for="home-color" class="block mt-5 text-diman-light-gray">Team Color</label>
          <input v-model="homeColor" class="block w-full px-2 bg-diman-dark-gray" id="home-color" type="color">
          <label for="home-logo" class="block mt-5 text-diman-light-gray">Team Logo</label>
          <select v-model="homeLogo" class="block w-full px-2 bg-diman-dark-gray" id="home-logo" type="color">
            <option v-for="logo in logos" :value="logo.value">
              {{ logo.text }}
            </option>
          </select>
          <div class="mt-7 flex">
            <button class="inline-block py-2 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                           @click="decrementHomeRuns()">-1<br>Runs</button>
            <div class="w-2"></div>
            <button class="inline-block py-2 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                           @click="incrementHomeRuns()">+1<br>Runs</button>
          </div>
        </div>
        <div>
          <div class="flex items-center">
            <span class="mr-2">Bases</span>
            <div class="flex-1 h-0.5 bg-diman-dark-gray"></div>
          </div>
          <div class="mt-2 flex">
            <button :class="{'red-border': baseThree}"
                    class="inline-block py-4 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                    @click="toggleBaseThree()">3</button>
            <div class="w-2"></div>
            <button :class="{'red-border': baseTwo}"
                    class="inline-block py-4 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                           @click="toggleBaseTwo()">2</button>
            <div class="w-2"></div>
            <button :class="{'red-border': baseOne}"
                    class="inline-block py-4 bg-diman-dark-gray block flex-1 text-center
                           border-b-2 border-diman-light-gray hover:border-white"
                    @click="toggleBaseOne()">1</button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      // Team data
      awayName: "Away Team Name",
      awayColor: "#FF0000",
      awayLogo: "default",
      homeName: "Diman Bengals",
      homeColor: "#F79224",
      homeLogo: 'diman',

      // Game data
      homeRuns: 0,
      awayRuns: 0,
      innings: 0,
      inningBottom: false,
      outs: 0,

      // Bases
      baseOne: false,
      baseTwo: false,
      baseThree: false,

      logos: [
        { text: 'Blank', value: 'default' },
        { text: 'Diman Bengals', value: 'diman' },
        { text: 'Westport Wildcats', value: 'westport' }
      ]
    }
  },
  methods: {
    // Runs
    decrementAwayRuns() {
      if(this.awayRuns == 0) return
      this.awayRuns--
    },
    incrementAwayRuns() {
      this.awayRuns++
    },
    decrementHomeRuns() {
      if(this.homeRuns == 0) return
      this.homeRuns--
    },
    incrementHomeRuns() {
      this.homeRuns++
    },

    // Innings
    decrementInnings() {
      if(this.innings == 0) return
      this.innings--
    },
    incrementInnings() {
      this.innings++
    },
    changeInningSide() {
      this.inningBottom = !this.inningBottom
      this.baseOne = false; this.baseTwo = false; this.baseThree = false;
      if(!this.inningBottom) this.incrementInnings()
    },

    // Outs
    decrementOuts() {
      if(this.outs == 0) this.outs = 2
        else this.outs--
    },
    incrementOuts() {
      if(this.outs == 3) return
      this.outs++
      if(this.outs == 3) {
        setTimeout(function() {
          this.outs = 0
          this.changeInningSide()
        }.bind(this), 1000)
      }
    },

    // Bases
    toggleBaseOne() {
      this.baseOne = !this.baseOne
    },
    toggleBaseTwo() {
      this.baseTwo = !this.baseTwo
    },
    toggleBaseThree() {
      this.baseThree = !this.baseThree
    },

    // Modals
    executeHomeRun() {
      let modal = this.$refs["modal-hr"]
      let modalText = this.$refs["modal-hr-text"]
      // Enable modal
      modal.classList.add("modal-active")
      setTimeout(function() {
        // Enable modal text
        modalText.classList.add("modal-text-active")
        setTimeout(function() {
          // Disable modal text
          modalText.classList.remove("modal-text-active")
          setTimeout(function() {
            //Enable modal text
            modalText.innerHTML = "Run"
            modalText.classList.add("modal-text-active")
            // Disable modal text
            setTimeout(function() {
              modalText.classList.remove("modal-text-active")
            }, 500)
          }, 500)
        }, 500)
      }.bind(this), 700)

      // Disable modal
      setTimeout(function() {
        modal.classList.remove("modal-active")
        modalText.innerHTML = "Home"
      }.bind(this), 2600)
    },
    executeStrikeout() {
      let modal = this.$refs["modal-so"]
      let modalText = this.$refs["modal-so-text"]
      // Enable modal
      modal.classList.add("modal-active")
      setTimeout(function() {
        // Enable modal text
        modalText.classList.add("modal-text-active")
        setTimeout(function() {
          // Disable modal text
          modalText.classList.remove("modal-text-active")
        }, 800)
      }.bind(this), 700)

      // Disable modal
      setTimeout(function() {
        modal.classList.remove("modal-active")
      }.bind(this), 2000)
    }
  },
  computed: {
    computedDegrees: function() {
      if(this.inningBottom) {
        return "-45deg"
      } else {
        return "135deg"
      }
    }
  }
}
</script>

<style>
.diamond {
  left: -20px;
}

.right-block {
  margin-left: 7px;
}

.left-block {
  margin-top: 4px;
}

.active {
  background-color: #FF0000 !important;
}

.red-border {
  border-color: #C40606 !important;
}

.modal {
  user-select: none;
  transition: 0.75s all ease-in-out;
  opacity: 0;
}

.modal-text {
  transition: 0.5s all;
  opacity: 0 !important;
  text-shadow: 0 0 5px black;
}

.modal-text-active {
  transform: scale(1.25);
  transition: 0.5s all;
  opacity: 1 !important;
}

.modal-active {
  transition: 0.75s all ease-in-out;
  opacity: 1 !important;
}
</style>
