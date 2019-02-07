<template>
  <div id="app">
    <h1>Code Wars Code Katas with CJ</h1>
    <div class="input">
      <input v-model="search" placeholder="filter">
      <div class="kyu-list">
        <div>Kyu:</div>
        <div
          v-for="kyu in kyus"
          :key="kyu"
          class="kyu"
          :class="{
            selected: selectedKyus[kyu]
          }"
          @click="toggleKyu(kyu)">{{!kyu ? '???' : Math.abs(kyu)}}</div>
      </div>
    </div>
    <div v-for="kata in filteredKatas" :key="kata.githubUrl" class="kata">
      <h3>{{kata.kataName}}</h3>
      <div
        class="kyu"
        @click="singleOutKyu(kata.kyu)"
        >{{!kata.kyu ? '???' : Math.abs(kata.kyu)}} kyu</div>
      <div class="episode">Episode - {{kata.episodeNum}}</div>
      <a :href="kata.video" target="_blank" rel="noopener">Watch on YouTube</a>
      <a :href="kata.githubUrl" target="_blank" rel="noopener">View Solution on Github</a>
      <a v-if="kata.kyu" :href="`https://www.codewars.com/kata/${kata.slug}`" target="_blank" rel="noopener">Solve on Code Wars</a>
    </div>
  </div>
</template>

<script>
import katas from './katas.json';

// Array of sorted, unique kyu values
const kyus = [...new Set(katas.map(kata => kata.kyu).sort())];

// An object representing the kyu toggles
const selectedKyus = kyus.reduce((all, kyu) => (all[kyu] = true, all), {});

// Has a single kyu been singled out?
let singledOutKyu = false;
// The key of the currently singled out kyu
let singledOutKyuKey;
// A memory of the previously selected key
const selectedKyusMemory = Object.assign({}, selectedKyus);

export default {
  name: 'app',
  // Data available in/from the template
  data: () => ({
    search: '',
    katas,
    kyus,
    selectedKyus,
  }),
  // Functions available in the template
  methods: {
    // Toggle a kyu on click or set the state for a kyu
    toggleKyu(_kyu, state = !this.selectedKyus[_kyu], singledOut = false) {
      // Stringify the kyu value (numbers and null)
      const kyu = '' + _kyu;
      // Is not part of a singling-out (i.e. clicking on the kyu-list)
      if(!singledOut) {
        // Is this kyu the last one to be singled out?
        if(singledOutKyu && singledOutKyuKey === kyu) {
          // No kyu is singled out anymore
          singledOutKyu = false;
          // Return the kyu list to the previous memory
          return this.returnToMemory();
        }
        else {
          // No kyu is singled out anymore
          singledOutKyu = false;
        }
      }
      // The current state of the kyu prior to assignment
      const currentState = this.selectedKyus[kyu];
      this.selectedKyus[kyu] = state;
      // Has the current state been changed?
      return currentState !== state;
    },
    // Single out a specific kyu
    singleOutKyu(_kyu) {
      // Stringify the kyu value (numbers and null)
      const kyu = '' + _kyu;
      // Remember the current state before possibly altering it
      const tempMemory = Object.assign({}, this.selectedKyus);
      // Will be true if at least one change is made
      let stateChanged = false;
      // Loop over all kyu states
      for(const key in this.selectedKyus) {
        // This key is not the kyu that is to be singled-out
        if(key !== kyu) {
          // Disable this kyu
          const didChange = this.toggleKyu(key, false, true);
          // Has at least one change been made?
          stateChanged = stateChanged || didChange;
        }
      }
      // If a state change has been made
      if(stateChanged) {
        // Successfully singled out one kyu
        singledOutKyu = true;
        // Remember the currently singled-out kyu
        singledOutKyuKey = kyu;
        // Commit the temporary memory
        Object.assign(selectedKyusMemory, tempMemory);
      }
      // No change was made
      else {
        // Reassign the old memory
        this.returnToMemory();
      }
    },
    // Change all kyu to their state in the memory
    returnToMemory() {
      // Will be true if at least one change is made
      let stateChanged = false;
      // Loop over all kyu states
      for(const key in selectedKyusMemory) {
        // Reassign this kyu
        let didChange = this.toggleKyu(key, selectedKyusMemory[key]);
          // Has at least one change been made?
        stateChanged = stateChanged || didChange;
      }
      // Have any of the states been changed?
      return stateChanged;
    }
  },
  computed: {
    filteredKatas() {
      if (!this.search.trim()) {
        return this.katas.filter(kata => this.selectedKyus[kata.kyu]);
      }
      const lowerSearch = this.search.toLowerCase();
      return this.katas.filter(kata => this.selectedKyus[kata.kyu] && kata.kataName.toLowerCase().includes(lowerSearch));
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Open+Sans:400|Ubuntu:700");

body,
.input,
input {
  background: white;
}

body,
input {
  font-family: "Open Sans", sans-serif;
}

body {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
}

a {
  color: hsl(0, 70%, 40%);
}

#app {
  padding: 0.5em;
}

h1, h2, h3, h4, h5, h6,
.kyu {
  font-family: "Ubuntu", sans-serif;
  font-weight: 700;
}

h1 {
  cursor: default;
}

.input,
input,
.kyu {
  border: 1px solid hsl(0, 70%, 40%);
  border-radius: 2px;
}

.input {
  display: flex;
  flex-direction: column;
  padding: 0.5em;
  border-width: 0;
  border-bottom-width: 3px;
  border-top-width: 1px;
  border-radius: 0;
  position: -webkit-sticky;
  position: sticky;
  top: -1px;
  z-index: 10;
}

input {
  font-size: 1em;
  padding: 0.5em;
  margin: 0.5em;
}

.kyu-list {
  padding: 0.2em;
  padding-left: 1.5em;
  display: flex;
  align-items: center;
  flex-wrap: wrap;
}

.kyu-list > div:first-child {
  margin-right: 1em;
}

.kyu {
  padding: 0.35em 0.7em;
  margin: 0.25em;
  cursor: pointer;
  -webkit-user-select: none;
     -moz-user-select: none;
      -ms-user-select: none;
          user-select: none;
  transition: 100ms background;
}

.selected {
  background: hsl(0, 70%, 40%);
  color: white;
}

.kata {
  padding: 1em;
  padding-right: 7em;
  padding-bottom: 1.5em;
  position: relative;
}

.kata:not(:last-child) {
  border-bottom: 2px solid hsl(0, 70%, 40%);
}

.kata h3 {
  margin-top: 0.25em;
}

.kata a {
  display: block;
}

.kata .kyu {
  position: absolute;
  top: 1em;
  right: 1em;
}

.episode {
  font-size: 14px;
  margin-bottom: 1em;
}

@media (max-width: 600px) {
  #app {
    padding: 0;
  }

  h1 {
    font-size: 5.5vw;
    text-align: center;
  }

  .input {
    padding: 0.25em;
  }

  .kyu-list {
    padding-left: 1em;
  }

  .kyu-list > div:first-child {
    margin-right: 0.5em;
  }

  .kyu {
    margin: 0.1em;
  }
}
</style>
