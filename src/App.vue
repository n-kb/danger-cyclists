<template>
  <div id="app">
    <div class="container">
      <div class="columns is-centered content">
        <div class="column is-half">
          <h1 class="title is-1 has-text-centered">Europe's most dangerous city for cyclists</h1>
          <p class="byline has-text-centered">
            A story by <strong>Nicolas Kayser-Bril</strong> &emsp; 3 July 2018.
          </p>
          <p>
            Cycling in the city is dangerous. But some European cities are more dangerous than others.
          </p>
          <p>
            The chart below shows the number of cyclists <strong>killed or seriously injured</strong> (KSI) in traffic in five large European cities.
          </p>
          <d3app viz_type="KSI"></d3app>
          <p>
            Of course, Berlin is a larger city, it has more cyclists than Helsinki. It is only normal that is has more casulaties. We will now look at the number of casualties for every million bicycle rides. <strong>Can you guess</strong> which of these is the most dangerous city for cyclists?
          </p>
          <p class="has-text-centered" id="selectCity">
            Select the city you think is the most dangerous for cyclists.
          </p>
          <p class="has-text-centered">
            <a class="button" @click="choose_city('Berlin')">Berlin</a>
            <a class="button" @click="choose_city('London')">London</a>
            <a class="button" @click="choose_city('Paris')">Paris</a>
            <a class="button" @click="choose_city('Barcelona')">Barcelona</a>
            <a class="button" @click="choose_city('Helsinki')">Helsinki</a>
          </p>
          <div v-if="city_has_been_chosen">
            <d3app viz_type="per_ride" :chosen_city="chosen_city.name" ></d3app>
            <p v-if="correctGuess"> 
              You selected <span class="city Berlin">Berlin</span> and you were right. In 2012, <span class="city Berlin">699</span> cyclists were killed or severely injured in the city. Considering that there were about <span class="city Berlin">360,000</span> daily rides, this adds up to <span class="city Berlin">5.4</span> fatalities for every million rides, a much higher number than in Paris or Barcelona.
            </p>
            <p v-else> 
              You selected <span :class="chosen_city.name" class="city">{{chosen_city.name}}</span>, but the most dangerous city was <span class="city Berlin">Berlin</span>. In {{ chosen_city.name }}, <span :class="chosen_city.name" class="city">{{ chosen_city.severe_injuries + chosen_city.deaths }}</span> cyclists were killed or severely injured in {{ chosen_city.year }}, against <span class="city Berlin">699</span> in Berlin (in 2012). There were about <span :class="chosen_city.name" class="city">{{ chosen_city.daily_rides.toLocaleString('en') }}</span> daily bicyle rides in {{ chosen_city.name }} against <span class="city Berlin">360,000</span> in Berlin. This adds up to <span :class="chosen_city.name" class="city">{{ chosen_city.ksi_per_m }}</span> fatalities per million rides in {{ chosen_city.name }} against <span class="city Berlin">5.4</span> in Berlin.
            </p>
            <p>
              When light injuries are taken into account, <span class="city Barcelona">Barcelona</span> becomes more dangerous, with <span class="city Barcelona">57</span> light injuries per million bicycle rides against <span class="city Berlin">39</span> for Berlin. <span class="city Paris">Paris</span> has <span class="city Paris">27</span> light injuries per million rides and <span class="city Helsinki">Helsinki</span> only <span class="city Helsinki">2</span>. I could not find a reliable figure for light injuries in <span class="city London">London</span>. 
            </p>
            <h2>Data quality</h2>
            <p>
              Data on cycling is rarely collected by authorities. Only one survey, the Urban Audit of Eurostat, offers comparable data on bicycle usage in Europe at the city level. Most member states do not bother to keep it up-to-date, which is why the data for all cities but Helsinki is over five-year-old. However, there is no reason to think that the order of magnitude of the metrics presented above changed recently.
            </p>
            <p>
              Data on injuries also varies by city. The data from Paris, for instance, comes from police reports, while the data from Helsinki comes from hospitals. In case of deaths, hospitals consider that a patient deceased within 30 days of the accident was killed in traffic, whereas police reports only deal with the immediate consequences of a crash. 
            </p>
            <p>
              This does not make Berlin look any better, as Berlin's is police data. For the Berlin police, a serious injury means that the victim spent at least 24 hours in the hospital after the crash. In Paris, a serious injury is any injury that requires hospital treatment. If anything, the numbers above probably underestimate the dangerousness of cycling in Berlin.
            </p>
            <h2>The data</h2>
            <p>I used the share of the population cycling to work as a proxy to obtain the number of daily rides. While not everyone goes to work, children, unemployed and retired people also need to move through the city ; I assumed they did so using means of transportation similar to the workers'.</p>
            <table class="table">
              <tr><td>City</td><td>Year</td><td>Share of the population cycling to work</td><td>Population for given year</td><td>Light injuries</td><td>Severe injuries</td><td>Deaths</td></tr>
              <tr><td>Berlin</td><td>2012</td><td>10.8</td><td>3326002</td><td>5025</td><td>684</td><td>15</td></tr>
              <tr><td>London</td><td>2011</td><td>4.3</td><td>8173941</td><td></td><td>555</td><td>16</td></tr>
              <tr><td>Paris</td><td>2011</td><td>3.12</td><td>2249977</td><td>695</td><td>49</td><td>0</td></tr>
              <tr><td>Helsinki</td><td>2016</td><td>17</td><td>628208</td><td>97</td><td>3</td><td>0</td></tr>
              <tr><td>Barcelona</td><td>2011</td><td>1.72</td><td>1611013</td><td>570</td><td>10</td><td>2</td></tr>
            </table>
          </div>
        </div>
      </div>
    </div>

     <footer class="footer">
        
      </footer>

  </div>
</template>

<script>
import d3app from './d3-app.vue'
import axios from 'axios'

export default {
  name: 'app',
  data() {
    return {
      chosen_city: {},
      city_has_been_chosen: false,
      city_data: [],
      correctGuess: false
    }
  },
  methods: {
    choose_city(city) {
      this.city_has_been_chosen = true;
      this.correctGuess = (city == 'Berlin') ? true : false;
      var self = this;
      this.city_data.forEach(function(d){
        if (d.city == city) {
          self.chosen_city = {
            name: city,
            daily_rides: Math.floor(d.daily_rides/10000) * 10000,
            severe_injuries: d.severe_injuries,
            deaths: d.deaths,
            light_injuries: d.light_injuries,
            pop: Math.floor(d.pop/100000) * 100000,
            share_cycle: d.share_cycle,
            ksi_per_m: Math.floor((+d.severe_injuries + d.deaths) / (+d.daily_rides * 360) * 10000000) / 10,
            year: d.year
          }
        }
      })
    }
  },
  created () {
    axios.get("https://raw.githubusercontent.com/n-kb/danger-cyclists/master/src/assets/data.json").then(response => {
          this.city_data = response.data;
        });
  },
  components: { d3app }
}
</script>

<style lang="sass">

$family-primary: "Arial";
$title-size: 2.5em
$weight-semibold: 200
$body-family: "Georgia"

@import "~bulma/bulma"

h1
  font-family: $family-primary

p
  font-size: 1.2rem
  line-height: 1.9rem

.byline
  font-variant: small-caps
  font-size: .8rem

.container
  margin-top: 3em
  margin-left: auto

.city
  border-bottom: 4px solid

.Berlin
  border-color: rgba(#354458,.7)

.Helsinki
  border-color: rgba(#3A9AD9,.7)

.Barcelona
  border-color: rgba(#29ABA4,.7)

.Paris
  border-color: rgba(#E9E0D6,.7)

.London
  border-color: rgba(#EB7260,.7)

.footer
  background-color: #fff
</style>
