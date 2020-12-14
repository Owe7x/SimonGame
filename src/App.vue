<template>
  <div id="app">
  <audio ref="sound1" src="https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"></audio>
  <audio ref="sound2" src="https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"></audio>
  <audio ref="sound3" src="https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"></audio>
  <audio ref="sound4" src="https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"></audio>
  <div id="simon">
    <div id="buttons">
      <div class="button" :class="{highlight: hlGreen}" id="green" @click="input(1)"></div>
      <div class="button" :class="{highlight: hlRed}" id="red" @click="input(2)"></div>
      <div class="button" :class="{highlight: hlYellow}" id="yellow" @click="input(3)"></div>
      <div class="button" :class="{highlight: hlBlue}" id="blue" @click="input(4)"></div>
    </div>
    <div id="center">
      <div id="controls">
        <div id="counter">
          <span>Round:</span>
          <span :class="{lit: power}" v-text="showError ? 'Oops' : (showWin ? '* *' : displayCount)"></span>
        </div>
        <a id="easy" class="btn" @click="easy"></a>
        <a id="medium" class="btn" @click="medium"></a>
        <a id="hard" class="btn" @click="medium"></a>
      </div>
      <div id="power">
        <p>Off</p>
        <div id="switch" :class="{on: power}" @click="togglePower"></div>
        <p>On</p>
      </div>
    </div>

  </div>
  
</div>

</template>

<script>
export default {
  name: 'Simon',
  data () {
    return {
      power: false,   // Is the power turned on?
      started: false, // Has the game started?
      count: 0,       // What's the current count?
      series: [],     // The current series of tones
      playingSeries: false, // Is the game currently outputting the series of tones?
      userInput: [],
      hlRed: false,
      hlGreen: false,
      hlYellow: false,
      hlBlue: false,
      allowInput: false,
      showError: false,
      showWin: false,
      totalToWin: 20,
      easyStart: false,
      mediumStart: false,
      hardStart: false
    }
  },
  computed: {
    displayCount() {
      if (this.count == 0)
        return ""
      else
        return this.count
    }
  },
  methods: {
    reset() {
      // Reset the game state
      this.started = false
      this.count = 0
      this.series = []
      this.userInput = []
      this.allowInput = false
      this.playingSeries = false
      this.showError = false
      this.showWin = false
      this.hlGreen = false
      this.hlRed = false
      this.hlYellow = false
      this.hlBlue = false
      this.easyStart = false
      this.mediumStart = false
      this.hardStart = false
    },
    // This method executes when the user wins the game
    winner() {
      this.showWin = true
      let self = this
      let delay = 500
      this.hlGreen = true; 
      for (let x = 1; x <= 6; x++) {
        setTimeout(function() { self.playTone(1) }, delay)
        if (x == 6)
          setTimeout(function() { self.hlGreen = false; self.showWin = false; self.reset() }, delay + 500)
        delay += 500
      }
    },
    input(tone) {
      if (!this.allowInput)
        return
      this.playTone(tone)
      this.userInput.push(tone)
      // Check if this was the wrong input
      if (this.userInput[this.userInput.length - 1] != this.series[this.userInput.length - 1]) {
        this.userInput = []
        this.allowInput = false
        let self = this
        this.showError = true
        return
      }
      // If this was the final input
      if (this.userInput.length == this.series.length) {
        let self = this
        this.userInput = []
        // Check if we won (20 correct inputs)
        if (this.series.length == this.totalToWin) {
          // User has won the game
          setTimeout(this.winner, 500)
        } else {
          setTimeout(function() { 
            self.addTone()
            if(self.easyStart) {
              self.playEasy()
            }
            if(self.mediumStart) {
              self.playMedium()
            }
            if(self.hardStart) {
              seld.playHard()
            }



          }, 1000)
        }
      }
    },
    togglePower() {
      // Toggle power
      this.power = !this.power
      // Reset if power is turned off
      if (!this.power) {
        this.reset()
        this.stopTones()
      }
    },
    easy() {
      if (this.power && this.count == 0) {
        this.started = true
        this.easyStart = true
        this.addTone()
        this.playEasy()
      }
    },
    medium() {
      if (this.power && this.count == 0) {
        this.started = true
        this.mediumStart = true
        this.addTone()
        this.playMedium()
      }
    },
    hard() {
      if (this.power && this.count == 0) {
        this.started = true
        this.hardStart = true
        this.addTone()
        this.playHard()
      }
    },
    addTone() {
      this.count++
      this.series.push(this.randomTone())
    },
    playEasy() {
      this.allowInput = false
      this.playingSeries = true
      let self = this
      let delay = 1000
      this.series.forEach(function(tone, index, array) {
        if (index == array.length - 1 )
          setTimeout(function() { 
            if (self.started) {
            self.playTone(tone)
            self.allowInput = true
            self.playingSeries = false
            }
          }, delay)
        else
          setTimeout(function() { self.playTone(tone) }, delay)
        delay += 1500
      })
      this.playingSeries = false
    },
    playMedium() {
      this.allowInput = false
      this.playingSeries = true
      let self = this
      let delay = 1000
      this.series.forEach(function(tone, index, array) {
        if (index == array.length - 1 )
          setTimeout(function() { 
            if (self.started) {
            self.playTone(tone)
            self.allowInput = true
            self.playingSeries = false
            }
          }, delay)
        else
          setTimeout(function() { self.playTone(tone) }, delay)
        delay += 1000
      })
      this.playingSeries = false
    },
    playHard() {
      this.allowInput = false
      this.playingSeries = true
      let self = this
      let delay = 1000
      this.series.forEach(function(tone, index, array) {
        if (index == array.length - 1)
          setTimeout(function() { 
            if (self.started) {
            self.playTone(tone)
            self.allowInput = true
            self.playingSeries = false
            }
          }, delay)
        else
          setTimeout(function() { self.playTone(tone) }, delay)
        delay += 400
      })
      this.playingSeries = false
    },
    clearHighlights() {
      this.hlGreen = false
      this.hlRed = false
      this.hlYellow = false
      this.hlBlue = false
    },
    // Plays the tone & highlights the color
    playTone(tone) {
      if (!this.power)
        return
      switch (tone) {
        case 1:
          this.$refs.sound1.pause()
          this.$refs.sound1.currentTime = 0
          this.$refs.sound1.play()
          this.hlGreen = true
          break;
        case 2:
          this.$refs.sound2.pause()
          this.$refs.sound2.currentTime = 0
          this.$refs.sound2.play()
          this.hlRed = true
          break;
        case 3:
          this.$refs.sound3.pause()
          this.$refs.sound3.currentTime = 0
          this.$refs.sound3.play()
          this.hlYellow = true
          break;
        case 4:
          this.$refs.sound4.pause()
          this.$refs.sound4.currentTime = 0
          this.$refs.sound4.play()
          this.hlBlue = true
          break;
      }
      if (!this.showWin)
        setTimeout(this.clearHighlights, 750)
    },
    stopTones() {
      this.$refs.sound1.pause()
      this.$refs.sound2.pause()
      this.$refs.sound3.pause()
      this.$refs.sound4.pause()
      this.$refs.sound1.currentTime = 0
      this.$refs.sound2.currentTime = 0
      this.$refs.sound3.currentTime = 0
      this.$refs.sound4.currentTime = 0
    },
    randomTone() {
      return Math.floor(Math.random() * 4) + 1
    }
  }
}
</script>

<style lang="scss">
// Colors
$color-black: #000;
$color-dark-gray: #2e2e2e;
$color-med-gray: #666;

$color-green: #BEDE15;
$color-red: #FF5643;
$color-yellow: #FEEF33;
$color-blue: dodgerblue;
$color-white: #fff;
$color-light-green: lighten(green, 5%);
$color-light-red: lighten(red, 5%);
$color-light-blue: lighten(#0000cd, 5%);
$color-light-yellow: lighten(#ffd700, 5%);
$color-bisque: #ffe4c4;

// Main Styles
body {
  font-family: 'Open Sans', sans-serif;
  user-select: none;
  background-color: $color-white;
}
h1 {
  font-family: 'Russo One', sans-serif;
  font-size: 3rem;
  cursor: default;
  margin: 1rem 0;
}
#simon {
  margin: 2rem auto 0 auto;
  max-width: 630px;
  min-width: 630px;
  max-height: 330px;
  min-height: 330px;
  display: flex;
  align-items: center;
  justify-content: center;
}
#buttons {
  display: flex;
  flex-wrap: wrap;
  max-width: 320px;
  min-width: 320px;
  margin-right: 80px;
  justify-content: space-between;
}
.button {
  cursor: pointer;
  min-width: 160px;
  min-height: 160px;
  max-width: 160px;
  max-height: 160px;
}
#green { 
  background-color: $color-green; 
  border-top-left-radius: 300px;
  box-shadow: inset 3px 3px 10px rgba(255,255,255,.3);
}
#blue { 
  background-color: $color-blue; 
  border-bottom-right-radius: 300px;
  box-shadow: inset -3px -3px 10px rgba(255,255,255,.3);
}
#yellow { 
  background-color: $color-yellow; 
  border-bottom-left-radius: 300px;
  box-shadow: inset 3px -3px 10px rgba(255,255,255,.3);
}
#red { 
  background-color: $color-red; 
  border-top-right-radius: 300px;
  box-shadow: inset -3px 3px 10px rgba(255,255,255,.3);
}
#green.highlight { background-color: $color-light-green; }
#red.highlight { background-color: $color-light-red; }
#blue.highlight { background-color: $color-light-blue; }
#yellow.highlight { background-color: $color-light-yellow; }

// Center Controls
#center {
  box-sizing: border-box;
  min-width: 200px;
  max-width: 200px;
  min-height: 200px;
  max-height: 200px;
  border-radius: 50%;
  display: flex;
  flex-direction: column;
  color: $color-dark-gray;
}
#controls {
  display: flex;
  flex-direction: column;
  min-width: 180px;
  
}
#counter {
  display: inline-block;
  width: 100px;
  height: 40px;
  color: $color-med-gray;
  display: flex;
  font-size: 1.6rem;
}
.lit { color: $color-red; }
#power {
  margin-top: 1rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 110px;
  font-size: .875rem;
  text-transform: uppercase;
  font-weight: bold;
}
#switch { 
  cursor: pointer;
  display: inline-block; 
  width: 50px;
  height: 25px;
  background-color: $color-dark-gray;
  position: relative;
}
#switch:after {
  content: '';
  position: absolute;
  top: 0;
  height: 19px;
  width: 19px;
  border: 3px solid $color-dark-gray; 
  background-color: $color-red;
}
#switch.off:after { left: 0 }
#switch.on:after { right: 0 }
.btn {
  cursor: pointer;
  position: relative;
  padding: 10px 10px 6px 10px;
  width: 50px;
  height: 24px;
  background-color: $color-yellow;
  border-radius: 5px;
  border: none;
  margin-top: 1rem;
  box-shadow: 2px 2px 3px rgba(0,0,0,.4);
}
.btn::after {

  font-size: 12px;
  text-transform: uppercase;
  font-weight: bold;
}
.btn:hover { background-color: $color-light-yellow; }
#easy.btn { background-color: $color-red; }
#easy.btn:hover { background-color: $color-light-red; }
#medium.btn { background-color: $color-green; }
#medium.btn:hover { background-color: $color-light-green; }
#hard.btn { background-color: $color-blue; }
#hard.btn:hover { background-color: $color-light-blue; }


#easy::after { content: 'Easy'; }
#medium::after { content: 'Medium'; }
#hard::after { content: 'Hard'; }


</style>
