<template>
  <div id="app">
    <h1>Code Wars Code Katas with CJ</h1>
    <input v-model="search">
    <br>
    <div
      v-for="kyu in kyus"
      :key="kyu"
      class="kyu"
      :class="{
        selected: selectedKyus[kyu]
      }"
      @click="toggleKyu(kyu)">{{!kyu ? '???' : Math.abs(kyu)}}</div>
    <div v-for="kata in filteredKatas" :key="kata.githubUrl" class="kata">
      <h3>{{!kata.kyu ? '???' : Math.abs(kata.kyu)}} kyu - {{kata.kataName}}</h3>
      <small>Episode - {{kata.episodeNum}}</small>
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
#app {
  font-family: sans-serif;
}

input {
  width: 80%;
  padding: 0.5em;
  margin: 0.5em;
}

@import url("https://fonts.googleapis.com/css?family=Open+Sans:300,400|Ubuntu:700");

body {
  font-family: "Open Sans", sans-serif;
}

a {
  color: hsl(0, 70%, 40%);
}

.kata {
  padding: 1em;
}

.kata:not(:last-child) {
  border-bottom: 2px solid hsl(0, 70%, 40%);
}

.kata a {
  display: block;
}

h3 {
  font-family: "Ubuntu", sans-serif;
}

small {
  display: block;
  margin-bottom: 0.5em;
}

.kyu {
  display: inline-block;
  padding: 0.5em;
  margin: 0.25em;
  border: 1px solid hsl(0, 70%, 40%);
  cursor: pointer;
}

.selected {
  background: hsl(0, 70%, 40%);
  color: white;
}
</style>
