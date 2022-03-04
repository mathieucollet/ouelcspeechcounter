<template>
  <div class="flex justify-center items-center w-full h-full">
    <div class="w-2/3">
      <div style="height:45vh;" class="flex flex-col justify-end p-5 relative">
        <div
            class="w-full p-3 mb-2 rounded shadow-md uppercase flex justify-between items-center bg-white cursor-pointer"
            v-on:click="setSecondsTo(step)"
            v-for="step in pastSteps"
            :class="isActual(step)"
            :key="step.name">
          <span>{{ step.name }}</span>
          <span v-html="remainingTime(step.start, step.end)"></span>
        </div>
        <div id="pasted" class="absolute w-full h-full"></div>
      </div>
      <div style="height:10vh" class="px-5 flex flex-col justify-center">
        <div class="w-full h-full px-3 mb-2 rounded shadow-md uppercase flex justify-between items-center bg-white"
             v-for="step in currentStep"
             :class="isActual(step)"
             :key="step.name">
          <span>{{ step.name }}<br><span class="text-xl">{{ step.txt }}</span></span>
          <span class="text-6xl" v-html="remainingTime(step.start, step.end)"></span>
        </div>
      </div>
      <div style="height:45vh" class="flex flex-col justify-start p-5 overflow-hidden relative">
        <div
            class="w-full p-3 mb-2 rounded shadow-md uppercase flex justify-between items-center bg-white cursor-pointer"
            v-on:click="setSecondsTo(step)"
            v-for="step in futurSteps"
            :class="isActual(step)"
            :key="step.name">
          <span>{{ step.name }}</span>
          <span v-html="remainingTime(step.start, step.end)"></span>
        </div>
        <div id="coming" class="absolute w-full h-full"></div>
      </div>
    </div>
    <div class="w-1/3 ml-4 mr-8 flex flex-col justify-center items-center bg-white">
      <h1 class="text-6xl font-black" id="globalTime" v-html="globalTime"></h1>
      <h1 class="text-6xl font-black" id="time" v-html="time"></h1>
      <h1 class="text-6xl font-black" id="reverseTime" v-html="reverseTime"></h1>
      <div class="pb-4">
                <span
                    class="tracking-wider text-white px-4 py-1 text-sm rounded leading-loose mx-2 font-semibold cursor-pointer"
                    :class="state === 'play' ? 'bg-orange-500 hover:bg-orange-600' : 'bg-green-500 hover:bg-green-600'"
                    title="" v-on:click="state === 'pause' || state === 'stop' ? start() : pause()">
                     <i class="fas" :class="state === 'play' ? 'fa-pause' : 'fa-play'"
                        aria-hidden="true"></i> {{ state === 'play' ? 'Pause' : 'Start' }}
                </span>
        <span
            class="tracking-wider text-white bg-red-500 hover:bg-red-600 px-4 py-1 text-sm rounded leading-loose mx-2 font-semibold cursor-pointer"
            title="" v-on:click="resume">
                     <i class="fas fa-stop" aria-hidden="true"></i> Reset
                </span>
      </div>
    </div>
  </div>
</template>

<script>
const M = 'Margaux'
const E = 'Evan'
const El = 'Elyse'
const V = 'Victor'
const MC = 'Mathieu'
const J = 'Jérémy'
const T = 'Tous'
const D = 'DEVS'

export default {
  name: 'HelloWorld',
  data: function () {
    return {
      steps: [
        {id: 1, past: false, current: false, name: `${M} : Pitch / Problématique`, start: 0, end: 180, txt: ''},
        {id: 2, past: false, current: false, name: `${E} : Unified`, start: 180, end: 360, txt: ''},
        {id: 3, past: false, current: false, name: `${T} : Équipe`, start: 360, end: 480, txt: ''},
        {id: 4, past: false, current: false, name: `${El} : Marché`, start: 480, end: 660, txt: ''},
        {id: 5, past: false, current: false, name: `${V} : Concurrence`, start: 660, end: 880, txt: ''},
        {id: 6, past: false, current: false, name: `${MC} : Interview`, start: 880, end: 1000, txt: ''},
        {id: 7, past: false, current: false, name: `${J} : Personas`, start: 1000, end: 1120, txt: ''},
        {id: 8, past: false, current: false, name: `${E} : Logo / Typo`, start: 1120, end: 1180, txt: ''},
        {id: 9, past: false, current: false, name: `${M} : Design System`, start: 1180, end: 1300, txt: ''},
        {id: 10, past: false, current: false, name: `${MC} : Wireframe`, start: 1300, end: 1360, txt: ''},
        {id: 11, past: false, current: false, name: `${M} : Maquette`, start: 1360, end: 1600, txt: ''},
        {id: 12, past: false, current: false, name: `${D} : Dev`, start: 1600, end: 1960, txt: ''},
        {id: 13, past: false, current: false, name: `${V} : Contrainte juridique`, start: 1960, end: 2020, txt: ''},
        {id: 14, past: false, current: false, name: `${El} : Business model`, start: 2020, end: 2200, txt: ''},
        {id: 15, past: false, current: false, name: `${V} : Projection financière`, start: 2200, end: 2320, txt: ''},
        {id: 16, past: false, current: false, name: `${El} + ${V} : Acquisition`, start: 2320, end: 2500, txt: ''},
        {id: 17, past: false, current: false, name: `${J} : Marketing`, start: 2500, end: 2620, txt: ''},
        {id: 18, past: false, current: false, name: `${V} : Communication`, start: 2620, end: 2740, txt: ''},
        {id: 19, past: false, current: false, name: `${El} : Commerciale`, start: 2740, end: 2920, txt: ''},
        {id: 20, past: false, current: false, name: `${T} : Gestion de projet`, start: 2920, end: 3460, txt: ''},
        {id: 21, past: false, current: false, name: `${J} : Conclusion`, start: 3460, end: 3580, txt: ''},
        {id: 22, past: false, current: false, name: `${MC} + ${E} : Tests`, start: 3580, end: 3700, txt: ''},
      ],
      state: 'stop',
      totalSeconds: 0,
      globalSeconds: 0,
      duration: 3700,
    };
  },
  mounted() {
    setInterval(this.increment, 1000);
  },
  computed: {
    time: function () {
      return this.displayMinutes() + ' : ' + this.displaySeconds();
    },
    globalTime: function () {
      return this.displayMinutes(this.globalSeconds) + ' : ' + this.displaySeconds(this.globalSeconds);
    },
    reverseTime: function () {
      return this.reverseMinutes() + ' : ' + this.reverseSeconds();
    },
    pastSteps: function () {
      return this.steps.filter(step => step.past);
    },
    currentStep: function () {
      return this.steps.filter(step => step.current);
    },
    futurSteps: function () {
      return this.steps.filter(step => !step.past && !step.current);
    }
  },
  methods: {
    displayMinutes: function (seconds = this.totalSeconds) {
      const min = Math.floor((seconds / 60) % 60);
      return min >= 10 ? min : '0' + min;
    },
    displaySeconds: function (seconds = this.totalSeconds) {
      const sec = Math.ceil(seconds % 60);
      return sec >= 10 ? sec : '0' + sec;
    },
    reverseMinutes: function () {
      const min = Math.floor((this.duration - this.totalSeconds) / 60);
      return min >= 10 ? min : '0' + min;
    },
    reverseSeconds: function () {
      const sec = Math.floor((this.duration - this.totalSeconds) % 60);
      return sec >= 10 ? sec : '0' + sec;
    },
    start: function () {
      this.state = 'play';
    },
    pause: function () {
      this.state = 'pause';
    },
    setSecondsTo: function (step) {
      this.totalSeconds = step.start;
      this.steps.map(s => {
        s.current = false;
        s.past = s.id < step.id;
      });
      step.current = true;
    },
    resume: function () {
      if (confirm("Est tu certain de vouloir remettre à zéro ?")) {
        this.state = 'stop';
        this.totalSeconds = 0;
        this.globalSeconds = 0;
        this.steps.map(step => {
          step.current = false;
          step.past = false;
        })
      }
    },
    increment: function () {
      if (this.state === 'play') {
        this.totalSeconds += 1;
      }
      if (this.state !== 'stop') {
        this.globalSeconds += 1;
      }
    },
    remainingTime: function (start, end) {
      let response = '';
      if (this.totalSeconds >= start && this.totalSeconds < end) {
        response = this.displayMinutes(end - this.totalSeconds) + ' : ' + this.displaySeconds(end - this.totalSeconds)
      } else {
        response = this.displayMinutes(end - start) + ' : ' + this.displaySeconds(end - start);
      }
      return response;
    },
    isActual: function (step) {
      let response = '';
      if (this.totalSeconds >= step.end) {
        response = 'font-bold text-blue-500 text-xl';
        step.past = true;
        step.current = false;
      }
      if (this.totalSeconds >= step.start && this.totalSeconds < step.end) {
        response = 'font-black text-green-500 text-3xl';
        step.past = false;
        step.current = true;
      }
      if (this.totalSeconds < step.start && (step.start - this.totalSeconds) < 120) {
        response = 'font-extrabold text-purple-500 text-2xl';
      }
      if (this.totalSeconds < step.start && (step.start - this.totalSeconds) >= 120) {
        response = 'font-bold'
      }
      return response;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#coming {
  z-index: 100;
  margin-left: -20px;
  background: linear-gradient(rgba(255, 255, 255, 0), rgba(255, 255, 255, 0) 50%, rgb(236, 236, 236) 90%, rgb(236, 236, 236));
  pointer-events: none;
}

#pasted {
  pointer-events: none;
  z-index: 100;
  margin-left: -20px;
  background: linear-gradient(to top, rgba(255, 255, 255, 0), rgba(255, 255, 255, 0) 50%, rgb(236, 236, 236) 90%, rgb(236, 236, 236));;
}

#time {
  font-size: 140px;
}
</style>
