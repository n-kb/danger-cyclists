<template>
  <div>
    <div :id="viz_type" class="svg-container">
    </div>
  </div>
</template>

<script>

  import * as d3 from 'd3'

  export default {
    name: 'd3app',
    props: { 
      viz_type: "",
      chosen_city: ""
    },
    mounted() {

      var margin = {top: 20, right: 20, bottom: 70, left: 40},
      width = 600 - margin.left - margin.right,
      height = 300 - margin.top - margin.bottom;

      var x = d3.scaleBand();

      var y = d3.scaleLinear();

      var svg = d3.select("#" + this.viz_type).append("svg")
      .attr("preserveAspectRatio", "xMinYMin meet")
      .attr("viewBox", "0 0 600 300")
      .attr("class","svg-content")
      .append("g")
      .attr("transform", 
        "translate(" + margin.left + "," + margin.top + ")");

      var self = this;
      d3.json("https://raw.githubusercontent.com/n-kb/danger-cyclists/master/src/assets/data.json").then(function(data) {

        var legend = ""

        data.forEach(function(d) {
          d.city = d.city;
          if (self.viz_type == "KSI") {
            d.value = +d.severe_injuries + d.deaths;
          } else if (self.viz_type == "per_ride") {
            d.value = Math.floor((+d.severe_injuries + d.deaths) / (+d.daily_rides * 360) * 10000000) / 10;
          }
        });

        data.sort(function(a, b){
           return d3.descending(a.value, b.value);
        });


        y.domain(d3.extent(data, function(d) { return d.value; })).nice()
            .rangeRound([height, 0]);
        x.domain(data.map(function(d) { return d.city; }))
          .rangeRound([0, width]).padding(0.1);

        svg.append("g")
            .attr("class", "axis x_axis")
            .attr("transform",`translate(0,${height})`)
            .call(d3.axisBottom(x));

        var bar_width = 20;
        svg.selectAll(".bar1")
          .data(data)
          .enter().append("rect")
            .attr("class", d => d.city == self.chosen_city ? "bar1 chosen " + d.city : "bar1 " + d.city)
            .attr("x", d => x(d.city))
            .attr("y", y(0))
            .attr("width", x.bandwidth())
            .attr("height", 0);

        svg.selectAll(".bar1")
          .transition()
          .duration(1000)
          .delay(function(d,i){return i*100})
          .attr("height", d => y(0) - y(d.value))
          .attr("y", d => y(d.value));

        svg.selectAll(".labels")
          .data(data)
          .enter()
          .append("text")
          .attr("text-anchor", "middle")
          .attr("class", "labels")
          .attr("x", d => x(d.city) + x.bandwidth() / 2)
          .attr("y", d => y(d.value) - 10);

        if (self.viz_type == "KSI") {
          svg.selectAll(".labels")
            .text(d => d.value + " KSI (" + d.year + ")");
        } else {
          svg.selectAll(".labels")
            .text(d => d.value + " KSI/m");
        }
      });
      if (self.viz_type == "per_ride"){
        //hack to prevent scroll from happening on first one
        document.getElementById("selectCity").scrollIntoView({behavior: "smooth"});
      }
    }
  }


</script>

<style lang="sass">

.svg-container
  width: 100%
  position: relative
  overflow: hidden
  padding-bottom: 300px;
  vertical-align: top;
  overflow: hidden;

.svg-content
    display: inline-block
    position: absolute
    top: 0
    left: 0

.labels
  font-family: Arial
  font-size: 0.6em

.Berlin
  fill: #354458

.Helsinki
  fill: #3A9AD9

.Barcelona
  fill: #29ABA4

.Paris
  fill: #E9E0D6

.London
  fill: #EB7260

.Stuttgart
  fill: #3C3865

.Cologne
  fill: #386365

.Hamburg
  fill: #654A47

.chosen
  stroke: #333
  stroke-width: 1
</style>
