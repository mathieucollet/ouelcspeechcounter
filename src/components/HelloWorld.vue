<template>
    <div class="flex justify-center items-center w-full h-full">
        <div class="w-1/2">
            <div style="height:45vh;" class="flex flex-col justify-end p-5">
                <div class="w-full p-3 mb-2 rounded shadow-md uppercase flex justify-between items-center bg-white"
                     v-for="step in pastSteps"
                     :class="isActual(step)"
                     :key="step.name">
                <span>
                    {{step.name}}
                </span>
                    <span v-html="remainingTime(step.start, step.end)"></span>
                </div>
            </div>
            <div style="height:10vh" class="px-5 flex flex-col justify-center">
                <div class="w-full h-full px-3 mb-2 rounded shadow-md uppercase flex justify-between items-center bg-white"
                     v-for="step in currentStep"
                     :class="isActual(step)"
                     :key="step.name">
                <span>
                    {{step.name}}
                </span>
                    <span v-html="remainingTime(step.start, step.end)"></span>
                </div>
            </div>
            <div style="height:45vh" class="flex flex-col justify-start p-5">
                <div class="w-full p-3 mb-2 rounded shadow-md uppercase flex justify-between items-center bg-white"
                     v-for="step in futurSteps"
                     :class="isActual(step)"
                     :key="step.name">
                <span>
                    {{step.name}}
                </span>
                    <span v-html="remainingTime(step.start, step.end)"></span>
                </div>
            </div>
        </div>
        <div class="w-1/2 flex flex-col justify-center items-center bg-white">
            <h1 class="text-6xl font-black" id="time" v-html="time"></h1>
            <div>
                <span class="tracking-wider text-white bg-green-500 hover:bg-green-600 px-4 py-1 text-sm rounded leading-loose mx-2 font-semibold cursor-pointer"
                      title="" v-on:click="start">
                     <i class="fas fa-play" aria-hidden="true"></i> Play
                </span>
                <span class="tracking-wider text-white bg-orange-500 hover:bg-orange-600 px-4 py-1 text-sm rounded leading-loose mx-2 font-semibold cursor-pointer"
                      title="" v-on:click="pause">
                     <i class="fas fa-pause" aria-hidden="true"></i> Pause
                </span>
                <span class="tracking-wider text-white bg-red-500 hover:bg-red-600 px-4 py-1 text-sm rounded leading-loose mx-2 font-semibold cursor-pointer"
                      title="" v-on:click="resume">
                     <i class="fas fa-stop" aria-hidden="true"></i> Reset
                </span>
            </div>
        </div>
    </div>
</template>

<script>
  export default {
    name: 'HelloWorld',
    data: function () {
      return {
        steps: [
          {past: true, current: false, name: 'Math : step 1', start: 0, end: 20},
          {past: false, current: true, name: 'Margaux : step 2', start: 20, end: 40},
          {past: false, current: false, name: 'Cora : step 3', start: 40, end: 60},
          {past: false, current: false, name: 'step 4', start: 60, end: 80},
          {past: false, current: false, name: 'step 5', start: 80, end: 100},
          {past: false, current: false, name: 'step 6', start: 100, end: 120},
        ],
        state: 'pause',
        totalSeconds: 25,
      };
    },
    mounted() {
      setInterval(this.increment, 1000);
    },
    computed: {
      time: function () {
        return this.displayMinutes() + ' : ' + this.displaySeconds();
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
      start: function () {
        this.state = 'play';
      },
      pause: function () {
        this.state = 'pause';
      },
      resume: function () {
        this.state = 'pause';
        this.totalSeconds = 0;
        this.steps.map(step => {
          step.current = false;
          step.past = false;
        })
      },
      increment: function () {
        if (this.state === 'play') {
          this.totalSeconds += 1;
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
        if (this.totalSeconds < step.start && (step.start - this.totalSeconds) < 10) {
          response = 'font-extrabold text-orange-500 text-2xl';
        }
        return response;
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
