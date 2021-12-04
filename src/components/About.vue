<template>
  <v-container>
    
    <v-row class="text-center">
      <v-col cols="12">
        <v-img
          :src="require('../assets/logo.svg')"
          class="my-3"
          contain
          height="200"
        />
      </v-col>

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
            <songs :songs="totalSongs"/>
          </v-col>
          <v-list v-else max-width="480px">
            <v-list-item right>
              <v-list-item-text>
              Go to <b><a class="black--text" href="https://takeout.google.com">takeout.google.com</a></b>
              </v-list-item-text>
            </v-list-item>
            <v-list-item right>
              <v-list-item-text>
              Select <b>YouTube</b>
              </v-list-item-text>
            </v-list-item>
            <v-list-item right>
              <v-list-item-text>
              Select <b>History</b> in the items to export
              </v-list-item-text>
            </v-list-item>
            <v-list-item right>
              <v-list-item-text>
              Select <b>JSON</b> as the format to export
              </v-list-item-text>
            </v-list-item>
            <v-list-item right>
              <v-list-item-text>
              <b>Wait</b> about 10 minutes for the export to complete.
              </v-list-item-text>
            </v-list-item>
            <v-list-item right>
              <v-list-item-text>
              Download and <b>extract</b> the zip file
              </v-list-item-text>
            </v-list-item>
            <v-list-item right>
              <v-list-item-text>
              Go to <b>Takeout > Youtube and YouTube Music > history</b> and import <b>watch-history.json</b>
              </v-list-item-text>
            </v-list-item>
          </v-list>
        </v-row>
        <v-row justify="center">
          <v-alert v-if="loading" color="primary">
            Loading may take a while
          </v-alert>
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
        history: [], 
        loading: false,
        error: null
    }),
    computed: {
        songs() {
            return _.filter(this.history, e => e.header === "YouTube Music");
        }, 
        countedSongs() {
            return _.countBy(this.songs, 'titleUrl');
        }, 
        keyedSongs() {
            return _.keyBy(this.songs, 'titleUrl');
        }, 
        totalSongs() {
            let s = [];
            Object.keys(this.countedSongs).forEach(key => {
                s.push({...this.keyedSongs[key], count: this.countedSongs[key], name: this.keyedSongs[key].title})
            })
            s.sort((a,b) => a.count < b.count ? 1 : a.count > b.count ? -1 : 0);
            return s;
        }
    }, 
    methods: {
        parseFile(e) {
           this.error = null;
            console.log(e.target.files);
            const f = new FileReader();
            this.loading = true;
            f.readAsText(e.target.files[0]);
            f.onload = (ev) => {
              try {
                let history = JSON.parse(ev.target.result);
                history.filter(e => {
                  return e.header === "Youtube Music" && (new Date(e.time)).getFullYear() === 2021;
                });
                this.history = history;
              } catch (e) {
                this.error = "Could not parse file";
              } finally {
                this.loading = false;
              }
            }
            f.onprogress = function(event) {
                console.log(event);
            }
        }
    }
  }
</script>
