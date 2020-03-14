<template>
  <div>
  <div id="graph">
  </div>
  </div>
</template>

<script>
const d3 = window.d3;
export default {
  name: 'BarChart',
  props: {
    data: {
      type: Array,
      required: true
    },

    margin: {
      type: Object,
      default: () => { 
        return {top: 10, right: 30, bottom: 90, left: 40}
      }
    },

    height: {
      type: Number,
      default: 450
    },

    width: {
      type: Number,
      default: 460
    },

    xField: {
      type: String,
      required: true
    },

    yField: {
      type: String,
      required: true
    }
  },


  data() {
    return {
      svg: null
    }
  },
  computed: {
    heightComp() {
      return this.height - this.margin.top - this.margin.bottom;
    },
    widthComp() {
      return this.height - this.margin.left - this.margin.right;
    },

    rangeY() {
      let output = Math.max.apply(Math, this.data.map((d) => { return d[this.yField]; })) + 2;
      console.log(output)
      return output
    }
  },
  mounted() {
    this.initGraph();
  },

  methods: {
    initGraph() {
      this.svg = d3.select("#graph")
      .append("svg")
      .attr("width", this.widthComp + this.margin.left + this.margin.right)
      .attr("height", this.heightComp + this.margin.top + this.margin.bottom)
      .append("g")
      .attr("transform", "translate(" + this.margin.left + "," + this.margin.top + ")");
      this.createAxis()
    },

    createAxis() {
      let x = d3.scaleBand()
      .range([0, this.widthComp])
      .domain(this.data.map((d) => {return d[this.xField]}))
      .padding(0.2);
      
      this.svg.append("g")
      .attr("transform", "translate(0," + this.heightComp + ")")
      .call(d3.axisBottom(x))
      .selectAll("text")
      .attr("transform", "translate(-10)rotate(-45)")
      .style("text-anchor", "end");

      let y = d3.scaleLinear()
      .domain([0, this.rangeY])
      .range([this.heightComp, 0]);

      this.svg.append("g")
      .call(d3.axisLeft(y))

      this.createBars(x, y);
    },

    createBars( x, y) {
      this.svg.selectAll("myBar")
      .data(this.data)
      .enter()
      .append("rect")
      .attr("x", (d) => {return x(d[this.xField])})
      .attr("width", x.bandwidth())
      .attr("fill", "#69b3a2")
      // eslint-disable-next-line no-unused-vars
      .attr("height", (d) => {return this.heightComp - y(0)})
      // eslint-disable-next-line no-unused-vars
      .attr("y", (d) => {return y(0)})
      
      this.animate(x, y);
    },

    animate(x, y) {
      this.svg.selectAll("rect")
      .transition()
      .duration(800)
      .attr("y", (d) => {return y(d[this.yField])})
      .attr("height", (d) => {return this.heightComp - y(d[this.yField])})
      .delay((d, i) => {return(i*100)})
    },

    updateGraph() {
      // TODO cancellare i ticks
      this.createAxis();
    }
  },

  watch: {
    data: {
      immediate: true,
      deep: true,
      handler(newValue, oldValue) {
        console.log(newValue)
        console.log(oldValue)
        if (oldValue) {
          this.updateGraph();
        }
      }
    }

  }
  

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
