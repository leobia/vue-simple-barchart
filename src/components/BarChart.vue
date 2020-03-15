<template>
  <div>
    <div id="graph" />
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
        return {top: 10, right: 30, bottom: 90, left: 40};
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
    },
    barPadding: {
      type: [Number, String],
      default: 0.2
    },
    showGrid: {
      type: Boolean,
      default: true
    },
    barColor: {
      type: String,
      default: '#69b3a2'
    },
    borderBarColor: {
      type: String,
      default: '#69b3a2'
    },
    borderBarWidth: {
      type: String,
      default: '1.5'
    } 
  },

  data() {
    return {
      svg: null
    };
  },

  computed: {
    heightComp() {
      return this.height - this.margin.top - this.margin.bottom;
    },
    widthComp() {
      return this.height - this.margin.left - this.margin.right;
    },
    rangeY() {
      return Math.max.apply(Math, this.data.map((d) => { return d[this.yField]; })) + 2;
    },
    padding() {
      return new Number(this.barPadding);
    }
  },

  watch: {
    data: {
      deep: true,
      handler(newValue, oldValue) {
        if (oldValue) {
          this.updateGraph();
        }
      }
    }
  },
  
  mounted() {
    this.initGraph();
  },

  methods: {
    initGraph() {
      this.svg = d3.select('#graph')
      .append('svg')
      .attr('width', this.widthComp + this.margin.left + this.margin.right)
      .attr('height', this.heightComp + this.margin.top + this.margin.bottom)
      .append('g')
      .attr('transform', 'translate(' + this.margin.left + ',' + this.margin.top + ')');
      this.createAxis();
    },

    createAxis() {
      let x = this.getXAxis();
      this.svg.append('g')
      .attr('class', 'x axis')
      .attr('transform', 'translate(0,' + this.heightComp + ')')
      .call(d3.axisBottom(x))
      .selectAll('text')
      .attr('transform', 'translate(-10)rotate(-45)')
      .style('text-anchor', 'end');

      let y = this.getYAxis();
      this.svg.append('g')
      .attr('class', 'y axis')
      .call(d3.axisLeft(y));

      if (this.showGrid) {
        this.drawGridLines(x, y);
      }
      this.createBars(x, y);
    },

    getXAxis() {
      let x = d3.scaleBand()
      .range([0, this.widthComp])
      .domain(this.data.map((d) => d[this.xField]))
      .padding(this.padding);
      return x;
    },

    getYAxis() {
      let y = d3.scaleLinear()
      .domain([0, this.rangeY])
      .range([this.heightComp, 0]);
      return y;
    },

    drawGridLines(x, y) {
      this.svg.append('g')			
          .attr('class', 'grid')
          .attr('transform', 'translate(0,' + this.heightComp + ')')
          .call(this.xGridLines(x)
              .tickSize(-this.heightComp)
              .tickFormat(''))
          .selectAll('line')
          .attr('stroke', 'lightgrey')
          .attr('stroke-opacity', '0.7')
          .attr('shape-rendering', 'crispEdges');
          
      this.svg.append('g')			
          .attr('class', 'grid')
          .call(this.yGridLines(y)
              .tickSize(-this.widthComp)
              .tickFormat(''))
          .selectAll('line')
          .attr('stroke', 'lightgrey')
          .attr('stroke-opacity', '0.7')
          .attr('shape-rendering', 'crispEdges');

        // delete extarnal borders
        this.svg.selectAll('.grid')
          .selectAll('path')
          .attr('stroke-width', '0');
    },

    xGridLines(x) {
      return d3.axisBottom(x).ticks();
    },

    yGridLines(y) {
      return d3.axisLeft(y).ticks();
    },

    createBars(x, y) {
      //Create bars
      var self = this;
      this.svg.selectAll('bars')
      .data(this.data)
      .enter()
      .append('rect')
      .attr('x', (d) => {return x(d[this.xField]);})
      .attr('width', x.bandwidth())
      .attr('fill', this.barColor + '90')
      .attr('stroke', this.borderBarColor)
      .attr('stroke-width', this.borderBarWidth)
      .attr('height', () => this.heightComp-y(0))
      .attr('y', () => y(0))
      .on('mouseover', function() {
          d3.select(this).style('fill', self.borderBarColor);
      })
      .on('mouseout', function() {
          d3.select(this).style('fill', self.borderBarColor + '90');
      });    
      
      this.animate(x, y);
    },

    animate(x, y) {
      this.svg.selectAll('rect')
      .transition()
      .duration(800)
      .attr('y', (d) => {return y(d[this.yField]);})
      .attr('height', (d) => {return this.heightComp - y(d[this.yField]);})
      .delay((d, i) => {return(i*100);});
    },

    updateGraph() {
      let x = this.getXAxis();
      let y = this.getYAxis();

      this.svg.select('.y.axis')
        .transition()
        .duration(750)
        .call(d3.axisLeft(y));
      this.svg.select('.x.axis')
        .transition()
        .duration(750)
        .call(d3.axisBottom(x));
        
      // TODO update grid
      this.animate(x, y);
    }
  },
};
</script>

<style scoped>
</style>
