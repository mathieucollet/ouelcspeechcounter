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
          <span class="w-10/12">{{ step.name }}<br><span class="text-xl">{{ step.text }}</span></span>
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
      <div class="pb-4">
        <div>
          Mathieu : {{ mathieu / 60}} minutes
        </div>
        <div>
          Evan : {{ evan / 60}} minutes
        </div>
        <div>
          Jérémy : {{ jeremy / 60}} minutes
        </div>
        <div>
          Margaux : {{ margaux / 60}} minutes
        </div>
        <div>
          Victor : {{ victor / 60}} minutes
        </div>
        <div>
          Elyse : {{ elyse / 60 }} minutes
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data: function () {
    return {
      steps: [],
      state: 'stop',
      totalSeconds: 0,
      globalSeconds: 0,
      duration: 0,
      starting_duration: 0,
      mathieu: 0,
      evan: 0,
      margaux: 0,
      elyse: 0,
      victor: 0,
      jeremy: 0,
    };
  },
  beforeMount() {
    this.getSegments()
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
    async getSegments() {
      fetch(`${process.env.VUE_APP_BASE_URL}/api/auth/local`, {
            method: 'post',
            headers: {
              "Content-type": "application/json",
            },
            body: JSON.stringify({
              identifier: process.env.VUE_APP_IDENTIFIER,
              password: process.env.VUE_APP_PASSWORD
            })
          }
      )
      .then((response) => response.json())
      .then((data) => {
        fetch(`${process.env.VUE_APP_BASE_URL}/api/segments?sort[0]=position:asc&pagination[pageSize]=60`, {
          headers: {
            Authorization: `Bearer ${data.jwt}`
          },
        })
            .then((response) => response.json())
            .then((data) => {
              this.steps = data.data.map((curr) => {
                curr.attributes.start = this.starting_duration
                this.starting_duration += curr.attributes.duration
                curr.attributes.end = this.starting_duration
                return {...curr.attributes, past: false, current: false}
              })
              this.duration = data.data.reduce((prev, curr) => prev + curr.attributes.duration, 0)
              this.mathieu = data.data.filter((segment) => segment.attributes.name.includes('Mathieu')).reduce((prev, curr) => prev + curr.attributes.duration, 0)
              this.evan = data.data.filter((segment) => segment.attributes.name.includes('Evan')).reduce((prev, curr) => prev + curr.attributes.duration, 0)
              this.elyse = data.data.filter((segment) => segment.attributes.name.includes('Elyse')).reduce((prev, curr) => prev + curr.attributes.duration, 0)
              this.victor = data.data.filter((segment) => segment.attributes.name.includes('Victor')).reduce((prev, curr) => prev + curr.attributes.duration, 0)
              this.jeremy = data.data.filter((segment) => segment.attributes.name.includes('Jérémy')).reduce((prev, curr) => prev + curr.attributes.duration, 0)
              this.margaux = data.data.filter((segment) => segment.attributes.name.includes('Margaux')).reduce((prev, curr) => prev + curr.attributes.duration, 0)
            })
      })
    },
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
      if (this.totalSeconds >= start && this.totalSeconds < end) {
        return this.displayMinutes(end - this.totalSeconds) + ' : ' + this.displaySeconds(end - this.totalSeconds)
      }
      return this.displayMinutes(end - start) + ' : ' + this.displaySeconds(end - start);
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
  },
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
