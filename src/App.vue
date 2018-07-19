<template>
  <div id="app">
    <div class="container">
      <div class="columns is-centered content">
        <div class="column is-half">
          <h1 class="title is-1 has-text-centered">Europe's most dangerous city for cyclists</h1>
          <p class="byline has-text-centered">
            A story by <strong><a href="http://nkb.fr" class="byline--link">Nicolas Kayser-Bril</a></strong> &emsp; 3 July 2018 (updated 19 July)
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
            <a class="button Berlin btn-choose" @click="choose_city('Berlin')">Berlin</a>
            <a class="button London btn-choose" @click="choose_city('London')">London</a>
            <a class="button Cologne btn-choose" @click="choose_city('Cologne')">Cologne</a>
            <a class="button Hamburg btn-choose" @click="choose_city('Hamburg')">Hamburg</a>
            <a class="button Stuttgart btn-choose" @click="choose_city('Stuttgart')">Stuttgart</a>
            <a class="button Paris btn-choose" @click="choose_city('Paris')">Paris</a>
            <a class="button Barcelona btn-choose" @click="choose_city('Barcelona')">Barcelona</a>
            <a class="button Helsinki btn-choose" @click="choose_city('Helsinki')">Helsinki</a>
          </p>
          <div v-if="city_has_been_chosen">
            <h2>Killed or seriously injured per million rides</h2>
            <d3app viz_type="per_ride" :chosen_city="chosen_city.name" ></d3app>
            <p v-if="correctGuess"> 
              You selected <span class="city Stuttgart">Stuttgart</span> and you were right. In 2012, <span class="city Stuttgart">88</span> cyclists were killed or severely injured in the city. Considering that there were about <span class="city Stuttgart">27,800</span> daily rides, this adds up to <span class="city Stuttgart">8.8</span> fatalities for every million rides, a much higher number than in Paris or Barcelona.
            </p>
            <p v-else> 
              You selected <span :class="chosen_city.name" class="city">{{chosen_city.name}}</span>, but the most dangerous city was <span class="city Stuttgart">Stuttgart</span>. In {{ chosen_city.name }}, <span :class="chosen_city.name" class="city">{{ chosen_city.severe_injuries + chosen_city.deaths }}</span> cyclists were killed or severely injured in {{ chosen_city.year }}, against <span class="city Stuttgart">88</span> in Stuttgart (in 2012). There were about <span :class="chosen_city.name" class="city">{{ chosen_city.daily_rides.toLocaleString('en') }}</span> daily bicyle rides in {{ chosen_city.name }} against <span class="city Stuttgart">27,800</span> in Stuttgart. This adds up to <span :class="chosen_city.name" class="city">{{ chosen_city.ksi_per_m }}</span> fatalities per million rides in {{ chosen_city.name }} against <span class="city Stuttgart">8.8</span> in Stuttgart.
            </p>
            <p>
              When light injuries are taken into account, <span class="city Barcelona">Barcelona</span> becomes more dangerous, with <span class="city Barcelona">57</span> light injuries per million bicycle rides against <span class="city Berlin">39</span> for Berlin. <span class="city Paris">Paris</span> has <span class="city Paris">27</span> light injuries per million rides and <span class="city Helsinki">Helsinki</span> only <span class="city Helsinki">2</span>. I could not find a reliable figure for light injuries in <span class="city London">London</span> or <span class="city Stuttgart">Stuttgart</span>. 
            </p>
            <p>
               Using the same metrics, less than one in a million car rides in Berlin ended in a car driver or passenger being dead or seriously injured.
            </p>
            <p>
              The reason why Berlin and London are so much more dangerous than Paris or Barcelona is probably due to higher vehicle speeds. Most severe injuries and deaths occur when cars and lorries make a right turn into a cyclist. Higher speeds reduce reaction times and increase the probability of a cyclist's being run over. However, no data on vehicle speed exist that would let me test this hypothesis.
            </p>
            <p>
              Bike lanes appear to have no key influence on the dangers car drivers pose to cyclists. In 2011, <span class="city Barcelona">Barcelona</span> had a paltry <span class="city Barcelona">180 kilometers</span> of dedicated bike lanes <a href="http://appsso.eurostat.ec.europa.eu/nui/show.do?query=BOOKMARK_DS-355369_QID_-60BE9D5F_UID_-3F171EB0&layout=TIME,C,X,0;CITIES,L,Y,0;INDIC_UR,L,Z,0;INDICATORS,C,Z,1;&zSelection=DS-355369INDIC_UR,TT1079V;DS-355369INDICATORS,OBS_FLAG;&rankName1=INDIC-UR_1_2_-1_2&rankName2=INDICATORS_1_2_-1_2&rankName3=TIME_1_0_0_0&rankName4=CITIES_1_2_0_1&sortC=ASC_-1_FIRST&rStp=&cStp=&rDCh=&cDCh=&rDM=true&cDM=true&footnes=false&empty=false&wai=false&time_mode=NONE&time_most_recent=false&lang=EN&cfo=%23%23%23%2C%23%23%23.%23%23%23">compared</a> to <span class="city Berlin">Berlin</span>'s <span class="city Berlin">1,500 km</span>. <span class="city Helsinki">Helsinki</span>, a city one-quarter of the size of Berlin by area, had <span class="city Helsinki">2,200 km</span>.
            </p>
            <p>
              The key to safer cities for cyclists lies in a joint commitment by local authorities, including politicians and police, to enforce current regulations on speed limits and invest in dedicated infrastructure for cyclists. On June 28, Berlin <a href="https://adfc-berlin.de/radverkehr/571-dank-der-fahrrad-community-berlin-bekommt-deutschlands-erstes-mobilitaetsgesetz.html">passed a law</a> containing a series of measures aimed at fostering cycling. Time will tell if it succeeds in bringing down the number of cyclists killed or maimed on our streets. 
            </p>
            <h2>Data quality</h2>
            <p>
              Data on cycling is rife with methodological issues. Cyclists can commute into a city from a neighboring one. They might choose to cycle one day and use public transportation the next, especially if rain is involved. Only one survey, the Urban Audit of Eurostat, offers comparable data on bicycle usage in Europe at the city level. Most member states do not bother to keep it up-to-date, which is why the data for all cities but Helsinki is over five-year-old. However, there is no reason to think that the order of magnitude of the metrics presented above changed recently.
            </p>
            <p>
              Data on injuries also varies by city. The data from Paris, for instance, comes from police reports, while the data from Helsinki comes from hospitals. In case of deaths, hospitals consider that a patient deceased within 30 days of the accident was killed in traffic, whereas police reports only deal with the immediate consequences of a crash. 
            </p>
            <p>
              This does not make Berlin look any better, as Berlin's is police data. For the Berlin police, a serious injury means that the victim spent at least 24 hours in the hospital after the crash. In Paris, a serious injury is any injury that requires hospital treatment. If anything, the numbers above probably underestimate the dangerousness of cycling in Berlin.
            </p>
            <h2>The data</h2>
            <p>I used the share of the population cycling to work as a proxy to obtain the number of daily rides. While not everyone goes to work, children, unemployed and retired people also need to move through the city ; I assumed they did so using means of transportation similar to the workers'. You will find the detailed data and sources on the <a href="https://github.com/n-kb/danger-cyclists">Github repository</a> for this piece.</p>
            <table class="table">
              <tr><td>City</td><td>Year</td><td>Share of the population cycling to work</td><td>Population for given year</td><td>Light injuries</td><td>Severe injuries</td><td>Deaths</td></tr>
              <tr><td>Berlin</td><td>2012</td><td>10.8</td><td>3326002</td><td>5025</td><td>684</td><td>15</td></tr>
              <tr><td>London</td><td>2011</td><td>4.3</td><td>8173941</td><td></td><td>555</td><td>16</td></tr>
              <tr><td>Paris</td><td>2011</td><td>3.12</td><td>2249977</td><td>695</td><td>49</td><td>0</td></tr>
              <tr><td>Helsinki</td><td>2016</td><td>17</td><td>628208</td><td>97</td><td>3</td><td>0</td></tr>
              <tr><td>Barcelona</td><td>2011</td><td>1.72</td><td>1611013</td><td>570</td><td>10</td><td>2</td></tr>
              <tr><td>Hamburg</td><td>2012</td><td>8.7</td><td>1718187</td><td>2000</td><td>200</td><td>4</td></tr>
              <tr><td>Cologne/Lev.</td><td>2012</td><td>11.0</td><td>1173038</td><td>1578</td><td>216</td><td>5</td></tr>
              <tr><td>Stuttgart</td><td>2012</td><td>4.7</td><td>591015</td><td></td><td>10</td><td>2</td></tr>
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
      this.correctGuess = (city == 'Stuttgart') ? true : false;
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

.btn-choose
  margin: .3em

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

.Stuttgart
  border-color: rgba(#3C3865, .7)

.Cologne
  border-color: rgba(#386365, .7)

.Hamburg
  border-color: rgba(#654A47, .7)

.footer
  background-color: #fff

.byline--link
  color: black
</style>
