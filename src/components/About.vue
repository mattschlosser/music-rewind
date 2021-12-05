<template>
  <v-container>
    
    <v-row class="text-center">

      <v-col class="mb-4">
        <h1 class="display-2 font-weight-bold mb-3">
          Music Rewind
        </h1>

        <p class="subheading font-weight-regular">
          Ever wanted to see what your most listened to song was over the past year, 
          but the music service you used didn't provide any fancy interface? This
          service is for you. 
        </p>
      </v-col>


      <v-col class="mb-4">
        <h2 class="display-2 font-weight-bold mb-3">
          Getting Started
        </h2>

        <p class="subheading font-weight-regular">
          To begin, you'll first need to get your Google Takeout data, to get a formatted listened
          of your music history. Once you have obtained that, upload it below.
        </p>
      </v-col>

      <v-col
        class="mb-5"
        cols="12"
      >
        <h2 class="headline font-weight-bold mb-3">
          What's next?
        </h2>

        <v-row justify="center" >
          <v-col cols="12" v-if="songs.length">
            <v-select
              v-model="currentYear"
              :items="years"
              label="Standard"
            ></v-select>
            <songs :songs="currentYearSongs"/>
          </v-col>
          <v-list v-else max-width="480px">
            <v-list-item right>
              <div>
              Go to <b><a class="black--text" href="https://takeout.google.com">takeout.google.com</a></b>
              </div>
            </v-list-item>
            <v-list-item right>
              <div>
              Select <b>YouTube</b>
              </div>
            </v-list-item>
            <v-list-item right>
              <div>
              Select <b>History</b> in the items to export
              </div>
            </v-list-item>
            <v-list-item right>
              <div>
              Select <b>JSON</b> as the format to export
              </div>
            </v-list-item>
            <v-list-item right>
              <div>
              <b>Wait</b> about 10 minutes for the export to complete.
              </div>
            </v-list-item>
            <v-list-item right>
              <div>
              Download and <b>extract</b> the zip file
              </div>
            </v-list-item>
            <v-list-item right>
              <div>
              Go to <b>Takeout > Youtube and YouTube Music > history</b> and import <b>watch-history.json</b>
              </div>
            </v-list-item>
          </v-list>
        </v-row>
        <v-row justify="center">
          <v-col cols="12">
            <v-btn
              color="primary"
              dark
              large
              href="https://takeout.google.com"
              target="_blank"
            >
              Go to Takeout
            </v-btn>
          </v-col>
          <v-col cols="12">
            <v-btn
              color="primary"
              dark
              large
              href="https://www.youtube.com/music"
              target="_blank"
            >
              Go to YouTube Music
            </v-btn>
          </v-col>
          <v-col cols="12">
            <v-alert v-if="loading" color="primary">
              Loading may take a while
            </v-alert>
          </v-col>
          <v-alert v-if="error" color="error">
            {{error}}
          </v-alert>
          <v-alert prominent class="warning mt-5">
            No files are uploaded to any server. Everything is processed locally for your privacy.  
          </v-alert>
          <input type="file" ref="file" @change="parseFile" style="display: none"/>
          <v-btn block depressed primary class="mt-5" @click="$refs.file.click()">
            import your history
          </v-btn>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import _ from 'lodash';
import Songs from './Songs';
  export default {
    name: 'HelloWorld',
    components: {
        Songs
    }, 
    data: () => ({
        songs: [], 
        loading: false,
        countedSongs: {},  
        keyedSongs: {},  
        error: null, 
        currentYear: null, 
        totalSongs: [], 
        years: []
    }),
    computed: {
      currentYearSongs() {
        return this.totalSongs.filter(song => song.year == this.currentYear || song.year === null);
      }
    }, 
    methods: {
        computeTotalSongs() {
            let s = [];
            Object.keys(this.countedSongs).forEach(key => {
                let year = (new Date(this.keyedSongs[key].time)).getFullYear();
                s.push({...this.keyedSongs[key], count: this.countedSongs[key], year, name: this.keyedSongs[key].title.replace(/^Watched /, '')})
            })
            s.sort((a,b) => a.count < b.count ? 1 : a.count > b.count ? -1 : 0);
            return s;
        }, 
        parseFile(e) {
          this.error = null;
          const f = new FileReader();
          this.loading = true;
          f.readAsText(e.target.files[0]);
          f.onload = ev => {
            try {
              let history = JSON.parse(ev.target.result);
              history = history.filter(e => {
                return e.header === "YouTube Music";
              });
              this.songs = history;
              this.years = _.uniq(history.map(e => (new Date(e.time).getFullYear().toString())));
              this.countedSongs = _.countBy(this.songs, 'titleUrl');
              this.keyedSongs = _.keyBy(this.songs, 'titleUrl');
              this.totalSongs = this.computeTotalSongs();
            } catch (e) {
              this.error = "Could not parse file";
            } finally {
              this.loading = false;
            }
          }
        }
    }
  }
</script>
