<template>
    <div class="app">
      <div class="timer" 
        :class="{ 'complite': timers[activeTimer].timerCountdown == 0, 
                 'paused': timers[activeTimer].isPaused == true }">
        <Timer :countdown="timers[activeTimer].timerCountdown" />
      </div>
      <div class="btn-container">
        <label class="timer-name">Таймер {{ timers[activeTimer].idTimer + 1 }}</label>
        <input ref="input" class="input" v-model="inputValue" type="number" min="1" 
        placeholder="Введите количество минут" @keyup.enter="startTimer(activeTimer)" @input="checkInput">
        <button class="btn" @click="startTimer(activeTimer)">{{ timers[activeTimer].timerCountdown !== 0 ? "Сбросить": 'Старт' }}</button>
        <button class="btn" @click="toggleTimer" :disabled="timers[activeTimer].timerCountdown == 0">
            {{ timers[activeTimer].isPaused === true ? 'Продолжить' : 'Пауза' }}
        </button>
        <button class="btn" @click="switchTimer">Переключить таймер</button>
      </div>
    </div>
</template>
  
<script>
  import Timer from './components/Timer.vue';
  
  export default {
    components: {
      Timer,
    },
    data() {
      return {
        activeTimer: JSON.parse(localStorage.getItem('activeTimer')) || 0,
        inputValue: '',
        timers: JSON.parse(localStorage.getItem('timers')) || [
          { idTimer: 0, timerCountdown: 0, timerInterval: null, isPaused: false},
          { idTimer: 1, timerCountdown: 0, timerInterval: null, isPaused: false},
          // { idTimer: 2, timerCountdown: 0, timerInterval: null, isPaused: false},
        ]
      }
    },
    watch: {
        activeTimer: {
            handler(newVal) {
            localStorage.setItem('activeTimer', JSON.stringify(newVal));
            },
            deep: true
        },
        timers: {
            handler(newVal) {
            localStorage.setItem('timers', JSON.stringify(newVal));
            },
            deep: true
        }
    },
    mounted() {
        this.timers.forEach((timer, index) => {
            if (timer.timerCountdown > 0) {
            this.startTimer(index, true);
            }
        });
    },
    methods: {
        startTimer(id, local=false) {
            this.timers[id].isPaused = false;
            clearInterval(this.timers[id].timerInterval);
            if(!local) this.setCountdown(id)
            this.timers[id].timerInterval = setInterval(() => {
                if (!this.timers[id].isPaused) {
                if (this.timers[id].timerCountdown > 0) {
                        this.timers[id].timerCountdown--;
                    } else {
                        clearInterval(this.timers[id].timerInterval);
                        this.timers[id].timerInterval = null;
                    }
                }
            }, 1000);
            this.inputValue = '';
        },

        setCountdown(id) {
            clearInterval(this.timers[id].timerInterval);
            if ( this.inputValue == '') this.inputValue = 0
            const countdownInSeconds = parseInt(this.inputValue) * 60;
            if (countdownInSeconds >= 0) {
                this.timers[id].timerCountdown = countdownInSeconds;
            }
        },

        checkInput() {
            if ( this.inputValue == '' || this.inputValue < 0 || this.inputValue > 999999) {
                this.$refs.input.style.borderColor = 'red'
                this.inputValue = ''
            } else {
                this.$refs.input.style.borderColor = '';
            }
        },

        toggleTimer() {
            if (this.timers[this.activeTimer].timerInterval) {
                this.timers[this.activeTimer].isPaused = !this.timers[this.activeTimer].isPaused
            }
        },

        switchTimer() {
            if ( this.activeTimer === this.timers.length - 1) {
            this.activeTimer = 0;
            } else {
            this.activeTimer++;
            }
        },
        
    },
}
</script>

<style>

* {
    margin: 0px;
    padding: 0px;
    box-sizing: border-box;
    font-family: 'Montserrat', sans-serif;
}

.app {
    display: flex;
    flex-direction: column;
    align-items: center;
    font-size: 32px;
    justify-content: center;
    min-height: 100vh;
    background-color: rgb(255,250,250);

    @media (max-width: 500px) {
        font-size: 16px;
    }
}

.timer {
    font-size: 120px;
    margin-bottom: 200px;
    text-shadow: 4px 4px 4px rgb(184, 184, 184);

    @media (max-width: 500px) {
        font-size: 80px;
        margin-bottom: 100px;
        text-shadow: 1px 1px 1px rgb(184, 184, 184);
    }
}

.complite {
    color: rgb( 11,218,81);
}

.paused {
    color: rgb(255,36,0);
}

.timer-name{
    font-size: 32px;
}

.input {
  padding:10px;
  border-radius:10px;
  width: 300px;
  border: 1px solid rgb(174, 174, 174);
  outline:none;
}

.btn {
    border: none;
    font-size: 28px;
    border-radius: 10px;
    color: white;
    transition: .2s linear;
    background: #0B63F6;
}

.btn:hover {
    box-shadow: 0 0 0 2px white, 0 0 0 4px #518ff9;
}

.btn:disabled {
    background: #6097f6;
}

.btn-container {
    display: flex;
    width: 300px;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 20px
}

.btn-container button {
    width: 100%;
    margin: 0 5px;
    font-size: 28px;
}

</style>