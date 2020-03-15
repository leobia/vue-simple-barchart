<template>
  <div id="app">
    <BarChart 
      :data="data"
      x-field="key"
      y-field="a"
      bar-padding="0.4"
    />
    <BarChart
      :data="data"
      x-field="key"
      y-field="b"
    />
    <BarChart
      :data="data"
      x-field="key"
      y-field="c"
    />
    <button @click="changeData">
      Change Data
    </button>
  </div>
</template>

<script>
import BarChart from './components/BarChart.vue';

export default {
  name: 'App',
  components: {
    BarChart
  },

  data() {
    return {
      data: [
        {
          key: 'Java',
          a: 5,
          b: 12,
          c: 10
        },
        {
          key: 'Vue',
          a: 3,
          b: 8,
          c: 6
        },
        {
          key: 'React',
          a: 15,
          b: 3,
          c: 7
        },
        {
          key: 'C#',
          a: 5,
          b: 16,
          c: 10
        },
        {
          key: 'Angular',
          a: 6,
          b: 24,
          c: 20
        },
        {
          key: 'Python',
          a: 6,
          b: 4,
          c: 3
        },
      ],
    };
  },

  methods: {
    changeData() {
      this.data.forEach(d => {
        d.a = Math.floor(Math.random() * (30 + 1));
        d.b = Math.floor(Math.random() * (35 + 1));
        d.c = Math.floor(Math.random() * (40 + 1));
      });

      this.data.push({
          key: 'test',
          a: 5,
          b: 12,
          c: 10
      });
    }
  },

};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
