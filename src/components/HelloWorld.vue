<template>
    <div class="flex justify-center items-center w-full h-full">
        <div class="w-2/3">
            <div style="height:45vh;" class="flex flex-col justify-end p-5 relative">
                <div class="w-full p-3 mb-2 rounded shadow-md uppercase flex justify-between items-center bg-white cursor-pointer"
                     v-on:click="setSecondsTo(step)"
                     v-for="step in pastSteps"
                     :class="isActual(step)"
                     :key="step.name">
                    <span>{{step.name}}</span>
                    <span v-html="remainingTime(step.start, step.end)"></span>
                </div>
                <div id="pasted" class="absolute w-full h-full"></div>
            </div>
            <div style="height:10vh" class="px-5 flex flex-col justify-center">
                <div class="w-full h-full px-3 mb-2 rounded shadow-md uppercase flex justify-between items-center bg-white"
                     v-for="step in currentStep"
                     :class="isActual(step)"
                     :key="step.name">
                    <span>{{step.name}}<br><span class="text-xl">{{step.txt}}</span></span>
                    <span class="text-6xl" v-html="remainingTime(step.start, step.end)"></span>
                </div>
            </div>
            <div style="height:45vh" class="flex flex-col justify-start p-5 overflow-hidden relative">
                <div class="w-full p-3 mb-2 rounded shadow-md uppercase flex justify-between items-center bg-white cursor-pointer"
                     v-on:click="setSecondsTo(step)"
                     v-for="step in futurSteps"
                     :class="isActual(step)"
                     :key="step.name">
                    <span>{{step.name}}</span>
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
                <span class="tracking-wider text-white px-4 py-1 text-sm rounded leading-loose mx-2 font-semibold cursor-pointer"
                      :class="state === 'play' ? 'bg-orange-500 hover:bg-orange-600' : 'bg-green-500 hover:bg-green-600'"
                      title="" v-on:click="state === 'pause' || state === 'stop' ? start() : pause()">
                     <i class="fas" :class="state === 'play' ? 'fa-pause' : 'fa-play'" aria-hidden="true"></i> {{state === 'play' ? 'Pause' : 'Start'}}
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
          {id: 2, past: false, current: false, name: 'Baptiste : Anglais', start: 0, end: 240, txt: ''},
          {id: 3, past: false, current: false, name: 'Mathieu : Présentation Jérémy', start: 240, end: 270, txt: ''},
          {id: 4, past: false, current: false, name: 'Jérémy : Présentation Margaux', start: 270, end: 300, txt: ''},
          {id: 5, past: false, current: false, name: 'Margaux : Présentation Baptiste', start: 300, end: 330, txt: ''},
          {
            id: 6,
            past: false,
            current: false,
            name: 'Baptiste : Présentation Coraléane',
            start: 330,
            end: 360,
            txt: ''
          },
          {id: 7, past: false, current: false, name: 'Coraléane : Présentation Mathieu', start: 360, end: 390, txt: ''},
          {id: 8, past: false, current: false, name: 'Margaux : Génèse', start: 390, end: 450, txt: ''},
          {
            id: 9,
            past: false,
            current: false,
            name: 'Mathieu : Cahier des charges',
            start: 450,
            end: 540,
            txt: 'lien social - homeur/comeur - hozeil - macros - mineurs - bassin - pub'
          },
          {id: 10, past: false, current: false, name: 'Margaux : Gestion de projet', start: 540, end: 600, txt: ''},
          {id: 11, past: false, current: false, name: 'Mathieu : Choix techniques', start: 600, end: 660, txt: ''},
          {id: 12, past: false, current: false, name: 'Margaux : Le design', start: 660, end: 840, txt: ''},
          {id: 13, past: false, current: false, name: 'Margaux : Design System', start: 840, end: 900, txt: ''},
          {id: 14, past: false, current: false, name: 'Jérémy : Front', start: 900, end: 1140, txt: ''},
          {
            id: 15,
            past: false,
            current: false,
            name: 'Mathieu : Back',
            start: 1140,
            end: 1380,
            txt: 'Laravel - TDD - IC - Docs'
          },
          {id: 16, past: false, current: false, name: 'Jérémy : Tests', start: 1380, end: 1440, txt: ''},
          {
            id: 17,
            past: false,
            current: false,
            name: 'Baptiste : Secteurs et tendances',
            start: 1440,
            end: 1560,
            txt: ''
          },
          {id: 18, past: false, current: false, name: 'Baptiste : Concurrents', start: 1560, end: 1620, txt: ''},
          {id: 19, past: false, current: false, name: 'Baptiste : Business Model', start: 1620, end: 1740, txt: ''},
          {id: 20, past: false, current: false, name: 'Coraléane : SWOT', start: 1740, end: 1860, txt: ''},
          {
            id: 21,
            past: false,
            current: false,
            name: 'Coraléane : Sur les réseaux sociaux',
            start: 1860,
            end: 2100,
            txt: ''
          },
          {id: 22, past: false, current: false, name: 'Margaux : Référencement', start: 2100, end: 2220, txt: ''},
          {id: 23, past: false, current: false, name: 'Jérémy : Ambassadeurs', start: 2220, end: 2280, txt: ''},
          {
            id: 24,
            past: false,
            current: false,
            name: 'Baptiste : Soirée de lancement',
            start: 2280,
            end: 2400,
            txt: ''
          },
          {id: 25, past: false, current: false, name: 'Margaux : Partenaires', start: 2400, end: 2460, txt: ''},
          {id: 26, past: false, current: false, name: 'Margaux : V2 => Ohplanet', start: 2460, end: 2520, txt: ''},
          {
            id: 27,
            past: false,
            current: false,
            name: 'Jérémy : V2 => Application mobile',
            start: 2520,
            end: 2580,
            txt: ''
          },
          {id: 28, past: false, current: false, name: 'Mathieu : V2 => Chat', start: 2580, end: 2610, txt: ''},
          {
            id: 29,
            past: false,
            current: false,
            name: 'Baptiste : V2 => Régional + coivoit',
            start: 2610,
            end: 2670,
            txt: ''
          },
          {id: 30, past: false, current: false, name: 'Coraléane : V2 => Blog', start: 2670, end: 2700, txt: ''},
          {id: 31, past: false, current: false, name: 'Margaux : Remerciements', start: 2700, end: 2720, txt: ''},
        ],
        state: 'stop',
        totalSeconds: 0,
        globalSeconds: 0,
        duration: 2720,
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
