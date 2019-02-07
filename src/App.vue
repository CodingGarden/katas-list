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
      <div class="kyu">{{!kata.kyu ? '???' : Math.abs(kata.kyu)}} kyu</div>
      <div class="episode">Episode - {{kata.episodeNum}}</div>
      <a :href="kata.video" target="_blank" rel="noopener">Watch on YouTube</a>
      <a :href="kata.githubUrl" target="_blank" rel="noopener">View Solution on Github</a>
      <a v-if="kata.kyu" :href="`https://www.codewars.com/kata/${kata.slug}`" target="_blank" rel="noopener">Solve on Code Wars</a>
    </div>
  </div>
</template>

<script>
import katas from './katas.json';

const kyus = [...new Set(katas.map(kata => kata.kyu).sort())];

const selectedKyus = kyus.reduce((all, kyu) => (all[kyu] = true, all), {});

export default {
  name: 'app',
  data: () => ({
    search: '',
    katas,
    kyus,
    selectedKyus,
  }),
  methods: {
    toggleKyu(kyu) {
      this.selectedKyus[kyu] = !this.selectedKyus[kyu];
    },
  },
  computed: {
    filteredKatas() {
      if (!this.search.trim()) return this.katas.filter(kata => this.selectedKyus[kata.kyu]);
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
